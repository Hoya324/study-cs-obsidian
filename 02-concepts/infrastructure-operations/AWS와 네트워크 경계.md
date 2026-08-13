---
type: concept
domains:
  - infrastructure-operations
  - network-web
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - AWS 구조와 서비스
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# AWS와 네트워크 경계

## 한 문장 정의

AWS 네트워크 경계는 VPC·subnet·route·security group·load balancer로 통신 가능 범위와 진입점을 정의한다.

## 요청 흐름 속 위치

인터넷에서 애플리케이션, DB와 관리 plane으로 이어지는 배치 구조에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

IP/port, route, NAT/LB capacity, availability zone, cost를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

경로·권한 오설정과 단일 AZ 의존은 연결 실패나 과도한 노출·장애 범위 확대를 만든다.

## 측정 지표와 확인 방법

LB target health, flow log, connection/error, AZ distribution, cost를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

public/private 경계와 최소 권한을 문서화하고 다중 AZ·health check·egress 의존성을 함께 검증한다.

## 연결된 개념

- [[TCP와 연결]]
- [[웹 서버와 WAS]]
- [[보안 경계]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- public subnet과 public IP는 같은 뜻인가?

## 학습 자료

- [[AWS 구조와 서비스]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
