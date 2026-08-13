---
type: map
domains: [network-web]
status: learning
level: 0
confidence: low
prerequisites: [서버란 무엇인가, 프로세스와 스레드]
related_projects: [출석 보상 게임, Excel Export]
related_sources: [주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]
last_reviewed:
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# 03 네트워크 HTTP 웹 서버와 스레드 풀

> [!question] 핵심 질문
> 클라이언트 요청은 어떻게 연결되고, Tomcat은 어떤 요청에 스레드를 배정하며, 포화 시 어디에서 기다리게 하는가?

## 요청 흐름 속 위치

클라이언트와 Spring 애플리케이션 사이의 입구다. 연결, 파싱, 큐잉, 타임아웃이 처음 나타난다.

## 가지

- [[TCP와 연결]]
- [[HTTP 요청과 응답]]
- [[웹 서버와 WAS]]
- [[Tomcat 스레드 풀]]
- [[타임아웃과 재시도]]
- DNS, TLS와 프록시
- keep-alive와 커넥션 관리

## 연결

- 선수: [[01 컴퓨터 구조와 운영체제]], [[02 Java와 JVM]]
- 다음: [[04 Spring 내부 원리와 애플리케이션 설계]], [[06 리소스 성능과 부하]]
- 프로젝트: [[출석 보상 게임]], [[Excel Export]]

## 아직 답하지 못한 질문

- accept queue, Tomcat worker queue, DB connection wait queue는 무엇이 다른가?
- 재시도가 장애를 회복시키는 대신 증폭시키는 조건은 무엇인가?
