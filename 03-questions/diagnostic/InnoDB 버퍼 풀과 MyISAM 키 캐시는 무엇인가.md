---
type: question
domains:
  - persistence-data
  - performance-resources
status: learning
level: 0
confidence: low
prerequisites:
  - MySQL 시스템 변수의 scope와 생명주기
related_projects: []
related_sources:
  - Real MySQL 8.0 1
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# InnoDB 버퍼 풀과 MyISAM 키 캐시는 무엇인가

## 질문

`innodb_buffer_pool_size`와 `key_buffer_size`는 각각 어떤 memory 영역의 크기이며 왜 GLOBAL 변수인가요?

## 질문이 생긴 맥락

[[02-2.4.3 글로벌 변수와 세션 변수]]에서 server 전체가 공유하는 대표적인 GLOBAL system variable의 예로 두 변수를 만났다.

## 최초 답변 원문

> Q. InnoDB 버퍼풀 크기/MyISAM의 키 캐시 크기 가 각각 뭘 의미하는가 (답까지 적어줘)

## 진단 결과와 부족한 연결

두 변수가 storage engine의 global memory 영역이라는 위치는 포착했지만, cache 대상과 IO를 줄이는 과정에 대한 사용자 답변은 아직 없다.

## 관련 개념과 프로젝트

- [[MySQL 시스템 변수의 scope와 생명주기]]
- [[요청이 소비하는 리소스]]
- [[인덱스와 실행 계획]]
- [[JDBC 커넥션 풀]]

## 필요한 자료와 실험

- [[02-2.4.3 글로벌 변수와 세션 변수]]의 비교표
- `SHOW GLOBAL VARIABLES`와 InnoDB·MyISAM status counter 비교
- 동일 query를 cold·warm cache에서 실행해 disk read와 latency 비교

## 개선된 답변

InnoDB buffer pool은 모든 connection이 공유하며 InnoDB의 table data page와 index page를 cache하고 수정하는 memory 영역이다. `innodb_buffer_pool_size`는 그 전체 크기다. MyISAM key cache는 모든 thread가 공유하며 MyISAM의 index block만 cache하는 영역이고 `key_buffer_size`가 그 크기다. MyISAM data block은 별도 MySQL cache가 아니라 OS filesystem cache에 의존한다. 둘 다 connection별 자원이 아니라 server storage engine 전체의 공유 cache이므로 GLOBAL 변수다.

## 다음 꼬리 질문

왜 `innodb_buffer_pool_size`는 Global인데 `sort_buffer_size`는 Both일까요?
