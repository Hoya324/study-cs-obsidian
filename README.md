# Backend CS Learning Vault

Spring·Java 백엔드 지식을 요청의 전체 생명주기로 연결하는 개인 학습 Vault다.

## 시작하기

1. Obsidian에서 **Open folder as vault**를 선택한다.
2. 이 `backend-cs-vault` 폴더를 연다.
3. `00-home/백엔드 CS 학습 홈.md`에서 시작한다.
4. `현재 학습 위치`에 표시된 질문 하나에 자신의 말로 답한다.

Markdown과 Properties가 원본이고 Canvas는 같은 내용을 보여주는 시각 지도다. 초기에는 커뮤니티 플러그인이 필요하지 않다.

## 운영 원칙

- 자료를 처음부터 완독하지 않고 현재 질문을 해결할 장·절만 찾는다.
- 답하거나 실험한 증거 없이 `level`을 올리지 않는다.
- 프로젝트 수치와 보장 범위를 과장하지 않는다.
- 이력서 원본과 개인정보를 Vault에 복사하지 않는다.
- 세션을 끝낼 때 다음 세션의 첫 질문을 한 개 남긴다.

## 검증

프로젝트 루트에서 다음 명령을 실행한다.

```bash
python3 scripts/validate_vault.py backend-cs-vault
```

초기 구축 검증 결과(2026-08-13):

- Python 회귀 테스트 16개 통과
- frontmatter, 내부 링크, Canvas JSON·파일 대상·동명 Markdown·node 겹침 검사 통과
- 최상위 Canvas와 대표 영역 Canvas의 실제 PNG 렌더링 확인
- 잘린 제목, 겹친 card, 색상에만 의존한 구분 없음
- Obsidian 1.13.7의 핵심 기능만 사용하며 community plugin 의존성 없음
