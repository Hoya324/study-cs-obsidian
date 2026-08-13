---
type: concept
domains:
  - persistence-data
  - network-web
status: learning
level: 0
confidence: medium
prerequisites:
  - TCP와 연결
related_projects: []
related_sources:
  - Real MySQL 8.0 1
  - 공식 레퍼런스 색인
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL 로컬 연결과 Unix domain socket

## level 근거

아직 level 상승 근거 없음. “`--host` 설정에 따라 Unix domain socket 사용 여부가 나뉜다”는 방향은 포착했지만 정확한 옵션별 연결 방식을 자신의 말로 설명한 기록은 없다.

## 한 문장 정의

Unix 계열의 MySQL 클라이언트는 host 생략·`localhost`일 때 기본적으로 Unix domain socket을 사용하고, loopback IP나 `--protocol=TCP`일 때 TCP/IP를 사용한다.

## 요청 흐름 속 위치

애플리케이션 또는 CLI가 [[DB 서버와 애플리케이션 서버]]의 mysqld에 연결하는 가장 앞단이며, 인증과 SQL 실행보다 먼저 전송 경로를 정한다.

## 왜 필요한가

같은 서버에 연결한다고 생각해도 socket 경로 오류와 TCP 포트 오류는 원인과 해결 방법이 다르다. 연결 테스트가 어떤 경로를 검증했는지 알아야 배포 환경의 장애를 정확히 재현할 수 있다.

## 내부 동작

1. 클라이언트가 `--protocol`의 명시 여부를 확인한다.
2. 명시됐다면 해당 protocol을 사용한다.
3. 그렇지 않고 Unix 계열에서 host가 생략됐거나 `localhost`이면 socket 파일을 사용한다.
4. `127.0.0.1`, `::1` 또는 다른 host이면 TCP/IP를 사용한다.
5. 선택된 경로로 mysqld에 연결한 뒤 인증과 세션 생성이 이어진다.

## 소비하는 리소스

- Unix domain socket: kernel socket buffer, 파일 시스템 namespace의 socket 경로, file descriptor
- TCP/IP: loopback 또는 network interface, TCP socket buffer, IP·TCP 처리, port와 file descriptor

## 실패 조건과 관찰되는 증상

- 잘못된 socket 경로 또는 권한: local socket 연결 오류
- mysqld의 TCP listen 비활성 또는 잘못된 port: connection refused 또는 timeout
- Docker에서 host와 container의 `localhost` 혼동: 다른 network namespace로 연결 시도
- `localhost`에 port만 지정: 예상한 TCP 포트가 사용되지 않음

## 측정 지표와 확인 방법

- mysql `status`의 `Connection` 항목
- `SHOW VARIABLES LIKE 'socket';`
- `SHOW VARIABLES LIKE 'port';`
- 컨테이너 port publishing과 실제 실행 위치

## 해결 전략과 트레이드오프

자동화와 장애 재현에서는 host뿐 아니라 protocol을 명시해 의도를 드러낸다. 같은 호스트에서만 쓰는 관리 명령은 socket이 단순하고, 컨테이너·원격 호스트·일관된 배포 경로는 TCP/IP가 이해하기 쉽다.

## 연결된 개념

- [[TCP와 연결]]
- [[DB 서버와 애플리케이션 서버]]
- [[JDBC 커넥션 풀]]

## 내 프로젝트 사례

현재 Docker `realmysql` 실습에서 컨테이너 내부 CLI는 socket, macOS에서 공개 포트 `127.0.0.1:3306`으로 접근하는 클라이언트는 TCP/IP를 사용한다.

## 90초 설명

`localhost의 특별 처리 → socket과 TCP의 차이 → protocol override → port 무시 함정 → Docker network namespace` 순서로 설명한다.

## 아직 답하지 못한 질문

- [[MySQL localhost 연결은 어떤 전송 방식을 사용하는가]]

## 학습 자료

- [[Real MySQL 8.0 1]]의 [[02-2.2.3 서버 연결 테스트]]
- [[공식 레퍼런스 색인]]
