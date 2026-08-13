---
type: project
domains:
  - spring
  - persistence-data
  - concurrency-consistency
  - async-batch-distributed
  - infrastructure-operations
status: seed
level: 0
confidence: low
prerequisites: []
related_projects: []
related_sources:
  - 죽음의 Spring Batch
  - 데이터 중심 애플리케이션 설계
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Excel Export

## 문제와 제약

큰 Excel 파일 생성이 HTTP timeout과 메모리 문제를 만들 수 있어 요청과 작업 시간을 분리하고 중복 메시지·재처리·파일 보관을 다뤄야 했다.

## 선택한 구조

- Spring Boot·Spring Batch·MySQL·SQS·S3·Redis
- `PENDING` commit 뒤 SQS 발행
- consumer가 `PROCESSING` 상태를 원자적으로 선점하고 중복은 skip
- 상태 변경은 `REQUIRES_NEW`로 별도 기록
- keyset 1,000과 SXSSF window 200으로 메모리 상한 관리
- S3 저장, Redis 상태 조회, DLQ 운영

## 관련 지식 노드

- [[비동기 메시징과 전달 보장]]
- [[멱등성과 중복 처리]]
- [[Outbox와 상태 전이]]
- [[분산 시스템의 부분 실패]]
- [[Batch Chunk와 재시작]]
- [[JVM 메모리와 GC]]
- [[장애 복구]]

## 보장한다고 말할 수 있는 범위

상태 선점으로 동시에 같은 작업을 처리하는 중복을 줄였고 streaming writer로 메모리 상한을 제한했다. DB commit과 SQS publish, S3 upload는 하나의 원자 transaction이 아니다.

## 실패 시나리오

- PENDING commit 후 SQS publish 실패
- PROCESSING 선점 뒤 worker 종료
- S3 upload 성공 뒤 완료 상태 기록 실패
- visibility timeout보다 작업이 길어 메시지 재전달
- DLQ 재처리로 기존 파일과 충돌

## 관측과 복구

상태별 체류 시간, SQS age·receive count, DLQ, export row count, heap, S3 object와 DB 상태 불일치를 대사한다.

## 대안과 선택하지 않은 이유

Outbox/CDC, Step Function, direct polling worker, multipart output을 보장·운영 복잡도·비용으로 비교할 수 있다.

## 수치와 검증 방법

keyset 1,000과 SXSSF window 200은 memory와 I/O trade-off를 실험 조건과 함께 다시 검증한다.

## 예상 면접 질문

- `REQUIRES_NEW` 상태 기록이 본 작업 rollback과 어떤 불일치를 만들 수 있는가?
- 오래된 PROCESSING을 누가 언제 다시 살리는가?
- Exactly-once가 아니라면 사용자에게 어떤 결과를 보장하는가?
