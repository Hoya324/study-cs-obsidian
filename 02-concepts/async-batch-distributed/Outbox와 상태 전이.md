---
type: concept
domains:
  - async-batch-distributed
  - concurrency-consistency
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - Excel Export
related_sources:
  - 데이터 중심 애플리케이션 설계
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Outbox와 상태 전이

## 한 문장 정의

Outbox는 업무 상태와 발행할 이벤트를 같은 DB transaction에 기록하고 별도 전달자가 broker로 전송하는 패턴이다.

## 요청 흐름 속 위치

DB commit과 message publish 사이의 원자성 빈틈에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

outbox table/log, publisher, retry, cleanup storage를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

발행 지연·중복과 outbox 적체는 후속 작업 지연을 만들지만 DB commit 후 메시지 유실을 줄인다.

## 측정 지표와 확인 방법

unpublished count/age, publish retry, duplicate consume, cleanup lag를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

유일 event id, 멱등 consumer, polling/CDC, 보존·대사 정책을 함께 설계한다.

## 연결된 개념

- [[트랜잭션 경계와 전파]]
- [[비동기 메시징과 전달 보장]]
- [[멱등성과 중복 처리]]

## 내 프로젝트 사례

[[Excel Export]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- PENDING commit 후 SQS 발행 실패를 현재 구조는 어떻게 복구하는가?

## 학습 자료

- [[데이터 중심 애플리케이션 설계]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
