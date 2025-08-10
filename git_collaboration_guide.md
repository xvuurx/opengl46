# Git 협업 가이드 (A/B 시나리오 · 심화)

팀원이 2명(A, B)이라고 가정하고, **쉽게** 시작하되 **실무에서 바로 쓸 수 있게** 깊이를 더했습니다. 브랜치 전략, PR 규칙, 충돌 해결, 보호 브랜치, 템플릿, 훅(hook), LFS까지 한 문서로 끝냅니다.

---

## 0) TL;DR — 하루 실전 협업 흐름

**A (로그인 구현)**

1. 최신 받기: `git checkout main && git pull origin main`
2. 브랜치: `git checkout -b feat/login`
3. 작업/커밋: `git add . && git commit -m "feat(login): add form and API"`
4. 푸시: `git push -u origin feat/login`
5. PR 생성: 제목/설명/체크리스트 채우고 리뷰어에 **B 지정** → “Squash and merge” 정책

**B (회원가입 구현)**

1. 최신 받기: `git checkout main && git pull origin main`
2. 브랜치: `git checkout -b feat/signup`
3. 작업/커밋: `git add . && git commit -m "feat(signup): email validation"`
4. 푸시: `git push -u origin feat/signup`
5. A의 PR 리뷰(코멘트/승인) → 본인 PR도 동일 절차

**둘 다 반복 규칙**

- PR 전: `git fetch origin && git rebase origin/main` (충돌 해결→테스트→푸시 `--force-with-lease`)
- main에 직접 푸시 금지(보호 브랜치)
- 이슈 연결: PR 본문에 `Closes #123`/`Fixes #123` 기입

---

## 1) 브랜치 전략

### 1.1 권장: 트렁크 기반(Trunk-Based)

- **main**: 항상 배포 가능 상태
- **기능 브랜치**: `feat/<키워드>`, `fix/<키워드>` 등 **단명(short-lived)**
- PR 머지 후 **Squash merge**로 히스토리 정리

### 1.2 브랜치 네이밍 규칙

- `feat/<영문키워드>`: 새 기능 (예: `feat/login`)
- `fix/<영문키워드>`: 버그 수정 (예: `fix/login-null`)
- `refactor/`, `docs/`, `chore/`, `test/` 등 목적 기반 접두사 사용

---

## 2) 커밋 메시지 규칙 (Conventional Commits)

형식: `type(scope): summary`

- **type**: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `build`, `ci`, `style`
- **scope**: 선택(모듈/기능 이름), 예: `login`, `auth`
- **summary**: 한글/영문 자유, 명령형/간결하게

예시

```text
feat(login): add form and API
fix(auth): prevent null token crash
refactor(ui): extract button component
```

**Breaking change**는 본문에 `BREAKING CHANGE:`로 명시.

---

## 3) 이슈 → 브랜치 → PR → 리뷰 → 머지

### 3.1 이슈 생성

- 제목: 간결/명확 (예: `로그인 폼 제출 시 400 에러`)
- 내용: 재현 절차, 기대/실제 결과, 스크린샷/로그
- 라벨: `bug`, `feature`, `urgent` 등

### 3.2 브랜치 생성 (A 예시)

```bash
git checkout main
git pull origin main
git checkout -b feat/login
```

### 3.3 작업/테스트/커밋

```bash
git add .
git commit -m "feat(login): add form and API"
```

### 3.4 최신 main으로 리베이스(권장)

```bash
git fetch origin
git rebase origin/main
# 충돌 시 파일 수정 →
git add <파일>
git rebase --continue
# 취소하려면
git rebase --abort
```

푸시:

```bash
git push -u origin feat/login
# 리베이스 후 재푸시 시
git push --force-with-lease
```

### 3.5 PR 작성

- 제목: `[feat] 로그인 폼/API 추가`
- 본문: 문제/해결/영향/테스트 방법/관련 이슈(`Closes #123`)
- 체크리스트: 빌드 OK, 테스트 통과, 린트 통과, 문서 반영
- 리뷰어/어사이니 지정, 라벨 설정

### 3.6 코드 리뷰 (B가 A의 PR 리뷰)

- NIT(사소한 개선) / MUST(필수 수정) 구분
- 스니펫으로 제안, 성능/보안/테스트 관점 포함
- 승인이면 **Approve**, 수정 필요면 **Request changes**

### 3.7 머지 정책

- 권장: **Squash and merge** (PR 내 커밋을 하나로 압축)
- 대안: **Rebase and merge** (히스토리 직선 유지)
- 피하기: 큰 팀에서는 **Merge commit** 난립 주의

머지 후 브랜치 정리:

```bash
git branch -d feat/login
git push origin --delete feat/login
```

---

## 4) 충돌(Conflict) 해결 심화

### 4.1 왜 나는가

- 같은 파일 **같은 영역**을 서로 다르게 수정

### 4.2 해결 절차 (A의 로컬에서)

```bash
git fetch origin
git rebase origin/main
# 충돌 파일 열기 → <<<<<<<, =======, >>>>>>> 구간 수동 정리
# 혹은 머지툴 사용 (예: VS Code)
# VS Code: 커맨드 팔레트 → "Git: Open Merge Editor"

git add <충돌해결한파일>
git rebase --continue
```

테스트/빌드 확인 후:

```bash
git push --force-with-lease
```

### 4.3 자주 쓰는 도움 명령

```bash
git status              # 충돌 파일 확인
git diff                # 차이 확인
git mergetool           # GUI 머지툴 실행(설정 필요)
```

---

## 5) 원격/포크 동기화 (Fork 흐름 포함)

### 5.1 팀 저장소 직접 협업

