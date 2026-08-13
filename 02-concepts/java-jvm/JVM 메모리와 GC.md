---
type: concept
domains:
  - java-jvm
  - performance-resources
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - Excel Export
related_sources:
  - 주니어 백엔드 개발자가 반드시 알아야 할 실무 지식
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# JVM 메모리와 GC

## 한 문장 정의

JVM은 heap·stack·metaspace 등으로 실행 메모리를 관리하고 GC는 도달 불가능한 heap 객체를 회수한다.

## 요청 흐름 속 위치

요청 객체 생성부터 장수 객체 유지와 정리까지에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

heap, thread stack, metaspace, CPU와 stop-the-world 시간를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

할당률 증가, 메모리 누수, 부적절한 heap은 잦은 GC·긴 pause·OOM으로 나타난다.

## 측정 지표와 확인 방법

heap occupancy, allocation rate, GC frequency·pause, promotion, OOM 로그를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

먼저 객체 수명과 할당 원인을 확인하고 heap·collector 조정은 부하와 pause 목표를 함께 검증한다.

## 연결된 개념

- [[객체 메모리와 컬렉션]]
- [[JVM 관측과 튜닝]]
- [[요청이 소비하는 리소스]]

## 내 프로젝트 사례

[[Excel Export]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- 메모리 누수와 단순 높은 할당률을 어떤 지표로 구분하는가?

## 학습 자료

- [[주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
