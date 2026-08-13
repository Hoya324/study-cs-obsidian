---
type: concept
domains:
  - persistence-data
  - spring
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - RAG 챗봇
related_sources:
  - Spring 핵심 원리 MVC JPA 강의
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# JPA 영속성 컨텍스트와 N+1

## 한 문장 정의

영속성 컨텍스트는 엔티티 identity와 변경 추적을 관리하며 N+1은 연관 접근이 추가 쿼리를 반복 발생시키는 패턴이다.

## 요청 흐름 속 위치

repository 조회부터 transaction commit과 flush까지에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

heap entity graph, DB connection, query round trip, dirty checking CPU를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

과도한 엔티티 보관·지연 로딩은 메모리와 쿼리 수를 늘려 응답 시간을 악화한다.

## 측정 지표와 확인 방법

query count, fetched rows, flush time, persistence-context size를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

유스케이스별 fetch plan·projection·batch size를 선택하고 쿼리 수를 테스트로 고정한다.

## 연결된 개념

- [[Spring 요청 생명주기]]
- [[JDBC 커넥션 풀]]
- [[인덱스와 실행 계획]]

## 내 프로젝트 사례

[[RAG 챗봇]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- OSIV를 끄면 어떤 책임이 서비스 계층으로 이동하는가?

## 학습 자료

- [[Spring 핵심 원리 MVC JPA 강의]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
