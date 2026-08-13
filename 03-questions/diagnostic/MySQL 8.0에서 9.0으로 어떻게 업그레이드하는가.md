---
type: question
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 0
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
# MySQL 8.0에서 9.0으로 어떻게 업그레이드하는가

## 질문

MySQL 8.0에서 9.0으로 갈 때 지원되는 경로와 인플레이스·논리적 업그레이드의 실행 및 복구 차이는 무엇인가요?

## 질문이 생긴 맥락

[[02-2.3 MySQL 서버 업그레이드]]에서 중간 version과 GA release를 건너뛸 수 없다는 제약을 읽고, 현재 release model에서의 실제 경로를 확인했다.

## 최초 답변 원문

> 인플레이스 업그레이드 시 메이저 버전은 두 단계 건너뛴 업그레이드가 불가능, GA 버전이 아닌 경우 예를 들어 5.7.8에서 8.0으로 바로 업그레이드 불가능

## 진단 결과와 부족한 연결

중간 release series를 건너뛸 수 없고 non-GA 출발점은 지원되지 않는다는 방향은 맞다. 아직 8.4 LTS가 필요한 이유, 각 단계의 사전 검사와 backup, 기존 data directory를 바꾸는 인플레이스와 새 서버에 재생성하는 논리적 방식의 차이를 설명하지 않았다.

## 관련 개념과 프로젝트

- [[MySQL 인플레이스 업그레이드와 논리적 업그레이드]]
- [[장애 복구]]
- [[관측 가능성]]
- [[컨테이너와 이미지]]

## 필요한 자료와 실험

- [[02-2.3 MySQL 서버 업그레이드]]의 공식 지원 경로와 절차
- Docker data volume 복사본을 이용한 `8.0 → 8.4` 리허설
- Upgrade Checker 결과와 restore 성공 증거

## 개선된 답변

직접 8.0에서 9.0으로 가지 않고 `8.0 → 8.4 LTS → 9.0 Innovation`으로 두 번 업그레이드한다. 각 단계에서 Upgrade Checker, 변경점 검토, 복구 가능한 backup, test workload를 반복한다. 인플레이스는 새 바이너리가 기존 data directory와 data dictionary를 올리므로 빠르지만 복귀는 backup·snapshot 복원이 중심이다. 논리적 방식은 구버전 SQL dump를 새 버전의 새 data directory에 적재하므로 추가 시간과 공간이 들지만 구·신 서버 병렬 검증과 cutover가 쉽다.

## 다음 꼬리 질문

인플레이스 업그레이드가 기존 데이터 디렉터리를 직접 바꾸기 때문에 롤백 전략이 백업 복원 중심이 되는 이유는 무엇인가요?
