---
type: experiment
domains:
  - persistence-data
  - infrastructure-operations
status: verified
level: 2
confidence: high
prerequisites:
  - MySQL SET PERSIST와 설정 우선순위
related_projects: []
related_sources:
  - Real MySQL 8.0 1
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# realmysql my.cnf 설정 인벤토리

## 목적

Docker `realmysql` instance가 실제로 읽는 `my.cnf` 계열 파일의 **모든 활성 option과 주석 예제**를 구분하고, 현재 runtime 값과 persisted override 존재 여부를 확인한다.

이 목록은 모든 MySQL system variable의 사전이 아니라 현재 container image의 `/etc/my.cnf`에 들어 있는 항목의 인벤토리다.

## 환경

- 확인일: 2026-08-13
- container: `realmysql`
- MySQL: 8.0.44
- data directory: `/var/lib/mysql/`
- 기본 파일: `/etc/my.cnf`
- include: `/etc/mysql/conf.d/`
- include directory의 추가 `.cnf`: 없음
- `mysqld-auto.cnf`: 없음
- persisted variable: 0건

## `[mysqld]` 활성 option 전체

| my.cnf 표기 | 종류 | 현재 확인값·효과 | 메모 |
|---|---|---|---|
| `skip-host-cache` | startup command option | host cache 비활성화 | MySQL 8.0.30부터 deprecated. `host_cache_size=0` 사용 권장 |
| `skip-name-resolve` | global system variable | `skip_name_resolve=ON` | account host matching에서 hostname 대신 IP 사용 |
| `datadir=/var/lib/mysql` | global, read-only system variable | `/var/lib/mysql/` | DB file과 `mysqld-auto.cnf` 위치의 기준 |
| `socket=/var/run/mysqld/mysqld.sock` | global, read-only system variable | 같은 경로 | Unix domain socket path |
| `secure-file-priv=/var/lib/mysql-files` | global, read-only system variable | `/var/lib/mysql-files/` | file import/export 허용 directory 제한 |
| `user=mysql` | startup command option | OS process user `mysql` | MySQL account가 아니라 `mysqld` 실행 OS user |
| `pid-file=/var/run/mysqld/mysqld.pid` | global, read-only system variable | 같은 경로 | server process ID file |

`skip-host-cache`가 있어도 `host_cache_size` 조회값은 `279`로 보였다. 공식 문서상 이 startup option은 cache를 비활성화하며, 이후 `host_cache_size` 값을 바꿔도 cache가 다시 활성화되지 않는다. 따라서 숫자만 보고 host cache가 활성이라고 판단하면 안 된다.

또한 `skip-name-resolve=ON`이므로 이 instance는 client hostname DNS resolution을 사용하지 않는다.

## `[client]` 활성 option 전체

| my.cnf 표기 | 적용 대상 | 의미 |
|---|---|---|
| `socket=/var/run/mysqld/mysqld.sock` | option file을 읽는 client | host를 생략한 local socket connection의 기본 경로 |

같은 `socket` 이름이어도 `[mysqld]`에서는 server가 listen할 path이고 `[client]`에서는 client가 접속할 path다. 두 값이 맞아야 local socket 연결이 성공한다.

## include directive

```text
!includedir /etc/mysql/conf.d/
```

현재 directory에는 `.cnf`가 없지만 이후 file이 추가되면 함께 읽힌다. 문제 분석 때 `/etc/my.cnf` 한 파일만 보지 말고 include directory도 확인한다.

## 주석 처리된 예제 option 전체

아래 항목은 file에 보이지만 `#`로 시작하므로 현재 적용되지 않는다.

| 주석 예제 | 의미 | 현재 상태 |
|---|---|---|
| `innodb_buffer_pool_size=128M` | InnoDB shared buffer pool 크기 | 미적용 |
| `log_bin` | binary log 활성화 | 이 줄로는 미적용 |
| `join_buffer_size=128M` | index를 쓰지 못하는 join 등에 쓰이는 buffer의 기본값 | 미적용 |
| `sort_buffer_size=2M` | sort 수행 시 connection별로 할당 가능한 buffer 기본값 | 미적용 |
| `read_rnd_buffer_size=2M` | 정렬 후 random read 등에 쓰이는 session buffer 기본값 | 미적용 |
| `default-authentication-plugin=mysql_native_password` | default authentication plugin 변경 | 미적용, 8.4에서는 해당 system variable 제거됨 |

주석 예제의 큰 connection buffer 값을 그대로 활성화하면 동시 connection 수만큼 memory 사용이 늘 수 있으므로 workload 측정 없이 복사하지 않는다.

## persisted configuration 상태

```sql
SELECT *
FROM performance_schema.persisted_variables;
```

결과는 0건이고 `/var/lib/mysql/mysqld-auto.cnf`도 존재하지 않았다. `persisted_globals_load=ON`이므로 나중에 `SET PERSIST`를 실행해 file이 생성되면 restart 때 읽힌다.

## 재확인 명령

```bash
my_print_defaults mysqld
```

```sql
SELECT VARIABLE_NAME, VARIABLE_VALUE
FROM performance_schema.persisted_variables
ORDER BY VARIABLE_NAME;

SELECT VARIABLE_NAME, VARIABLE_SOURCE, VARIABLE_PATH
FROM performance_schema.variables_info
WHERE VARIABLE_SOURCE <> 'COMPILED'
ORDER BY VARIABLE_SOURCE, VARIABLE_NAME;
```

## 발견한 개선 후보

- 현재 `skip-host-cache`는 deprecated이므로 다음 configuration 정리 때 `host_cache_size=0`으로 교체를 검토한다.
- 8.4 upgrade 전에는 주석 예제라도 제거된 `default_authentication_plugin`에 의존하지 않는지 확인한다.
- custom 설정을 추가할 때 `/etc/my.cnf` 직접 변경과 `/etc/mysql/conf.d/*.cnf` mount 중 source of truth를 하나로 정한다.

## 한계

- 이 인벤토리는 검사 시점의 container filesystem과 runtime을 기준으로 한다.
- image 교체나 mounted config 추가 후에는 다시 생성해야 한다.
- compiled default로 동작하는 수백 개의 system variable은 포함하지 않고, 명시적 option과 persisted override만 추적한다.
