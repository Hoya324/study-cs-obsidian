---
type: concept
domains:
  - spring
  - network-web
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - Spring 핵심 원리 MVC JPA 강의
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Spring 요청 생명주기

## 한 문장 정의

Spring MVC 요청은 Servlet filter, DispatcherServlet, handler mapping·adapter, interceptor, controller와 응답 변환을 통과한다.

## 요청 흐름 속 위치

Tomcat worker가 HTTP 요청을 애플리케이션 유스케이스로 바꾸는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

worker thread, request/response buffer, bean 호출, serialization CPU를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

필터·인터셉터·예외 처리 경계 오해는 보안 누락, 중복 로깅, 일관되지 않은 오류를 만든다.

## 측정 지표와 확인 방법

단계별 duration, status, exception, trace span를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

횡단 관심사는 책임에 맞는 계층에 배치하고 하나의 trace context로 흐름을 연결한다.

## 연결된 개념

- [[HTTP 요청과 응답]]
- [[DI와 프록시]]
- [[예외 처리와 오류 응답]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- 인증, 트랜잭션, 예외 변환은 요청 흐름 어디에서 동작하는가?

## 학습 자료

- [[Spring 핵심 원리 MVC JPA 강의]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
