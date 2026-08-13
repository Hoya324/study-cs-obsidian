---
type: concept
domains:
  - computer-os
  - performance-resources
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 쿠폰 Batch
related_sources:
  - 주니어 백엔드 개발자가 반드시 알아야 할 실무 지식
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# CPU 메모리 디스크 네트워크 IO

## 한 문장 정의

백엔드 요청은 계산을 위해 CPU, 작업 상태를 위해 메모리, 영속화와 전송을 위해 디스크·네트워크 I/O를 사용한다.

## 요청 흐름 속 위치

요청의 모든 계산·할당·쿼리·외부 호출에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

CPU cycle, heap와 page cache, disk IOPS·bandwidth, network bandwidth·socket를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

각 자원의 포화는 높은 사용률뿐 아니라 run queue, swap, I/O wait, packet loss와 queue 증가로 나타난다.

## 측정 지표와 확인 방법

CPU utilization·load, RSS·heap·GC, IOPS·latency, throughput·retransmission를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

자원별 증거로 병목을 구분한 뒤 계산 감소, 캐시, batching, I/O 병렬성 또는 용량 확장을 선택한다.

## 연결된 개념

- [[요청이 소비하는 리소스]]
- [[부하와 병목]]
- [[관측 가능성]]

## 내 프로젝트 사례

[[쿠폰 Batch]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- CPU 30%인데 p99가 나쁘다면 어떤 대기부터 확인할 것인가?

## 학습 자료

- [[주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
