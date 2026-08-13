---
type: question
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
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL localhost 연결은 어떤 전송 방식을 사용하는가

## 질문

MySQL 로컬 연결은 `--host`와 `--protocol` 설정에 따라 어떤 전송 방식을 사용하나요?

## 질문이 생긴 맥락

[[02-2.2.3 서버 연결 테스트]]에서 로컬 MySQL 연결도 설정에 따라 Unix domain socket 또는 TCP/IP를 사용한다는 내용을 읽었다.

## 최초 답변 원문

> 로컬 연결에서 --host 세팅에 따라 Unix domain socket을 이용하는 여부가 나뉨 (따로 너가 정리)

## 진단 결과와 부족한 연결

host 설정이 전송 방식 선택에 영향을 준다는 핵심 방향은 맞다. 아직 host 생략, `localhost`, `127.0.0.1`, `--protocol=TCP`의 정확한 매핑과 port 무시 조건은 설명하지 않았다.

## 관련 개념과 프로젝트

- [[MySQL 로컬 연결과 Unix domain socket]]
- [[TCP와 연결]]
- [[DB 서버와 애플리케이션 서버]]

## 필요한 자료와 실험

- [[02-2.2.3 서버 연결 테스트]]의 공식 문서·Docker 실험 결과
- mysql `status`의 `Connection` 항목 비교

## 개선된 답변

Unix 계열에서 host를 생략하거나 `localhost`를 지정하면 기본적으로 Unix domain socket을 사용한다. `127.0.0.1`, `::1` 또는 다른 host는 TCP/IP를 사용한다. `--protocol=TCP`나 `SOCKET`을 명시하면 host의 기본 선택보다 우선한다.

## 다음 꼬리 질문

Unix 계열에서 `mysql -h localhost -P 3307`을 실행하면 왜 3307이 무시될 수 있을까요?
