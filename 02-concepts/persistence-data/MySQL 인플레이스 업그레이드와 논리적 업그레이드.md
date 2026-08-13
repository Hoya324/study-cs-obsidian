---
type: concept
domains:
  - persistence-data
  - infrastructure-operations
status: learning
level: 0
confidence: medium
prerequisites:
  - DB 서버와 애플리케이션 서버
related_projects: []
related_sources:
  - Real MySQL 8.0 1
  - 공식 레퍼런스 색인
last_reviewed: 2026-08-13
next_review: 2026-08-14
created: 2026-08-13
updated: 2026-08-13
---
# MySQL 인플레이스 업그레이드와 논리적 업그레이드

## level 근거

아직 level 상승 근거 없음. 중간 release series와 GA 제약은 포착했지만, 기존 데이터 디렉터리의 변화와 dump/load의 차이를 자신의 말로 설명한 기록은 없다.

## 한 문장 정의

인플레이스 업그레이드는 새 서버가 기존 데이터 디렉터리를 직접 변환하고, 논리적 업그레이드는 구버전의 논리 데이터와 객체 정의를 새 서버의 새 데이터 디렉터리에 재생성한다.

## 요청 흐름 속 위치

평상시 SQL 요청 처리 자체가 아니라 DB 서버의 version lifecycle과 배포·복구 경계에 속한다. 실패하면 모든 JDBC 요청과 batch job이 영향을 받으므로 [[장애 복구]]와 [[관측 가능성]]에 직접 연결된다.

## 왜 필요한가

버전 업그레이드는 단순 설치 작업이 아니다. data dictionary, system schema, 설정, 인증, SQL 호환성, optimizer 동작과 application driver가 함께 바뀔 수 있다. 방법 선택에 따라 downtime, 추가 저장 공간, 롤백 방법과 데이터 유실 위험이 달라진다.

## 내부 동작

### 인플레이스

1. 구버전 서버를 정상 종료한다.
2. 서버 바이너리·패키지 또는 container image를 교체한다.
3. 새 `mysqld`를 기존 data directory에 연결한다.
4. 서버가 data dictionary와 system schema 등을 필요한 형식으로 올린다.
5. 같은 물리 데이터를 새 서버가 계속 서비스한다.

### 논리적

1. 구버전 서버에서 DDL·DML 형태로 schema와 데이터를 export한다.
2. 새 버전 서버와 새 data directory를 만든다.
3. dump를 실행해 객체와 row를 다시 생성한다.
4. application 검증 뒤 접속 대상을 새 서버로 전환한다.

## 소비하는 리소스

- 인플레이스: upgrade 중 CPU·IO, metadata 변환 시간, error log 공간, backup·snapshot 공간
- 논리적: dump와 새 data directory를 위한 추가 저장 공간, export/import CPU·IO, network, 긴 처리 시간

## 실패 조건과 관찰되는 증상

- 지원되지 않는 version 경로: 시작 또는 사전 검사 실패
- 제거된 system variable·plugin·SQL 기능: startup 오류나 application query 실패
- 손상되거나 호환되지 않는 table·metadata: upgrade 중단, error log 오류
- dump 누락: routine, event, account 또는 권한 누락
- 성능 회귀: 같은 SQL의 실행 계획 변화, latency·CPU·IO 증가
- 복구 미검증: 장애 때 backup은 있지만 실제 restore 불가

## 측정 지표와 확인 방법

- Upgrade Checker의 error·warning·notice
- MySQL error log와 startup 완료 여부
- schema/object 수, 핵심 table row count와 checksum
- application integration test와 driver 연결
- 핵심 query의 실행 계획, p95/p99 latency, throughput, CPU·IO
- backup restore 성공 시간과 실제 RPO·RTO

## 해결 전략과 트레이드오프

- 다운타임과 저장 공간이 우선이면 인플레이스를 검토하되, 원본 복구 경계를 별도로 보존한다.
- 병렬 검증·환경 교체·명확한 cutover가 우선이면 논리적 업그레이드나 replication 기반 전환을 검토한다.
- 어느 방식이든 공식 지원 경로, Upgrade Checker, 실제 workload 리허설, 복원 검증을 생략하지 않는다.
- 8.0에서 9.x로 갈 때는 8.4 LTS를 중간 경계로 삼는다.

## 연결된 개념

- [[DB 서버와 애플리케이션 서버]]
- [[장애 복구]]
- [[관측 가능성]]
- [[컨테이너와 이미지]]
- [[트랜잭션 락과 격리 수준]]

## 내 프로젝트 사례

Docker `realmysql` 실습은 8.0 data volume의 복사본을 만든 뒤 `8.0 → 8.4 → 9.x` 순서로 image와 동일 volume의 결합을 바꾸며 인플레이스 동작을 관찰할 수 있다. 원본 volume과 논리 dump를 모두 보존해야 비교와 복구 실험이 가능하다.

## 90초 설명

`지원 version 경로 → 기존 data directory를 바꾸는 인플레이스 → 새 data directory를 만드는 논리적 방식 → downtime·저장 공간·롤백 trade-off → 검사와 복원 검증` 순서로 설명한다.

## 아직 답하지 못한 질문

- [[MySQL 8.0에서 9.0으로 어떻게 업그레이드하는가]]
- 인플레이스 업그레이드에서 실패 시점별 rollback 경계는 어디인가?

## 학습 자료

- [[Real MySQL 8.0 1]]의 [[02-2.3 MySQL 서버 업그레이드]]
- [[공식 레퍼런스 색인]]
