---
type: concept
domains:
  - spring
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - RAG 챗봇
related_sources:
  - 토비의 Spring 6
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# DI와 프록시

## 한 문장 정의

DI는 객체 생성과 의존 연결을 외부로 분리하고 Spring 프록시는 호출 경계에 트랜잭션·보안 같은 부가 기능을 적용한다.

## 요청 흐름 속 위치

Controller부터 service·repository bean 호출 사이에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

bean graph, proxy object, method dispatch, thread-local context를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

self-invocation, final/private 경계와 빈 밖 객체는 기대한 advice가 적용되지 않을 수 있다.

## 측정 지표와 확인 방법

bean wiring failure, proxy type, transaction log, test seam를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

오브젝트 역할을 분리하고 프록시 적용 경계를 테스트하며 구현 세부보다 인터페이스와 의존 방향을 관리한다.

## 연결된 개념

- [[Spring 요청 생명주기]]
- [[트랜잭션 경계와 전파]]
- [[테스트 전략]]

## 내 프로젝트 사례

[[RAG 챗봇]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- 같은 객체 내부 메서드 호출에 @Transactional이 적용되지 않는 이유는 무엇인가?

## 학습 자료

- [[토비의 Spring 6]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
