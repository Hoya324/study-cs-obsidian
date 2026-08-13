---
type: map
domains: [java-jvm]
status: seed
level: 0
confidence: low
prerequisites: [프로세스와 스레드]
related_projects: [RAG 챗봇]
related_sources: [주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# 02 Java와 JVM

> [!question] 핵심 질문
> Java 요청 처리는 JVM 안에서 어떤 메모리와 스레드를 사용하며, GC·락·할당은 지연시간에 어떤 영향을 주는가?

## 요청 흐름 속 위치

운영체제 위에서 Tomcat과 Spring을 실행하는 프로세스이며 애플리케이션의 CPU·메모리 행동을 결정한다.

## 가지

- [[JVM 메모리와 GC]]
- [[Java 스레드와 동시성]]
- [[객체 메모리와 컬렉션]]
- [[JVM 관측과 튜닝]]
- 클래스 로딩과 바이트코드
- 예외, 스택과 호출 비용
- Virtual Thread와 플랫폼 스레드

## 연결

- 선수: [[01 컴퓨터 구조와 운영체제]]
- 다음: [[03 네트워크 HTTP 웹 서버와 스레드 풀]], [[04 Spring 내부 원리와 애플리케이션 설계]]
- 프로젝트: [[RAG 챗봇]], [[Excel Export]]

## 아직 답하지 못한 질문

- 메모리 누수와 높은 할당률은 GC 지표에서 어떻게 다르게 보이는가?
- Virtual Thread가 DB 커넥션 부족까지 해결해 주지 않는 이유는 무엇인가?
