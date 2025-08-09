# Git 초기 설정 및 사용 가이드

## 1. 사전 준비 (한 번만 설정)

- **Git 설치**: [Git 공식 사이트](https://git-scm.com/)에서 OS에 맞는 버전을 설치합니다.
- **커밋 작성자 정보 등록** (최초 1회)

```bash
git config --global user.name "홍길동"
git config --global user.email "you@example.com"
```

- `--global` 옵션은 **PC 전체 모든 저장소에 적용**됩니다.
- 이 정보는 **커밋 작성자**를 식별하는 데 사용되며, GitHub 로그인 계정과는 별개입니다.

---

## 2. 명령어별 상세 설명

### `echo "# opengl46" >> README.md`

- `README.md` 파일의 끝에 `# opengl46` 한 줄을 추가합니다.
- 파일이 없으면 새로 생성합니다.
- `>>` : 덧붙이기(append), `>` : 덮어쓰기(overwrite)
- 결과: `README.md`에 마크다운 제목 한 줄이 추가됨.

---

### `git init`

- 현재 폴더를 **새 Git 저장소**로 초기화합니다.
- `.git` 폴더가 생성되며, 여기서 버전 이력과 브랜치 정보가 관리됩니다.
- 원격 저장소(GitHub)와는 아직 연결되지 않은 상태입니다.
- 기본 브랜치 이름은 Git 버전/설정에 따라 `master` 또는 `main`일 수 있습니다.

---

### `git add README.md`

- `README.md`를 **스테이징 영역**에 추가합니다.
- 스테이징 영역: 다음 커밋에 포함할 변경 사항을 등록하는 곳.
- 여러 파일을 한 번에 추가하려면:

```bash
git add .
```

---

### `git commit -m "first commit"`

- 스테이징한 변경 사항을 커밋(스냅샷)으로 저장합니다.
- `-m` 뒤에 커밋 메시지를 작성합니다.
- 이 시점에서 로컬 저장소에 첫 번째 버전 이력이 기록됩니다.

---

### `git branch -M main`

- 현재 브랜치 이름을 강제로 `main`으로 변경합니다.
- `-M` 옵션은 같은 이름의 브랜치가 이미 있어도 덮어씁니다.
- GitHub의 기본 브랜치 명칭(`main`)과 일치시키는 데 사용됩니다.

---

### `git remote add origin https://github.com/xvuurx/opengl46.git`

- 로컬 저장소에 **원격 저장소 별칭** `origin`을 추가합니다.
- `origin`이라는 이름으로 GitHub의 해당 URL과 연결됩니다.
- 이후 `git push origin main` 또는 `git pull origin main`처럼 사용 가능합니다.

---

### `git push -u origin main`

- 로컬의 `main` 브랜치를 원격 `origin`의 `main` 브랜치로 처음 푸시합니다.
- `-u` (`--set-upstream`) 옵션은 업스트림을 설정하여, 이후 `git push` 또는 `git pull` 명령만으로 기본 원격/브랜치를 자동 인식하게 합니다.
- 최초 푸시 시 GitHub 인증을 요구할 수 있습니다.
- HTTPS 사용 시 비밀번호 대신 \*\*Personal Access Token (PAT)\*\*을 비밀번호 입력란에 사용해야 합니다.

---

## 3. 계정 설정 방법

### (1) 글로벌 계정 설정 (모든 저장소 기본값)

```bash
git config --global user.name "메인계정이름"
git config --global user.email "main@example.com"
```

- PC의 모든 저장소에서 기본적으로 사용됩니다.
- 다중 계정 환경에서는 로컬 설정으로 덮어쓸 수 있습니다.

---

### (2) 로컬 계정 설정 (특정 저장소 전용)

저장소 디렉토리 안에서:

```bash
git config user.name "다른계정이름"
git config user.email "other@example.com"
```

- `--global` 옵션을 생략하면 현재 저장소의 `.git/config` 파일에만 저장됩니다.
- 글로벌 설정보다 **로컬 설정이 우선 적용**됩니다.

---

### (3) 설정 확인

```bash
git config --show-origin user.name
git config --show-origin user.email
```

- 설정된 값과, 해당 값이 저장된 위치(`global` 또는 `local`)를 경로와 함께 표시합니다.

---

## 4. 참고 사항

- **글로벌 설정**: 한 번 설정하면 PC 전체에 적용되며, 보통 개인 계정에 사용.
- **로컬 설정**: 특정 프로젝트나 회사 계정 등 다중 계정 환경에서 필수.
- **업스트림 설정**: `git push -u`를 한 번 하면, 이후 `git push`만으로 동일 브랜치에 푸시 가능.
- **Personal Access Token (PAT)**: 2021년 이후 GitHub는 HTTPS 인증 시 비밀번호 대신 토큰 사용을 요구합니다.

