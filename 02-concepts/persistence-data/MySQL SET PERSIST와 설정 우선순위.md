---
type: concept
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
  - 공식 레퍼런스 색인
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL SET PERSIST와 설정 우선순위

## level 근거

level 2 유지. “mysqld-auto.cnf에 있는값으로 변경되지 않을까? set persist 한 값이 거기로 저장된거니까”라고 persisted 값의 저장 위치와 재시작 후 우선 적용을 연결했다. 예상과 실제값이 다를 때 확인할 지표와 설정 출처 조회는 아직 답하지 않았다.

## 한 문장 정의

`SET PERSIST`는 global system variable의 현재 runtime 값과 재시작용 persisted 값을 함께 관리하는 명령이다.

## 요청 흐름 속 위치

평상시 request의 SQL 처리 방식과 server resource 한도를 바꾸고, restart 뒤에도 같은 설정을 재현하는 운영 configuration 경계에 위치한다.

## 왜 필요한가

`SET GLOBAL`만 사용하면 restart 후 값이 사라지고, `my.cnf`만 확인하면 `mysqld-auto.cnf` override를 놓칠 수 있다. 장애 분석과 배포 재현성을 위해 현재값·출처·재시작 후 값을 분리해야 한다.

## 내부 동작

1. `SET GLOBAL`은 현재 global runtime 값만 변경한다.
2. `SET PERSIST`는 현재 global runtime 값을 바꾸고 data directory의 `mysqld-auto.cnf`에 기록한다.
3. `SET PERSIST_ONLY`는 runtime 값은 유지하고 persisted 값만 기록한다.
4. restart 시 server는 일반 option file 뒤에 persisted setting을 처리한다.
5. `RESET PERSIST`는 persisted entry만 제거하고 현재 runtime 값은 바꾸지 않는다.

## 소비하는 리소스

설정 기록 자체의 비용은 작지만 대상 변수에 따라 memory, connection, thread, disk IO와 복구 시간이 크게 달라진다. 변경 전 영향 범위와 rollback 값을 기록해야 한다.

## 실패 조건과 관찰되는 증상

- `my.cnf`와 persisted 값 충돌: 파일의 값과 실제 runtime 값 불일치
- session-only 또는 nonpersistible 변수 사용: `SET PERSIST` 오류
- 권한 부족: `SYSTEM_VARIABLES_ADMIN` 관련 오류
- `mysqld-auto.cnf` 수동 편집 오류: startup parse failure
- persisted entry만 제거: 현재 runtime 값이 계속 유지되어 원복했다고 착각

## 측정 지표와 확인 방법

- `performance_schema.persisted_variables`
- `performance_schema.variables_info`
- `@@GLOBAL`, 실제 application connection의 `@@SESSION`
- startup error log와 configuration change 기록

## 해결 전략과 트레이드오프

- 즉시만 변경: `SET GLOBAL`
- 즉시 변경하고 restart 뒤 유지: `SET PERSIST`
- 다음 restart부터 적용: `SET PERSIST_ONLY`
- infrastructure-as-code와 file 중심 운영에서는 `my.cnf`를 source of truth로 삼고 persisted override 정책을 제한할 수 있다.
- 원격 긴급 조정에는 `SET PERSIST`가 편리하지만 설정 출처가 분산되는 비용이 있다.

## 연결된 개념

- [[MySQL 시스템 변수의 scope와 생명주기]]
- [[MySQL GLOBAL 변수 변경은 기존 세션에 적용되는가]]
- [[장애 복구]]
- [[관측 가능성]]

## 내 프로젝트 사례

현재 Docker `realmysql`에는 `mysqld-auto.cnf`와 persisted entry가 없다. 따라서 현재 explicit startup 설정은 `/etc/my.cnf` 인벤토리를 기준으로 확인할 수 있다.

## 90초 설명

`SET GLOBAL의 runtime 한계 → SET PERSIST의 즉시 변경과 파일 기록 → restart load order → PERSIST_ONLY → RESET PERSIST → 설정 출처 확인` 순서로 설명한다.

## 아직 답하지 못한 질문

- `my.cnf`를 바꿨는데 실제값이 다를 때 현재값과 설정 출처를 어떻게 확인하는가?
- 배포 환경에서 `my.cnf`와 persisted setting 중 무엇을 source of truth로 삼을 것인가?

## 학습 자료

- [[Real MySQL 8.0 1]]의 [[02-2.4 SET과 영속 시스템 변수]]
- [[공식 레퍼런스 색인]]
