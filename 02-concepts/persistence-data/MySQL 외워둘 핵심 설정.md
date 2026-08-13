---
type: concept
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 2
confidence: high
prerequisites:
  - MySQL 설정을 자원과 장애 관점으로 읽는 법
related_projects: []
related_sources:
  - Real MySQL 8.0 1
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL 외워둘 핵심 설정

## 암기 원칙

103개의 이름과 숫자를 외우지 않는다. 다음 세 가지만 기억한다.

1. **무엇을 지키거나 제한하는 설정인가**
2. **값을 바꾸면 어느 자원이 늘고 어떤 장애 위험이 생기는가**
3. **변경 전에 어떤 지표를 봐야 하는가**

숫자는 장비·MySQL 버전·workload에 종속되므로 외우지 않는다. 기본값도 버전에 따라 달라질 수 있으므로 적용 전에 runtime과 공식 문서를 확인한다.

## A급 — 이름과 역할을 바로 떠올릴 설정

면접, 장애 대응, Spring application 설계에서 자주 연결되는 핵심이다.

### A1 — 먼저 외울 8개

| 설정 | 한 문장 기억 | 함께 볼 것 |
|---|---|---|
| `max_connections` | DB가 동시에 받아들일 connection 상한 | 모든 Hikari pool 합, `Threads_connected`, `Threads_running` |
| `innodb_buffer_pool_size` | InnoDB data·index page의 핵심 shared cache | host RAM, hit ratio보다 working set·disk read·eviction |
| `innodb_redo_log_capacity` | checkpoint 사이에서 변경을 받아 줄 redo 공간 | write burst, checkpoint pressure, recovery time |
| `innodb_flush_log_at_trx_commit` | commit된 redo를 언제 disk까지 보낼지 결정 | commit latency와 mysqld·OS·전원 장애 시 유실 범위 |
| `sync_binlog` | binlog를 몇 transaction마다 disk와 동기화할지 결정 | replication·PITR 내구성과 fsync 비용 |
| `innodb_doublewrite` | partial page write에서 page를 복구할 방어선 | storage atomic write 보장 여부, write 비용 |
| `tmp_table_size` + `temptable_max_ram` | 8.0.28+ 기본 TempTable에서 개별 table과 전체 RAM 한도를 나눠 제어 | TempTable memory instrument, query shape, disk·mmap 사용 |
| `slow_query_log` + `long_query_time` | 느린 SQL을 발견하기 위한 시작점 | 기준 시간, log volume, digest·실행 계획 |

### A2 — 그다음 연결할 6개

| 설정 | 한 문장 기억 | 함께 볼 것 |
|---|---|---|
| `performance_schema` | wait·statement·memory 등 내부 event 관측 기반 | 필요한 instrument·consumer만 활성화, memory overhead |
| `sql_mode` | 잘못된 값과 SQL을 허용·보정·거부하는 규칙 | application validation, migration 호환성 |
| `character_set_server` + `collation_server` | 문자열 저장 기본값과 비교·정렬 의미 | schema·column 실제 설정, JDBC charset |
| `default_time_zone` | 새 session이 물려받는 기본 시간대 | JVM/JDBC, `TIMESTAMP`와 `DATETIME`, UTC 정책 |
| `binlog_format` | 복제·CDC에 statement 또는 row 변화를 어떻게 기록할지 결정 | 정확성, binlog 양, downstream 호환성 |
| `binlog_expire_logs_seconds` | binlog를 얼마 동안 보존할지 결정 | PITR 목표와 replica 최대 지연 |

## 핵심 결합 두 개

### Connection budget

```text
모든 Spring instance의 maximumPoolSize 합
+ batch·admin·migration·replication 여유
< MySQL max_connections
```

`max_connections`를 크게 만드는 것이 해결책은 아니다. connection마다 thread와 session 단위 memory·server scheduling 비용이 생기며, DB가 감당할 수 있는 동시 query 수가 먼저 한계가 될 수 있다.

### Commit durability

```text
InnoDB transaction
→ redo: innodb_flush_log_at_trx_commit
→ binlog: sync_binlog
→ data page: innodb_doublewrite
```

세 설정은 같은 기능이 아니다.

- redo 설정은 commit 복구 가능성에 관여한다.
- binlog 설정은 replication·PITR 기록의 내구성에 관여한다.
- doublewrite는 torn page 복구에 관여한다.

성능을 이유로 셋을 한꺼번에 약화하지 않는다. 업무가 허용하는 RPO와 storage 보장을 먼저 정한다.

## 값의 의미까지 기억할 것

