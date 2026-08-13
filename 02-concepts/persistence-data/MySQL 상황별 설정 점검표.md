---
type: concept
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 2
confidence: high
prerequisites:
  - MySQL 외워둘 핵심 설정
related_projects: []
related_sources:
  - Real MySQL 8.0 1
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL 상황별 설정 점검표

## 사용하는 법

증상이 생겼을 때 설정부터 바꾸지 않는다.

```text
사용자 증상 → 병목 구간 → 지표 확인 → 관련 설정의 현재값·출처 확인 → 한 번에 한 변수 실험
```

## Spring·MySQL 운영 상황 지도

| 상황 | 먼저 확인할 증상·지표 | 연결할 설정 | 성급하게 하면 안 되는 조치 |
|---|---|---|---|
| Hikari connection timeout | Hikari pending·timeout, `Threads_connected`, `Threads_running`, DB CPU | `max_connections`, application `maximumPoolSize` | DB와 pool 크기를 함께 무작정 올리기 |
| connection은 많은데 처리량이 안 나옴 | active query, CPU, lock·I/O wait, query latency | `max_connections`, pool size | connection 수가 곧 처리량이라고 가정하기 |
| SELECT가 disk I/O를 많이 발생 | working set, physical read, buffer pool eviction, query plan | `innodb_buffer_pool_size` | hit ratio 하나만 보고 RAM을 모두 할당하기 |
| write burst 뒤 latency가 튐 | dirty page, checkpoint age, fsync·storage latency | `innodb_redo_log_capacity`, `innodb_io_capacity(_max)` | redo만 크게 하고 recovery time·disk를 무시하기 |
| commit latency가 높음 | redo/binlog fsync latency, transaction rate | `innodb_flush_log_at_trx_commit`, `sync_binlog` | RPO 합의 없이 값을 0으로 낮추기 |
| crash 뒤 최근 transaction 유실 우려 | 장애 종류, redo·binlog 반영 상태, RPO | 위 두 설정과 `innodb_doublewrite` | 세 설정이 같은 보호라고 생각하기 |
| 임시 disk 사용이 증가 | TempTable memory instrument, `Created_tmp_tables`, `Created_tmp_disk_tables`, sort/group query | `tmp_table_size`, `temptable_max_ram`, `temptable_max_mmap`, `tmpdir` | 한도만 크게 올리거나 `Created_tmp_disk_tables` 하나만 믿기 |
| deadlock이 발생 | error log의 deadlock graph, transaction 순서·lock 범위 | `innodb_print_all_deadlocks` | deadlock을 단순 timeout으로만 처리하기 |
| 느린 SQL을 찾고 싶음 | latency 분포, slow log, statement digest, examined rows | `slow_query_log`, `long_query_time`, `log_slow_extra`, `performance_schema` | 운영에서 모든 계측을 영구적으로 최대 활성화하기 |
| table open 오류·FD 부족 | `Open_tables`, `Opened_tables`, process FD·OS limit | `table_open_cache`, `open_files_limit` | MySQL 값만 올리고 OS limit를 무시하기 |
| 큰 BLOB·bulk 작업 실패 | packet 관련 client/server error, payload 크기 | `max_allowed_packet` | application·proxy·replica의 다른 제한을 무시하기 |
| 한글 깨짐·정렬 결과 이상 | connection/schema/table/column charset·collation | `character_set_server`, `collation_server` | server default 변경이 기존 column도 바꾼다고 생각하기 |
| DB와 application 시간이 다름 | JVM·JDBC·session time zone, column type | `default_time_zone` | `TIMESTAMP`와 `DATETIME`을 동일하게 취급하기 |
| replication lag | network receive와 SQL apply 중 어디가 밀리는지, worker wait·lock | `replica_parallel_workers`, `binlog_format`, relay 설정 | worker 수만 무조건 늘리기 |
| replica가 필요한 binlog를 못 받음 | 현재 지연 시간, oldest binlog, disk 여유 | `binlog_expire_logs_seconds` | 보존 기간을 disk 절약만 보고 짧게 잡기 |
| CDC·복제 event가 기대와 다름 | downstream 지원 형식, row image, event volume | `binlog_format`, `binlog_row_image`, GTID | consumer 호환성 확인 없이 `MINIMAL` 사용하기 |
| MySQL 재시작 후 값이 달라짐 | `SHOW GLOBAL VARIABLES`, `performance_schema.variables_info`, option file 순서 | `SET GLOBAL`, `SET PERSIST`, `my.cnf`, `mysqld-auto.cnf` | runtime 값만 보고 원본 설정을 단정하기 |
| `LOAD DATA LOCAL`이 실패 | client option, server permission, 허용 경로 | `local_infile`, `secure_file_priv` | 편의를 위해 production 제한을 전면 해제하기 |
| 접속 단계가 DNS에 묶임 | handshake latency, DNS 상태, account host 정의 | `skip_name_resolve` | hostname 기반 account를 둔 채 활성화하기 |
| error log에 단서가 부족 | error log severity와 timestamp, rotation | `log_error_verbosity`, `log_timestamps` | 민감 SQL을 남기는 `log_raw`를 켜기 |

## 장애 종류별 내구성 구분

| 장애 | 주로 연결되는 보호 | 기억할 결과 |
|---|---|---|
| `mysqld` process crash | InnoDB redo | flush 주기 안의 redo가 있어야 committed state 복구 가능 |
| OS·전원 crash | redo fsync + binlog fsync | OS cache까지가 아니라 durable storage 반영 여부가 중요 |
| page write 도중 crash | doublewrite + checksum | torn page 탐지·복구와 연결 |
| replica·PITR 기록 손실 | binlog sync + retention | engine data와 binlog의 일관성·보존 기간이 중요 |

## 값을 바꾸기 전 공통 확인

1. 현재 MySQL 정확한 버전에서 변수의 존재·deprecated 여부를 확인한다.
2. `GLOBAL`·`SESSION`·startup 중 scope와 dynamic 여부를 확인한다.
3. 현재값뿐 아니라 `my.cnf`, `mysqld-auto.cnf` 등 설정 출처를 확인한다.
4. 성공 지표와 실패 지표를 하나씩 정한다.
5. 한 번에 한 변수만 바꾸고 되돌릴 기준을 준비한다.

> [!note] 임시 table의 버전 차이
> MySQL 8.0.28 이후 기본 TempTable engine에서는 `tmp_table_size`가 개별 in-memory internal temporary table의 상한이다. `temptable_max_ram`은 server 전체 TempTable RAM 사용에 관여한다. `max_heap_table_size`는 명시적으로 만든 MEMORY table과 internal MEMORY engine을 선택한 경우에 구분해서 본다.

## 연결

- [[MySQL 외워둘 핵심 설정]]
- [[MySQL SET PERSIST와 설정 우선순위]]
- [[Real MySQL 예시와 realmysql 8.0.44 설정 비교]]
- [[JDBC 커넥션 풀]]
- [[관측 가능성]]
