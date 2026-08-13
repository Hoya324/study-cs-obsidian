---
type: question
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
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL GLOBAL 변수 변경은 기존 세션에 적용되는가

## 질문

`SET GLOBAL autocommit = OFF`를 실행해도 이미 연결된 session의 `autocommit` 값이 바뀌지 않는 이유는 무엇인가요?

## 질문이 생긴 맥락

[[02-2.4.2 MySQL 시스템 변수의 특징]]에서 각 system variable의 `Var Scope`가 Global, Session 또는 둘 다일 수 있다는 내용을 읽었다.

## 최초 답변 원문

> 각 변수가 글로별 변수인지, 세션 변수인지 알아야함.

## 진단 결과와 부족한 연결

GLOBAL과 SESSION을 구분해야 한다는 핵심은 맞다. 아직 SESSION 값이 connection 생성 시점의 GLOBAL 값에서 초기화되고, JDBC connection pool이 그 session을 재사용한다는 시간적 연결은 설명하지 않았다.

## 관련 개념과 프로젝트

- [[MySQL 시스템 변수의 scope와 생명주기]]
- [[JDBC 커넥션 풀]]
- [[DB 서버와 애플리케이션 서버]]

## 필요한 자료와 실험

- 두 mysql connection에서 `@@GLOBAL.autocommit`, `@@SESSION.autocommit` 비교
- GLOBAL 변경 전 connection과 변경 후 새 connection 비교
- HikariCP connection 재사용 시 SESSION 값 확인

## 개선된 답변

대부분의 `Global, Session` 변수는 connection을 만들 때 그 시점의 GLOBAL 값을 SESSION 초기값으로 복사한다. `SET GLOBAL`은 서버의 GLOBAL 값과 앞으로 생성될 connection의 초기값을 변경할 뿐, 이미 독립적인 SESSION 값을 가진 기존 connection에는 소급 적용되지 않는다.

## 다음 꼬리 질문

`SET GLOBAL autocommit = OFF`를 실행해도 이미 연결된 세션의 `autocommit` 값이 바뀌지 않는 이유는 무엇인가요?
