---
type: concept
domains:
  - persistence-data
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
# JDBC 커넥션 풀

## 한 문장 정의

JDBC 커넥션 풀은 비싼 DB 연결을 재사용하고 애플리케이션이 동시에 DB를 사용하는 수를 제한한다.

## 요청 흐름 속 위치

Spring transaction 또는 repository가 쿼리 전 connection을 빌리고 반환하는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

DB session, socket, server thread/memory, client wait queue를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

connection 누수·긴 transaction·풀 부족은 acquisition timeout과 Tomcat thread 적체를 만든다.

## 측정 지표와 확인 방법

active/idle/pending connection, acquire time, usage time, timeout를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

DB 처리 용량과 transaction 시간을 기준으로 상한을 두고 누수 탐지·timeout·빠른 실패를 함께 설정한다.

## 연결된 개념

- [[Tomcat 스레드 풀]]
- [[트랜잭션 경계와 전파]]
- [[부하와 병목]]

## 내 프로젝트 사례

[[RAG 챗봇]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- Tomcat thread pool과 connection pool을 같은 크기로 맞춰야 하는가?

## 학습 자료

- [[주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
