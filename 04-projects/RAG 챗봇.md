---
type: project
domains:
  - java-jvm
  - spring
  - persistence-data
  - performance-resources
  - infrastructure-operations
status: seed
level: 0
confidence: low
prerequisites: []
related_projects: []
related_sources:
  - Spring 핵심 원리 MVC JPA 강의
  - Real MySQL 8.0 1
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# RAG 챗봇

## 문제와 제약

검색과 외부 AI 호출이 긴 응답 시간과 DB transaction·connection 점유로 이어지지 않도록 경계를 나눠야 했다.

## 선택한 구조

- Java·Spring MVC·MySQL
- MySQL Full-Text Index와 Lucene을 활용한 검색 개선
- OSIV 비활성화
- 외부 호출 구간에 `NOT_SUPPORTED`, 별도 상태 기록에 `REQUIRES_NEW`
- 부하 테스트 수행

## 관련 지식 노드

- [[인덱스와 실행 계획]]
- [[트랜잭션 경계와 전파]]
- [[JDBC 커넥션 풀]]
- [[타임아웃과 재시도]]
- [[요청이 소비하는 리소스]]
- [[관측 가능성]]
- [[테스트 전략]]

## 보장한다고 말할 수 있는 범위

검색 latency를 약 5초에서 200ms 수준으로 줄이고 외부 대기 중 DB transaction 참여를 피하는 구조를 만들었다. 모든 end-to-end 요청이 동일한 latency를 갖거나 external failure가 제거됐다는 뜻은 아니다.

## 실패 시나리오

- 외부 AI timeout·rate limit과 retry storm
- transaction 밖 외부 호출 뒤 상태 기록 실패
- Full-Text/Lucene index freshness 차이
- 높은 동시 사용자에서 Tomcat thread 또는 HTTP connection 포화

## 관측과 복구

검색 단계, 외부 호출, DB 상태 기록을 trace span으로 나누고 p95/p99, timeout, connection usage와 결과 상태를 연결한다.

## 대안과 선택하지 않은 이유

비동기 job, reactive/non-blocking client, 전용 검색 engine, cache를 latency·복잡도·정합성으로 비교할 수 있다.

## 수치와 검증 방법

VU 3,000 부하 기록과 5초→200ms 개선은 query, corpus, hardware, warm-up, percentile과 오류율을 함께 보존해야 재현할 수 있다.

## 예상 면접 질문

- OSIV를 끄고 생긴 책임과 이점은?
- `NOT_SUPPORTED`가 connection 점유에 주는 효과와 원자성 비용은?
- VU 3,000이 동시 요청 3,000과 항상 같은 뜻인가?
