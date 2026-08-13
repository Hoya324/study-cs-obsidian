---
type: session
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
# 2026-08-13 20:46 Real MySQL SET PERSIST 학습

## 시작 위치와 목표

일반 `SET`, `GLOBAL`, `PERSIST`, `PERSIST_ONLY`의 현재 runtime과 restart 이후 생명주기를 구분하고, 실제 `realmysql`의 `my.cnf` 전체 항목을 인벤토리화한다.

## 다룬 질문

- [[SET PERSIST는 my.cnf를 수정하는가]]

## 사용자 메모 원문

> SET 명령으로 시스템 변수를 바꾸는건 my.cnf 등의 파일에 바로 적용되는게 아님. 기동 중인 MySQL 인스턴스에서만 유효함
> SET PERSIST로 하면 가능 -> mysqld-auto.cnf 파일에 기록해서 MySQL이 다시 시작할 때 적용됨
> GLOBAL 키워드도 존재함
>
> my.cnf에 있는 모든 변수 정리해둘것

## 설정 우선순위 질문 답변 원문

> mysqld-auto.cnf에 있는값으로 변경되지 않을까? set persist 한 값이 거기로 저장된거니까

## 생성하거나 수정한 노트

- [[02-2.4 SET과 영속 시스템 변수]]
- [[MySQL SET PERSIST와 설정 우선순위]]
- [[SET PERSIST는 my.cnf를 수정하는가]]
- [[realmysql my.cnf 설정 인벤토리]]
- [[Real MySQL 8.0 1]]
- [[현재 학습 위치]]

## 확인한 사실

- `SET GLOBAL`은 runtime global 값만 바꾸고 configuration file을 수정하지 않는다.
- `SET PERSIST`는 runtime global 값을 즉시 바꾸고 `mysqld-auto.cnf`에도 기록한다.
- `SET PERSIST_ONLY`는 현재 runtime을 유지하고 다음 startup용 값만 기록한다.
- `mysqld-auto.cnf`는 일반 option file 뒤에 처리되며 직접 편집하지 않는다.
- 현재 `realmysql`은 `/etc/my.cnf`를 사용하고 추가 `.cnf`와 persisted entry는 없다.
- 활성 server option 7개, client option 1개와 모든 주석 예제를 인벤토리에 분리했다.

## level 변경과 근거

level 2 유지. runtime `SET`의 수명과 `SET PERSIST → mysqld-auto.cnf → restart 적용`에 이어, 같은 변수가 충돌하면 persisted 값이 적용된다는 이유를 자신의 말로 설명했다. 실패 증상에서 실제값과 설정 출처를 조회하는 방법은 아직 답하지 않았다.

## 미해결 질문

- `my.cnf`의 값과 실제 runtime 값이 다를 때 어떤 조회로 설정 출처를 찾는가?
- `RESET PERSIST` 후 현재 runtime 값까지 원복하려면 무엇을 해야 하는가?
- Docker image upgrade 시 container 내부 config와 persisted data volume은 어떻게 함께 이동하는가?

## 다음 세션 첫 질문

`my.cnf`에서 `max_connections=200`으로 바꿔 재시작했는데 실제값이 500이라면, 원인을 확인하기 위해 어떤 값과 설정 출처를 어디서 조회하겠어요?
