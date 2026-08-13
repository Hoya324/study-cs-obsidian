---
type: concept
domains:
  - network-web
status: seed
level: 0
confidence: low
prerequisites: []
related_projects:
  - 출석 보상 게임
related_sources:
  - 면접을 위한 CS 전공지식 노트
last_reviewed:
next_review:
created: 2026-08-13
updated: 2026-08-13
---
# TCP와 연결

## 한 문장 정의

TCP는 두 종단 사이에 순서·재전송·흐름 제어를 제공하는 바이트 스트림 연결이다.

## 요청 흐름 속 위치

DNS·TLS 뒤에서 HTTP 데이터를 운반하고 서버 소켓에 도달하는 구간에 놓인다.

## 왜 필요한가

이 개념을 알아야 요청의 계산 시간과 대기 시간을 구분하고, 실패가 어느 경계에서 시작됐는지 설명할 수 있다.

## 내부 동작

현재는 큰 틀을 잡는 시드다. 다음 진단에서 자신의 말로 흐름을 설명한 뒤 내부 구조와 반례를 채운다.

## 소비하는 리소스

socket, port, kernel buffer, bandwidth, connection state를 사용하거나 점유한다.

## 실패 조건과 관찰되는 증상

packet loss, backlog 포화, 연결 누수는 handshake 지연·재전송·connection refused로 나타난다.

## 측정 지표와 확인 방법

connection count, retransmission, RTT, accept queue, socket state를 요청 지연 및 오류와 같은 시간축에서 확인한다.

## 해결 전략과 트레이드오프

keep-alive와 timeout을 맞추고 backlog·파일 디스크립터·입구 용량을 함께 관리한다.

## 연결된 개념

- [[HTTP 요청과 응답]]
- [[타임아웃과 재시도]]
- [[서버란 무엇인가]]

## 내 프로젝트 사례

[[출석 보상 게임]]에서 이 개념이 실제로 어떤 가정과 보장을 만들었는지 확인한다. 현재 노트는 프로젝트 사실보다 강한 보장을 주장하지 않는다.

## 90초 설명

`정의 → 요청 흐름의 위치 → 사용하는 제한 자원 → 포화·실패 증상 → 지표 → 전략의 비용` 순서로 설명한다.

## 아직 답하지 못한 질문

- HTTP timeout과 TCP 연결 timeout은 어떤 실패를 각각 뜻하는가?

## 학습 자료

- [[면접을 위한 CS 전공지식 노트]]: 이 질문과 직접 관련된 장·절 또는 강의 구간만 찾는다.
