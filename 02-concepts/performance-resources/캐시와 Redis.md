---
type: concept
domains:
  - performance-resources
  - persistence-data
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - 주니어 백엔드 개발자가 반드시 알아야 할 실무 지식
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# 캐시와 Redis

## 한 문장 정의

캐시는 원본보다 빠른 저장소에 재사용 결과를 두어 반복 계산·DB·외부 호출 비용을 줄인다.

## 요청 흐름 속 위치

Spring과 DB·외부 시스템 사이의 읽기·상태 조회 경로에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

memory, key count, network, eviction policy, consistency budget를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

stale data, cache stampede, hot key, eviction과 장애 시 DB 부하 급증이 생길 수 있다.

## 측정 지표와 확인 방법

hit ratio, latency, memory, eviction, hot key, fallback load를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

TTL·무효화·원본 권위를 명확히 하고 stampede 방지와 cache 장애 시 보호를 설계한다.

## 연결된 개념

- [[부하와 병목]]
- [[DB 서버와 애플리케이션 서버]]
- [[정합성 모델과 실패 경계]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- Redis를 단순 캐시와 상태 원장으로 쓸 때 보장 차이는 무엇인가?

## 학습 자료

- [[주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
