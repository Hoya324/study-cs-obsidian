---
type: map
domains: [persistence-data]
status: seed
level: 0
confidence: low
prerequisites: [Spring 요청 생명주기]
related_projects: [출석 보상 게임, 쿠폰 Batch, RAG 챗봇]
related_sources: [Real MySQL 8.0 1]
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# 05 JDBC JPA RDBMS와 MySQL

> [!question] 핵심 질문
> 애플리케이션은 DB 연결을 어떻게 빌리고, MySQL은 쿼리를 어떻게 실행하며, 동시 변경을 어떤 락과 로그로 보호하는가?

## 요청 흐름 속 위치

Spring 요청이 영속 상태를 읽고 쓰는 구간이다. 네트워크 왕복, 커넥션 대기, 락 대기와 디스크 I/O가 겹친다.

## 가지

- [[DB 서버와 애플리케이션 서버]]
- [[JDBC 커넥션 풀]]
- [[JPA 영속성 컨텍스트와 N+1]]
- [[인덱스와 실행 계획]]
- [[트랜잭션 락과 격리 수준]]
- InnoDB 버퍼 풀과 로그
- 복제와 장애 복구

## 연결

- 선수: [[04 Spring 내부 원리와 애플리케이션 설계]]
- 다음: [[06 리소스 성능과 부하]], [[07 동시성 트랜잭션과 데이터 정합성]]
- 자료: [[Real MySQL 8.0 1]], [[Spring 핵심 원리 MVC JPA 강의]]

## 아직 답하지 못한 질문

- Tomcat 스레드 수보다 DB 커넥션 수가 작아도 되는 이유는 무엇인가?
- 인덱스가 있는데도 읽은 행 수가 많아지는 조건은 무엇인가?
