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
  - 출석 보상 게임
related_sources:
  - Real MySQL 8.0 1
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# DB 서버와 애플리케이션 서버

## 한 문장 정의

애플리케이션 서버는 요청별 비즈니스 로직을 실행하고 DB 서버는 공유 영속 상태를 쿼리·락·로그·버퍼로 관리한다.

## 요청 흐름 속 위치

Spring이 JDBC 연결로 MySQL에 쿼리를 보내고 결과를 기다리는 경계에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

앱은 thread·heap 중심, DB는 connection·buffer pool·lock·log·disk I/O 중심를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

앱 스케일 아웃만으로 공유 DB 병목은 사라지지 않으며 DB 포화는 모든 앱 인스턴스로 전파된다.

## 측정 지표와 확인 방법

앱 request/thread와 DB connection/QPS/rows/lock/I/O 지표의 상관관계를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

책임별 병목을 구분하고 쿼리·인덱스·캐시·샤딩·read replica는 보장과 운영비를 함께 평가한다.

## 연결된 개념

- [[서버란 무엇인가]]
- [[JDBC 커넥션 풀]]
- [[인덱스와 실행 계획]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- 앱 서버를 두 배로 늘렸을 때 DB가 더 느려질 수 있는 이유는 무엇인가?

## 학습 자료

- [[Real MySQL 8.0 1]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
