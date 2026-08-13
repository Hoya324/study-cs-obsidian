---
type: project
domains:
  - spring
  - persistence-data
  - performance-resources
  - async-batch-distributed
status: seed
level: 0
confidence: low
prerequisites: []
related_projects: []
related_sources:
  - 죽음의 Spring Batch
  - Real MySQL 8.0 1
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# 쿠폰 Batch

## 문제와 제약

대량 대상에 쿠폰을 발급하면서 메모리 상한, 조회 성능, 실패 재시작, 정책 변경 이력과 알림이 필요했다.

## 선택한 구조

- Spring Batch·Kotlin·MySQL·QueryDSL
- no-offset keyset reader와 `batchUpdate`
- Chunk 1,000, retry·skip 정책
- Batch metadata versioning, 정책 snapshot과 audit
- `afterChunk` 뒤 비동기 알림

## 관련 지식 노드

- [[Batch Chunk와 재시작]]
- [[키셋 페이징]]
- [[인덱스와 실행 계획]]
- [[트랜잭션 경계와 전파]]
- [[객체 메모리와 컬렉션]]
- [[관측 가능성]]

## 보장한다고 말할 수 있는 범위

Chunk transaction과 metadata를 이용해 실패 단위를 제한하고 재시작 가능한 구조를 만들었다. retry·skip 설정만으로 writer의 모든 side effect가 자동 멱등해지는 것은 아니다.

## 실패 시나리오

- commit 전후 writer 실패와 재실행
- 정렬 key가 같거나 실행 중 대상 row가 변경됨
- skip이 업무상 허용되지 않는 오류까지 숨김
- 알림 실패가 본 처리 성공 여부와 섞임

## 관측과 복구

read/write/skip/retry count, Chunk duration, rollback, 마지막 key, 정책 version과 실패 원인을 함께 기록한다.

## 대안과 선택하지 않은 이유

offset paging, cursor, partitioning, queue fan-out을 workload와 DB 부하에 따라 비교할 수 있다.

## 수치와 검증 방법

백만 건을 약 66분 17초에 처리한 기록이 있다. 데이터 분포, DB 사양, index, 동시 부하, Chunk 크기와 측정 범위를 함께 보존해야 비교 가능하다.

## 예상 면접 질문

- Chunk 1,000의 근거와 100·10,000일 때의 trade-off는?
- keyset 정렬 key가 유일하지 않으면 어떤 일이 생기는가?
- `afterChunk` 알림 실패가 job 상태를 바꾸는가?
