---
type: concept
domains:
  - async-batch-distributed
  - persistence-data
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 쿠폰 Batch
related_sources:
  - 죽음의 Spring Batch
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Batch Chunk와 재시작

## 한 문장 정의

Spring Batch Chunk는 일정 건수를 읽고 변환·쓰기한 뒤 transaction 단위로 commit하며 메타데이터로 재시작 지점을 관리한다.

## 요청 흐름 속 위치

대량 데이터를 온라인 요청과 분리해 반복 처리하는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

heap, DB connection, transaction/log, reader state, job metadata를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

큰 chunk는 메모리·락·rollback 비용을 키우고 작은 chunk는 commit overhead를 늘린다.

## 측정 지표와 확인 방법

items/sec, chunk duration, commit/rollback, skip/retry, memory를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

업무 원자성과 비용을 기준으로 chunk를 정하고 restart 가능 reader·writer와 실패 정책을 검증한다.

## 연결된 개념

- [[키셋 페이징]]
- [[트랜잭션 경계와 전파]]
- [[멱등성과 중복 처리]]

## 내 프로젝트 사례

[[쿠폰 Batch]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- chunk 1000을 선택했다면 어떤 수치와 실패 비용으로 설명할 것인가?

## 학습 자료

- [[죽음의 Spring Batch]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
