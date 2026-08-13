---
type: concept
domains:
  - concurrency-consistency
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - 데이터 중심 애플리케이션 설계
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# 분산 락과 CAS

## 한 문장 정의

분산 락은 여러 프로세스의 임계 구역 진입을 조정하고 CAS는 기대한 이전 값일 때만 상태를 원자적으로 바꾼다.

## 요청 흐름 속 위치

여러 인스턴스가 같은 사용자·쿠폰·작업 상태를 변경하는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

lock service/DB row, lease, version, retry loop를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

lease 만료, 소유권 상실, ABA, lock service 장애는 동시에 진입하거나 진행을 막을 수 있다.

## 측정 지표와 확인 방법

lock wait/failure, CAS conflict, lease renewal, critical-section duration를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

보호할 불변식과 fencing/소유권 검사를 정의하고 DB constraint를 최종 방어선으로 둔다.

## 연결된 개념

- [[동시성 경쟁 조건]]
- [[트랜잭션 락과 격리 수준]]
- [[정합성 모델과 실패 경계]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- 락을 얻은 프로세스가 멈췄다가 lease 만료 뒤 다시 실행되면 무엇이 필요한가?

## 학습 자료

- [[데이터 중심 애플리케이션 설계]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
