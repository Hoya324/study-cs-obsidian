---
type: concept
domains:
  - network-web
  - performance-resources
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - RAG 챗봇
related_sources:
  - 주니어 백엔드 개발자가 반드시 알아야 할 실무 지식
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Tomcat 스레드 풀

## 한 문장 정의

Tomcat 스레드 풀은 수신된 HTTP 요청의 애플리케이션 코드를 동시에 실행할 worker 수와 대기열을 제한한다.

## 요청 흐름 속 위치

연결이 해석된 뒤 Spring 요청 처리를 시작하고 응답을 쓸 때까지에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

platform thread·stack, CPU, executor queue, request context를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

worker 포화는 queue 증가, 긴 응답, timeout과 거부로 보이며 DB 대기가 원인일 수도 있다.

## 측정 지표와 확인 방법

busy/max threads, queue length, request duration, reject·timeout, thread state를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

풀만 키우지 말고 블로킹 시간과 하위 풀을 측정하며 입구 제한·timeout·격리·비동기를 선택한다.

## 연결된 개념

- [[프로세스와 스레드]]
- [[JDBC 커넥션 풀]]
- [[큐잉과 백프레셔]]

## 내 프로젝트 사례

[[RAG 챗봇]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- Tomcat worker 200개와 DB connection 20개의 관계를 어떻게 설명할 것인가?

## 학습 자료

- [[주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
