---
type: map
domains: [async-batch-distributed]
status: seed
level: 0
confidence: low
prerequisites: [멱등성과 중복 처리, 트랜잭션 경계와 전파]
related_projects: [쿠폰 Batch, Excel Export]
related_sources: [데이터 중심 애플리케이션 설계, 죽음의 Spring Batch]
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# 08 비동기 처리 Batch와 분산 시스템

> [!question] 핵심 질문
> 요청 시간 안에 끝내지 않을 일은 어떻게 분리하고, 메시지 중복·재순서·부분 실패와 재시작을 어떻게 견디는가?

## 요청 흐름 속 위치

HTTP 요청과 실제 작업의 시간 경계를 나누거나, 대량 데이터를 Chunk 단위로 처리한다.

## 가지

- [[비동기 메시징과 전달 보장]]
- [[Batch Chunk와 재시작]]
- [[키셋 페이징]]
- [[Outbox와 상태 전이]]
- [[분산 시스템의 부분 실패]]
- 스케줄링, 파티셔닝과 병렬 처리
- DLQ, retry, skip과 보상

## 연결

- 선수: [[07 동시성 트랜잭션과 데이터 정합성]]
- 다음: [[09 배포 클라우드 관측 장애 대응 보안과 테스트]]
- 프로젝트: [[쿠폰 Batch]], [[Excel Export]]
- 자료: [[죽음의 Spring Batch]], [[데이터 중심 애플리케이션 설계]]

## 아직 답하지 못한 질문

- DB 커밋과 메시지 발행 사이의 빈틈은 어떤 상태를 만드는가?
- Chunk 크기를 늘리면 처리량, 메모리, 락과 재시작 비용은 어떻게 달라지는가?
