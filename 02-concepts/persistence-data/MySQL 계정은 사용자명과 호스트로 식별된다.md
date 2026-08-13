---
type: concept
domains:
  - persistence-data
  - security-auth
status: learning
level: 1
confidence: high
prerequisites:
  - 보안 경계
related_projects: []
related_sources:
  - Real MySQL 8.0 1
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL 계정은 사용자명과 호스트로 식별된다

## level 근거

현재 level 1. 사용자가 “사용자 계정 뿐 아니라 사용자의 접속지점(IP주소)도 계정의 일부”라고 설명해 계정의 구성 요소를 자기 말로 정의했다. 후보 계정 선택 과정과 장애 진단은 아직 사용자 설명으로 확인하지 않았다.

## 한 문장 정의

MySQL 계정의 identity는 사용자명 하나가 아니라 `'user'@'host'` 조합이다.

## 인증·인가 흐름

```text
접속 사용자명 + client host
→ 일치할 mysql.user row 하나 선택
→ password·authentication plugin·account lock 검사
→ 연결 수립
→ 선택된 계정의 privilege로 SQL 요청 검사
```

인증은 “누구인가”를 확인하고, 인가는 인증된 `'user'@'host'`가 요청한 작업을 해도 되는지 확인한다.

## 가장 중요한 함정

- `'app'@'localhost'`와 `'app'@'%'`는 서로 다른 계정이다.
- 여러 후보의 권한을 합치지 않고, 정렬 후 처음 일치한 계정 하나를 사용한다.
- literal host·IP처럼 구체적인 Host가 `%` 같은 넓은 pattern보다 우선한다.
- `USER()`는 접속자가 제시한 정체, `CURRENT_USER()`는 실제 권한 검사에 사용된 계정을 보여 준다.

## Spring backend와 연결

Spring의 JDBC URL·실행 위치·Docker network가 MySQL이 보는 client host를 바꿀 수 있다. application을 local process에서 container로 옮겼을 때 같은 DB username과 password인데도 접속이 실패한다면, 실제 source host와 생성된 MySQL account의 Host 범위를 함께 확인한다.

## 실패와 확인

| 증상 | 확인 |
|---|---|
| `Access denied for user` | 오류에 표시된 user·host와 `mysql.user` 계정 범위 |
| 접속은 되지만 권한이 예상과 다름 | `SELECT USER(), CURRENT_USER()` |
| local socket은 되고 TCP는 실패 | `'user'@'localhost'`와 IP·`%` 계정 구분 |
| hostname account가 기대와 다름 | DNS와 `skip_name_resolve` 설정 |

## 연결된 노트

- [[03-3.1 사용자 식별]]
- [[MySQL 로컬 연결과 Unix domain socket]]
- [[보안 경계]]
