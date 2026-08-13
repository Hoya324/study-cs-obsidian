---
type: concept
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 1
confidence: medium
prerequisites:
  - DB 서버와 애플리케이션 서버
related_projects: []
related_sources:
  - Real MySQL 8.0 1
  - 공식 레퍼런스 색인
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL 시스템 변수의 scope와 생명주기

## level 근거

level 0 → 1. “MySQL 서버에 저장된 이런 값들은 시스템 변수”라고 정의했고, GLOBAL·SESSION scope와 dynamic·static 구분을 스스로 열거했다. 이어 “커넥션이 만들어지는 순간부터 해당 커넥션에서만 유효”하다고 SESSION의 수명을 연결했다. 아직 현재 GLOBAL 값이 새 SESSION으로 복사되고 기존 SESSION에는 소급되지 않는 과정을 자기 말로 설명하지 않아 level 1을 유지한다.

## 한 문장 정의

MySQL 시스템 변수는 서버 전체 또는 connection별 동작 방식을 나타내는 현재 설정값이다.

## 요청 흐름 속 위치

GLOBAL 값은 서버 전체의 동작과 새 connection의 초기 상태에 영향을 주고, SESSION 값은 JDBC pool에서 빌린 특정 connection이 SQL 요청을 실행하는 방식을 결정한다.

## 왜 필요한가

GLOBAL 값을 바꿨는데 기존 connection의 동작이 바뀌지 않거나, runtime 변경이 재시작 뒤 사라지는 문제를 구분하려면 scope, dynamic, persistence를 각각 이해해야 한다.

## 내부 동작

1. 서버가 기본값, option file과 command line을 읽어 GLOBAL 값을 초기화한다.
2. 새 connection을 만들 때 대부분의 `Global, Session` 변수는 현재 GLOBAL 값을 SESSION 초기값으로 복사한다.
3. `SET GLOBAL`은 GLOBAL과 미래 connection의 초기값을 바꾼다.
4. `SET SESSION`은 현재 connection 값만 바꾼다.
5. connection이 닫히면 그 SESSION 값도 사라진다.
6. runtime 변경은 별도로 persist하지 않으면 서버 재시작 때 사라진다.

`Scope`, startup 설정 가능 여부, `Dynamic`은 독립 속성이다. 일반적으로 `Both` 변수의 GLOBAL 값이 SESSION 기본값 역할을 하지만, 최신 MySQL 8.0에는 `Scope: Session`이면서 command line·option file로 기본값을 지정할 수 있는 예외도 있다. 따라서 scope만으로 설정 경로를 추론하지 않고 변수 reference의 각 열을 함께 확인한다.

## 소비하는 리소스

시스템 변수 자체의 저장 비용은 작지만 변수 값이 결정하는 buffer 크기, connection 수, optimizer 선택, log와 timeout이 CPU·메모리·disk·동시성 사용량을 크게 바꿀 수 있다.

## 실패 조건과 관찰되는 증상

- GLOBAL만 확인: application의 기존 SESSION과 실제 동작 불일치
- connection pool 재사용: 새 GLOBAL 설정 반영 지연
- static 변수에 runtime `SET`: 변경 거부
- `SET GLOBAL`만 사용: 재시작 뒤 원래 설정으로 복귀
- option 이름 오기: 서버 시작 실패 또는 option 무시
- 과도한 전역 buffer·connection 설정: 메모리 압박과 OOM 위험

## 측정 지표와 확인 방법

- `SHOW GLOBAL VARIABLES`와 `@@GLOBAL.변수명`
- 실제 application connection의 `@@SESSION.변수명`
- 서버 startup option과 읽힌 option file
- Performance Schema의 system variable 관련 table
- error log, connection 수, memory·CPU·IO와 query latency

## 해결 전략과 트레이드오프

- 운영 명령에서는 scope를 항상 명시해 의도를 드러낸다.
- GLOBAL 변경 뒤 기존 connection 처리 방식을 정한다: 유지, 점진적 재생성, pool 재시작.
- 즉시 적용과 재시작 후 유지가 모두 필요하면 `SET PERSIST`의 권한과 변경 이력을 관리한다.
- 설정 변경 전후 application workload와 자원 지표를 비교한다.

## 연결된 개념

- [[JDBC 커넥션 풀]]
- [[DB 서버와 애플리케이션 서버]]
- [[요청이 소비하는 리소스]]
- [[관측 가능성]]
- [[장애 복구]]
- [[InnoDB 버퍼 풀과 MyISAM 키 캐시는 무엇인가]]

## 내 프로젝트 사례

Spring application이 HikariCP connection을 재사용하면 MySQL GLOBAL 변수 변경 직후에도 이미 생성된 connection의 SESSION 값은 유지될 수 있다. 실제 적용 여부는 pool 밖 관리 connection이 아니라 application이 빌린 connection에서 확인해야 한다.

## 90초 설명

`시작 설정 → GLOBAL 값 → 새 connection의 SESSION 복사 → SET GLOBAL과 SET SESSION 차이 → pool 재사용 → dynamic과 persistence 차이` 순서로 설명한다.

## 아직 답하지 못한 질문

- [[MySQL GLOBAL 변수 변경은 기존 세션에 적용되는가]]
- 어떤 SESSION 변수는 connection pool 반환 전에 초기화해야 하는가?

## 학습 자료

- [[Real MySQL 8.0 1]]의 [[02-2.4.2 MySQL 시스템 변수의 특징]]
- [[공식 레퍼런스 색인]]
