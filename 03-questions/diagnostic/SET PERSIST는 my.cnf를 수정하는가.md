---
type: question
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 2
confidence: medium
prerequisites:
  - MySQL 시스템 변수의 scope와 생명주기
related_projects: []
related_sources:
  - Real MySQL 8.0 1
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# SET PERSIST는 my.cnf를 수정하는가

## 질문

`SET GLOBAL`, `SET PERSIST`, `SET PERSIST_ONLY`는 현재 runtime과 설정 파일을 각각 어떻게 바꾸나요?

## 질문이 생긴 맥락

[[02-2.4 SET과 영속 시스템 변수]]에서 runtime 변경과 restart 이후의 설정 보존을 구분했다.

## 최초 답변 원문

> SET 명령으로 시스템 변수를 바꾸는건 my.cnf 등의 파일에 바로 적용되는게 아님. 기동 중인 MySQL 인스턴스에서만 유효함
> SET PERSIST로 하면 가능 -> mysqld-auto.cnf 파일에 기록해서 MySQL이 다시 시작할 때 적용됨
> GLOBAL 키워드도 존재함

## 진단 결과와 부족한 연결

일반 `SET`과 persisted 설정의 수명을 정확히 구분했다. 다만 `SET PERSIST`가 현재 GLOBAL 값도 즉시 바꾼다는 점, `PERSIST_ONLY`, `RESET PERSIST`와 file precedence는 빠져 있었다.

## 설정 우선순위 답변 원문

> mysqld-auto.cnf에 있는값으로 변경되지 않을까? set persist 한 값이 거기로 저장된거니까

## 설정 우선순위 진단

맞다. `SET PERSIST`로 저장한 값이 `mysqld-auto.cnf`에 있고, 서버가 일반 option file 뒤에 persisted variable을 처리한다는 인과를 연결했다.

정확히는 `persisted_globals_load=ON`으로 시작해 persisted 값을 읽는 경우에 해당한다. 이를 `OFF`로 시작하면 `mysqld-auto.cnf`의 persisted 값은 적용되지 않는다.

## 관련 개념과 프로젝트

- [[MySQL SET PERSIST와 설정 우선순위]]
- [[MySQL 시스템 변수의 scope와 생명주기]]
- [[realmysql my.cnf 설정 인벤토리]]

## 필요한 자료와 실험

- 안전한 dynamic variable 하나로 `GLOBAL`, `PERSIST`, `PERSIST_ONLY` 전후 비교
- `persisted_variables`와 `variables_info` 조회
- restart 후 값과 source 비교

## 개선된 답변

`SET GLOBAL`은 현재 server의 global runtime 값만 바꾸고 파일은 수정하지 않는다. `SET PERSIST`는 현재 global runtime 값을 즉시 바꾸면서 `DATADIR/mysqld-auto.cnf`에도 기록한다. `SET PERSIST_ONLY`는 현재값을 바꾸지 않고 다음 시작용 값만 기록한다. 어느 명령도 `my.cnf`를 직접 수정하지 않는다. persisted entry는 `RESET PERSIST`로 제거하며, 제거해도 현재 runtime 값은 자동 원복되지 않는다.

같은 dynamic system variable이 `my.cnf`와 `mysqld-auto.cnf`에 서로 다르게 설정돼 있고 persisted 설정 로딩이 활성화돼 있다면, 나중에 처리되는 `mysqld-auto.cnf`의 값이 최종 runtime 값이 된다.

## 다음 꼬리 질문

`my.cnf`에서 `max_connections=200`으로 바꿔 재시작했는데 실제값이 500이라면, 원인을 확인하기 위해 어떤 값과 설정 출처를 어디서 조회하겠어요?
