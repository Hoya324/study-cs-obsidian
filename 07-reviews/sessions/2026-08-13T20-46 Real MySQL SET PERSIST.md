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

level 1 → 2. runtime `SET`의 수명과 `SET PERSIST → mysqld-auto.cnf → restart 적용` 상태 전이를 자신의 말로 설명했다. 동일 변수가 여러 설정 출처에 있을 때의 precedence는 아직 설명하지 않았다.

## 미해결 질문

- `my.cnf`와 persisted setting 충돌 시 최종값은 무엇인가?
- `RESET PERSIST` 후 현재 runtime 값까지 원복하려면 무엇을 해야 하는가?
- Docker image upgrade 시 container 내부 config와 persisted data volume은 어떻게 함께 이동하는가?

## 다음 세션 첫 질문

`my.cnf`와 `mysqld-auto.cnf`에 같은 시스템 변수가 서로 다른 값으로 설정돼 있으면 재시작 후 어떤 값이 적용되나요?
