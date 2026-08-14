---
type: map
domains: [spring]
status: learning
level: 1
confidence: medium
prerequisites: [HTTP 요청과 응답, Java 스레드와 동시성]
related_projects: [출석 보상 게임, 쿠폰 Batch, Excel Export, RAG 챗봇]
related_sources: [Spring 핵심 원리 기본편, 토비의 Spring 6]
last_reviewed: 2026-08-14
next_review: 2026-08-17
created: 2026-08-13
updated: 2026-08-14
---
# 04 Spring 내부 원리와 애플리케이션 설계

> [!question] 핵심 질문
> Tomcat이 넘긴 요청은 Spring의 어떤 계층과 프록시를 지나며, 트랜잭션·예외·검증 경계는 어디에 놓여야 하는가?

## 요청 흐름 속 위치

HTTP를 애플리케이션 유스케이스로 바꾸고 DB·Redis·외부 시스템 호출을 조정한다.

## 가지

- [[Spring 요청 생명주기]]
- [[DI와 프록시]]
- [[트랜잭션 경계와 전파]]
- [[예외 처리와 오류 응답]]
- [[테스트 전략]]
- MVC 필터·인터셉터·ArgumentResolver
- [[1.1 객체 지향 설계와 스프링]] — 역할과 구현, 다형성, Spring DI의 출발점

## 연결

- 선수: [[02 Java와 JVM]], [[03 네트워크 HTTP 웹 서버와 스레드 풀]]
- 다음: [[05 JDBC JPA RDBMS와 MySQL]], [[07 동시성 트랜잭션과 데이터 정합성]]
- 자료: [[Spring 핵심 원리 MVC JPA 강의]], [[토비의 Spring 6]]

## 아직 답하지 못한 질문

- `@Transactional`이 붙었는데 트랜잭션이 적용되지 않는 경계는 어디인가?
- 외부 API 호출을 DB 트랜잭션 안에 둘 때 어떤 자원이 오래 점유되는가?