- `origin`만 있으면 충분

### 5.2 포크 기반 협업 (내 계정 저장소로 작업)

```bash
git remote -v
# upstream(원본) 추가
git remote add upstream https://github.com/팀/프로젝트.git
# 최신 동기화
git fetch upstream
git checkout main
git rebase upstream/main
```

푸시 대상은 **내 포크(origin)**, PR은 \*\*원본(upstream)\*\*에 생성

---

## 6) 보호 브랜치 & 필수 체크 설정 (GitHub)

1. **Settings → Branches → Add rule**
2. `main` 보호 규칙 예시
   - Require a pull request before merging
   - Require approvals: **1+** (팀 규모에 맞게)
   - Dismiss stale approvals on new commits
   - Require status checks to pass (CI: build/test/lint)
   - Require branches to be up to date before merging
   - Include administrators (선택)

---

## 7) CODEOWNERS / PR 템플릿

### 7.1 CODEOWNERS

`.github/CODEOWNERS`

```text
# login 관련 변경은 A와 B가 리뷰
src/auth/login/*  @userA  @userB
# 모든 프론트엔드 변경은 A
web/**            @userA
```

### 7.2 PR 템플릿

`.github/pull_request_template.md`

```markdown
## 요약
- 무엇을, 왜 변경했나요?

## 테스트 방법
- [ ] 빌드 통과
- [ ] 단위 테스트 통과
- [ ] 수동 테스트 시나리오 기재

## 관련 이슈
Closes #123

## 체크리스트
- [ ] 린트/포맷 적용
- [ ] 문서/주석 업데이트
```

---

## 8) Git 훅(hook) & 사전 검증

### 8.1 pre-commit 훅(예: main 직접 커밋 방지)

`.git/hooks/pre-commit` (실행 권한 부여)

```bash
#!/usr/bin/env bash
BRANCH=$(git rev-parse --abbrev-ref HEAD)
if [ "$BRANCH" = "main" ]; then
  echo "❌ main 에 직접 커밋할 수 없습니다. 기능 브랜치를 사용하세요." >&2
  exit 1
fi
```

### 8.2 린트/포맷 자동화

- `pre-commit` 프레임워크 또는 npm 스크립트로 린트/포맷 실행
- 예: `pre-commit`에서 `black`, `eslint --fix`, `clang-format` 등

---

## 9) 대용량/바이너리 파일 — Git LFS

```bash
git lfs install
# 예: 오디오/모델 파일 추적
git lfs track "*.wav"
git lfs track "*.onnx"
# .gitattributes 에 규칙 추가됨
git add .gitattributes && git commit -m "chore: track large assets"
```

> LFS 미설정 상태로 100MB+ 푸시는 GitHub에서 거절됩니다.

---

## 10) 서브모듈/서브트리 (필요 시)

- **Submodule**: 외부 리포를 특정 커밋에 고정. 버전 고정엔 좋지만 워크플로가 복잡
- **Subtree**: 외부 코드를 소스에 병합하는 방식. 단일 리포 경험 유지

초기화/업데이트 예시(submodule)

```bash
git submodule add https://github.com/lib/lib.git external/lib
git submodule update --init --recursive
```

---

## 11) 릴리스 & 태깅 & 체인지로그

- 태그: `v1.2.3` (SemVer)

```bash
git tag -a v1.0.0 -m "First release"
git push origin v1.0.0
```

- 체인지로그: Conventional Commits 기반 자동 생성 도구(예: `conventional-changelog`, `release-please`) 사용 권장

---

## 12) 트러블슈팅

- **non-fast-forward**: 원격에 내게 없는 커밋 존재 →

```bash
git pull --rebase origin main && git push
```

- **refspec does not match any**: 커밋 없음/브랜치 오타 → 첫 커밋 후 재시도
- **Wrong account**: 잘못된 자격 증명 → HTTPS 자격 삭제/SSH config 확인
- **CI 실패로 머지 불가**: 로컬에서 동일 스크립트로 재현, 실패 테스트 수정

---

## 13) 보너스 — includeIf로 디렉터리별 사용자 정보

`~/.gitconfig`

```ini
[user]
  name = your-personal-name
  email = personal@example.com

[includeIf "gitdir:~/work/company/"]
  path = ~/.gitconfig-company
```

`~/.gitconfig-company`

```ini
[user]
  name = your-company-name
  email = you@company.com
```

---

## 14) A/B 시나리오 전체 예 (로그인·회원가입 동시 개발)

**A — 로그인**

```bash
git checkout main && git pull origin main
git checkout -b feat/login
# 구현 → 테스트 → 커밋
git add . && git commit -m "feat(login): add form and API"
git fetch origin && git rebase origin/main
# 충돌 해결 시 add/continue
git push -u origin feat/login
# PR 생성 (B를 리뷰어로)
```

**B — 회원가입**

```bash
git checkout main && git pull origin main
git checkout -b feat/signup
# 구현 → 테스트 → 커밋
git add . && git commit -m "feat(signup): email validation"
git fetch origin && git rebase origin/main
git push -u origin feat/signup
# PR 생성 (A를 리뷰어로)
```

**리뷰 & 머지**

- 서로의 PR 리뷰 → 수정을 요청/승인
- CI 통과 & 승인 완료 → **Squash and merge**
- 브랜치 정리(로컬/원격 삭제)

---

## 15) 체크리스트 (붙여놓고 쓰기)

-

---

필요하면 **GitHub Actions 예시(CI)**, **VS Code 머지툴 스크린샷 가이드**, **pre-commit 설정 파일**도 추가해 드립니다. 어느 부분을 자동화하고 싶은지 알려 주세요.

