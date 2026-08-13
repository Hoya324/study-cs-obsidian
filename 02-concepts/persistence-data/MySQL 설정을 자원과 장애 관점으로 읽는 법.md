---
type: concept
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 2
confidence: medium
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
# MySQL 설정을 자원과 장애 관점으로 읽는 법

## level 근거

level 2 유지. 기존 학습에서 runtime과 persisted 설정의 상태 전이·우선순위를 설명했다. 이번에는 사진 자료를 통해 설정을 resource·durability·observability·security·replication으로 분류했지만, 사용자가 각 trade-off를 직접 설명한 증거는 아직 없다.

## 한 문장 정의

`my.cnf`는 option 이름의 목록이 아니라 MySQL이 memory·disk I/O·connection·log를 어디에 얼마나 쓰고 장애 시 무엇을 보존할지 정하는 운영 정책이다.

## 요청 생명주기에서 읽기

```text
접속 허용
→ connection thread와 session memory
→ SQL mode·charset으로 입력 해석
→ buffer pool·temporary table에서 실행
→ redo·doublewrite로 page 복구 준비
→ binlog로 복제·PITR 준비
→ Performance Schema·slow/error log로 관측
```

## 네 가지 질문

각 변수를 볼 때 다음을 답한다.

1. 이 설정은 startup·GLOBAL·SESSION 중 어디에 적용되는가?
2. memory·CPU·thread·connection·disk I/O·network 중 무엇을 소비하는가?
3. 값을 높이거나 보호 기능을 끄면 어떤 장애와 데이터 손실이 가능한가?
4. 어떤 status·Performance Schema·log로 효과와 부작용을 확인할 것인가?

## 숫자를 복사하면 안 되는 이유

- `innodb_buffer_pool_size=20G`는 host RAM, 다른 process, connection memory를 모르면 의미가 없다.
- `max_connections=8000`은 application pool 합계와 query당 memory·thread scheduling 비용을 모르면 위험하다.
- `innodb_io_capacity=1000`은 storage가 실제 감당하는 IOPS와 맞지 않으면 flushing이 너무 느리거나 공격적일 수 있다.
- Performance Schema history 50,000건은 관측 범위를 늘리지만 in-memory 비용도 늘린다.
- log retention 3일은 replica 최대 지연과 point-in-time recovery 목표보다 길어야 한다.

## 가장 위험한 결합 설정

`innodb_flush_log_at_trx_commit=0 + sync_binlog=0 + innodb_doublewrite=OFF`

- 첫 번째는 최근 commit의 redo 내구성을 약하게 한다.
- 두 번째는 commit된 transaction과 binlog의 disk 반영 사이를 약하게 한다.
- 세 번째는 partial page write의 복구 방어선을 없앤다.

세 설정은 성능 knob 세 개가 아니라 서로 다른 crash failure mode를 막는 방어선 세 개다.

## Spring backend와 연결

```text
Tomcat request thread
→ HikariCP에서 connection 대기
→ MySQL connection/thread 확보
→ session buffer와 transaction 시작
→ buffer pool hit 또는 disk I/O
→ commit 시 redo·binlog flush
→ connection 반환
```

따라서 DB 설정은 application의 timeout·pool size·transaction 경계와 분리해서 튜닝할 수 없다.

## 관측 예시

- connection 고갈: `Threads_connected`, `Threads_running`, `Connection_errors_max_connections`, Hikari pending
- memory: buffer pool 크기·hit, connection memory, Performance Schema memory instruments
- temporary table: `Created_tmp_tables`, `Created_tmp_disk_tables`
- redo pressure: redo LSN·checkpoint·dirty page·fsync latency
- replication: relay log, applier worker, replication lag
- query: slow log, statement history/digest, wait events

## 연결된 개념

- [[02-2.4.6 my.cnf 파일 예시 해설]]
- [[MySQL SET PERSIST와 설정 우선순위]]
- [[MySQL GLOBAL 변수 변경은 기존 세션에 적용되는가]]
- [[InnoDB 버퍼 풀과 MyISAM 키 캐시는 무엇인가]]
- [[관측 가능성]]
- [[장애 복구]]

## 아직 답하지 못한 질문

- 성능을 위해 commit durability 방어선을 낮춰도 되는 workload는 무엇인가?
- 현재 application instance 수와 Hikari pool을 기준으로 DB connection budget을 어떻게 계산하는가?
- buffer pool·redo capacity·I/O capacity를 어떤 측정 순서로 조정하는가?
