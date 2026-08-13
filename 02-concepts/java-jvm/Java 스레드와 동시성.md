---
type: concept
domains:
  - java-jvm
  - concurrency-consistency
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - 면접을 위한 CS 전공지식 노트
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Java 스레드와 동시성

## 한 문장 정의

Java 스레드는 JVM의 실행 흐름이며 공유 객체에 동시에 접근할 때 가시성·원자성·순서 문제를 다뤄야 한다.

## 요청 흐름 속 위치

Tomcat, Batch, 비동기 executor가 애플리케이션 코드를 병렬 실행하는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

플랫폼 스레드·stack, scheduler, lock, shared heap를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

race condition, deadlock, thread starvation은 데이터 오류나 요청 정지로 보인다.

## 측정 지표와 확인 방법

thread dump, blocked/waiting 수, lock contention, executor queue를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

불변성과 소유권을 우선하고 필요한 경계에서 최소한의 동기화와 제한된 executor를 사용한다.

## 연결된 개념

- [[프로세스와 스레드]]
- [[동시성 경쟁 조건]]
- [[분산 락과 CAS]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- 메모리 안의 동시성 제어와 여러 서버 사이 제어는 무엇이 다른가?

## 학습 자료

- [[면접을 위한 CS 전공지식 노트]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