| 설정값 | 기억할 의미 |
|---|---|
| `innodb_flush_log_at_trx_commit=1` | commit마다 redo를 log file에 쓰고 disk로 flush하는 가장 강한 기본 선택 |
| `...=2` | commit마다 log file에는 쓰지만 disk flush는 대략 1초 주기이므로 OS·전원 장애에 취약 |
| `...=0` | redo write와 flush 모두 대략 1초 주기이므로 mysqld crash에서도 최근 transaction 유실 가능 |
| `sync_binlog=1` | transaction commit group마다 binlog를 disk와 동기화하는 가장 강한 선택 |
| `sync_binlog=0` | MySQL이 binlog를 직접 동기화하지 않고 OS에 맡김 |
| `sync_binlog=N` | N개의 binlog commit group마다 동기화; N이 커질수록 write 비용과 유실 범위가 교환됨 |
| `innodb_doublewrite=ON` | atomic page write 보장이 확인되지 않았다면 유지하는 안전한 출발점 |

주기성 flush의 “1초”는 scheduling 때문에 정확한 손실 상한을 보장하는 숫자가 아니다. 최고의 InnoDB·binlog 내구성이 필요하면 공식 권장은 `innodb_flush_log_at_trx_commit=1`과 `sync_binlog=1` 조합이다.

## B급 — 상황이 생기면 이름을 떠올릴 설정

| 영역 | 설정 | 언제 떠올리는가 |
|---|---|---|
| file·table cache | `open_files_limit`, `table_open_cache` | table open 오류, 높은 `Opened_tables`, FD 고갈 |
| 큰 요청 | `max_allowed_packet` | 큰 BLOB·bulk insert·replication packet 오류 |
| background I/O | `innodb_io_capacity`, `innodb_io_capacity_max` | dirty page 증가, flushing 부족 또는 과도한 I/O |
| MEMORY table | `max_heap_table_size` | 명시적 MEMORY table 또는 internal MEMORY engine의 크기 제한 |
| query 진단 | `innodb_print_all_deadlocks`, `log_slow_extra` | deadlock 원인과 느린 SQL의 작업량을 남겨야 할 때 |
| import·export 보안 | `local_infile`, `secure_file_priv` | `LOAD DATA`, `SELECT ... INTO OUTFILE` 실패·보안 검토 |
| 접속 DNS | `skip_name_resolve` | 접속 지연이나 DNS 장애, host 기반 account 점검 |
| replication 식별 | `server_id`, `gtid_mode`, `enforce_gtid_consistency` | replica·failover·CDC topology를 구성할 때 |
| replica 처리량 | `replica_parallel_workers` | replication lag가 SQL apply 단계에서 쌓일 때 |
| relay 복구 | `relay_log_recovery`, `relay_log_purge` | replica crash 복구와 relay log disk 관리 |
| 운영 로그 | `log_error_verbosity`, `log_timestamps` | 진단 정보 부족, 여러 서버 시간 상관 분석 |

## C급 — 외우지 않고 문서에서 찾을 설정

- 경로와 파일 배치: `datadir`, `tmpdir`, redo·undo·relay·error·slow log 경로
- Performance Schema의 개별 `instrument`, `consumer`, history size
- password validation의 문자 종류별 개수
- full-text parser, compressed table처럼 특정 기능에서만 쓰는 설정
- hardware와 workload가 없으면 의미 없는 cache instance 수와 세부 buffer 수치

이 항목은 “존재와 영역”만 기억하고 필요할 때 [[02-2.4.6 my.cnf 파일 예시 해설]]과 공식 문서를 검색한다.

## 현재 8.0에서 옛 이름으로 외우지 않을 것

| 과거 설정 | 현재 기억할 것 |
|---|---|
| `innodb_log_file_size`, `innodb_log_files_in_group` | 8.0.30 이후 `innodb_redo_log_capacity` 중심 |
| `innodb_undo_tablespaces` | 8.0.14 이후 설정값이 아니라 기본 undo tablespace와 DDL 관리 |
| `slave_*` 계열 | `replica_*` 이름 우선 |
| `keyring_file` plugin | 최신 8.0에서는 component 기반 keyring 우선 검토 |
| `relay_log_info_repository=TABLE` | TABLE이 기본이고 변수는 deprecated이므로 보통 생략 |

## 30초 회상 카드

> 연결이 몰리면 `max_connections`와 Hikari pool 합을 본다. 읽기 I/O가 많으면 buffer pool을 본다. 쓰기 정체면 redo capacity와 I/O capacity를 본다. commit 내구성은 redo flush·binlog sync·doublewrite를 묶어서 본다. 느린 SQL은 slow log와 Performance Schema로 찾는다. 문자열과 시간 문제는 charset·collation·time zone을 확인한다. 복제는 binlog·GTID·replica worker·보존 기간을 본다.

## 연결

- [[MySQL 상황별 설정 점검표]]
- [[02-2.4.6 my.cnf 파일 예시 해설]]
- [[MySQL 시스템 변수의 scope와 생명주기]]
- [[JDBC 커넥션 풀]]
- [[장애 복구]]
- [[관측 가능성]]

## 공식 확인 기준

- MySQL connection 한도: https://dev.mysql.com/doc/refman/8.0/en/too-many-connections.html
- InnoDB redo capacity: https://dev.mysql.com/doc/refman/8.0/en/innodb-init-startup-configuration.html
- redo·binlog 내구성 조합: https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html
- internal temporary table: https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html
