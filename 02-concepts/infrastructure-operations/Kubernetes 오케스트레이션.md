---
type: concept
domains:
  - infrastructure-operations
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - 그림과 실습으로 배우는 도커와 쿠버네티스
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# Kubernetes 오케스트레이션

## 한 문장 정의

Kubernetes는 선언한 desired state에 맞춰 컨테이너 배치·복구·서비스 디스커버리와 점진 배포를 조정한다.

## 요청 흐름 속 위치

여러 애플리케이션 인스턴스를 실행하고 트래픽을 연결하는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

node capacity, pod requests/limits, control plane, network, probes를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

잘못된 probe·limit·rollout은 재시작 반복, 준비 전 트래픽, 용량 부족을 만든다.

## 측정 지표와 확인 방법

pod status/restart, probe failure, pending, rollout, node saturation를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

request/limit과 startup/readiness/liveness 책임을 분리하고 disruption·rollback을 시험한다.

## 연결된 개념

- [[컨테이너와 이미지]]
- [[AWS와 네트워크 경계]]
- [[장애 복구]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- readiness와 liveness가 같은 endpoint면 어떤 장애가 생길 수 있는가?

## 학습 자료

- [[그림과 실습으로 배우는 도커와 쿠버네티스]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
