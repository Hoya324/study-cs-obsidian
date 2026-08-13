---
type: concept
domains:
  - network-web
  - spring
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
# 웹 서버와 WAS

## 한 문장 정의

웹 서버는 정적 콘텐츠·TLS·프록시를 주로 담당하고 WAS는 애플리케이션 코드를 실행하지만 실제 제품의 경계는 겹칠 수 있다.

## 요청 흐름 속 위치

외부 트래픽을 받아 Tomcat/Spring으로 라우팅하는 입구에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

connection, buffer, worker/event loop, routing table를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

잘못된 버퍼링·timeout·health check는 정상 인스턴스 배제나 과부하 전파를 만든다.

## 측정 지표와 확인 방법

upstream latency, active connection, 4xx·5xx, health status를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

각 계층의 책임과 timeout 순서를 문서화하고 정적 처리·TLS 종료·동적 실행을 구분해 관측한다.

## 연결된 개념

- [[HTTP 요청과 응답]]
- [[Tomcat 스레드 풀]]
- [[AWS와 네트워크 경계]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- Nginx와 Tomcat을 함께 둘 때 실패와 timeout 경계는 어떻게 나뉘는가?

## 학습 자료

- [[주니어 백엔드 개발자가 반드시 알아야 할 실무 지식]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
