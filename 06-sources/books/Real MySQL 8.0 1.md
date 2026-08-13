---
type: source
domains:
  - persistence-data
  - concurrency-consistency
status: learning
level: 0
confidence: low
prerequisites: []
related_projects: []
related_sources: []
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# Real MySQL 8.0 1

- 형태: 도서
- 상태: 보유
- 역할: MySQL 서버·InnoDB·transaction·lock·index·실행 계획 심화
- 공식 페이지: https://wikibook.co.kr/realmysql801-ebook/

## 이 자료를 여는 조건

connection·lock·index·buffer pool의 내부 동작과 지표가 필요할 때

## 장·절 또는 섹션 찾기 규칙

현재 질문의 핵심 용어를 목차에서 찾고, `읽을 위치 → 답할 질문 → 완료 증거`를 세트로 기록한다. 처음부터 순서대로 완독하지 않는다.

## 읽기 진행

- [[01-1.2 왜 MySQL인가]] — DBMS 선택 기준: 안정성 → 성능과 기능 → 커뮤니티와 인지도
- [[02-2.2.3 서버 연결 테스트]] — host와 protocol에 따른 Unix domain socket·TCP/IP 선택
- [[02-2.3 MySQL 서버 업그레이드]] — 인플레이스·논리적 방식과 `8.0 → 8.4 LTS → 9.x` 지원 경로
- [[02-2.4.2 MySQL 시스템 변수의 특징]] — 다섯 속성과 GLOBAL·SESSION·Dynamic의 생명주기
- [[02-2.4.3 글로벌 변수와 세션 변수]] — server 공유 cache와 connection별 설정의 scope 구분

## 완료 증거

- 자신의 말로 요청 흐름에 연결한 설명
- 실패 조건과 지표 한 쌍
- 프로젝트 또는 작은 실험에 적용한 결과

## 저작권 원칙

본문을 복제하지 않고 위치·요약·자신의 설명만 남긴다.
