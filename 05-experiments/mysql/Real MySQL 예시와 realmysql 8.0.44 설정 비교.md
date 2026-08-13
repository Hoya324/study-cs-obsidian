---
type: experiment
domains:
  - persistence-data
  - infrastructure-operations
status: verified
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
# Real MySQL 예시와 realmysql 8.0.44 설정 비교

## 가설

책의 `my.cnf`는 현재 실습 container의 default가 아니라 대규모 운영 환경을 가정한 tuning example이며, 일부 option은 MySQL 8.0 후반에서 deprecated되었을 것이다.

## 환경

- 확인일: 2026-08-13
- container: `realmysql`
- MySQL: 8.0.44
- 비교 자료: [[02-2.4.6 my.cnf 파일 예시 해설]]의 사진 5장
- 방법: `performance_schema.global_variables`를 읽기 전용 조회

## 대표 비교

| 영역 | 책 예시 | `realmysql` 8.0.44 | 해석 |
|---|---|---|---|
| 경로 | `/data/mysql`, `/log/...` | `/var/lib/mysql`, log는 container 기본 | 책은 data·log storage 분리를 가정 |
| buffer pool | `20G`, instances `10` | `128M`, instances `1` | 장비 규모가 전혀 다름 |
| connections | `8000` | `151` | pool·thread·memory·FD budget 없이 복사 금지 |
| open files | `65535` | `20480` | OS limit와 함께 결정 |
| table cache | `30000` | `4000` | memory·FD·contention의 교환 |
| temp memory | `10M` | `16M` | 작다고 항상 좋은 것이 아니며 disk temp 지표 필요 |
| redo | 3 × 2048M | `innodb_redo_log_capacity=100 MiB` | 8.0.30 이후 새 변수로 관리 |
| buffer durability | doublewrite `OFF` | `ON` | 실습 default가 안전한 쪽 |
| commit redo flush | `0` | `1` | 책 예시는 성능 우선, 실습은 commit durability 우선 |
| binlog flush | `0` | `1` | 책 예시는 OS crash 시 binlog 유실 위험 증가 |
| flush method | `O_DIRECT_NO_FSYNC` | `fsync` | storage·filesystem 보장 확인 필요 |
| I/O capacity | `1000` / `5000` | `200` / `2000` | storage IOPS에 맞춰 조정 |
| AHI | `OFF` | `ON` | workload contention에 따라 선택 |
| deadlock logging | `ON` | `OFF` | 관측성과 log volume의 교환 |
| slow log | `ON`, 1초, extra | `OFF`, 10초 | 책은 적극적인 query 관측 |
| Performance Schema long history | `50000` | `10000` | 책 예시는 더 긴 in-memory history |
| GTID | `ON` | `OFF` | 실습은 replication을 구성하지 않음 |
| binlog row image | `MINIMAL` | `FULL` | volume과 downstream 사용성 교환 |
| error verbosity | `1` | `2` | 책 값은 오류만 남겨 진단 정보가 적음 |
| log timestamp | `SYSTEM` | `UTC` | 여러 서버 상관 분석에는 UTC가 단순 |

## 이름·수명 확인

- `slave_*` alias는 8.0.44에도 조회되지만 8.0.26 이후 `replica_*` 이름을 우선 사용한다.
- `innodb_log_file_size`, `innodb_log_files_in_group`는 조회되지만 8.0.30부터 deprecated이며 `innodb_redo_log_capacity`로 대체한다.
- `innodb_undo_tablespaces`는 값 2로 보이지만 8.0.14부터 설정할 수 없다.
- `relay_log_info_repository=TABLE`은 현재도 보이지만 변수는 deprecated이며 TABLE이 기본이므로 명시할 필요가 없다.
- `validate_password.*`는 현재 container에서 조회되지 않았다. component/plugin이 설치되지 않은 상태로 판단한다.

## 결과

가설을 확인했다. 사진은 현재 실습 환경용 copy-paste template가 아니다. 특히 memory·connection·I/O 수치는 hardware와 workload 종속적이며, durability 관련 세 값은 기본값보다 보호 수준을 낮춘다.

## 한계

- 사진 예시를 실제로 적용하지 않았다.
- 현재 container는 production storage와 traffic을 재현하지 않는다.
- runtime 조회는 option이 존재함을 보여 주지만, 모든 deprecated·removal 시점을 자체적으로 증명하지 않으므로 공식 문서를 함께 확인했다.

## 다음 실험 후보

1. `performance_schema.variables_info`로 책의 대표 변수 10개의 source와 dynamic 여부 조회
2. Hikari pool 수를 가정해 connection memory budget 계산
3. 별도 disposable replica에서 redo·binlog durability 조합별 crash 결과 비교
