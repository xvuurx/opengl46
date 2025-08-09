# Git Push 완전 가이드 (쉬운 설명)

## 1. `git push`란 무엇인가?

`git push`는 **내 컴퓨터(로컬)의 변경 사항을 GitHub(원격 저장소)에 올리는 명령**입니다.

- 로컬에서 만든 **새 커밋**이 원격 저장소에 복사됩니다.
- 업로드할 때는 **브랜치 이름**을 지정해야 합니다.

기본 문법:

```bash
git push <원격이름> <로컬브랜치>:<원격브랜치>
```

예시:

```bash
git push origin main
```

- `origin` → 원격 저장소 별칭
- `main` → 브랜치 이름

---

## 2. 첫 푸시와 업스트림 설정

처음 푸시할 때는 보통 이렇게 합니다:

```bash
git push -u origin main
```

- `-u` 또는 `--set-upstream`: "이 브랜치의 기본 원격 브랜치"를 기억하도록 설정
- 한 번 설정하면 다음부터는 그냥 `git push`만 입력해도 됩니다.

업스트림 확인:

```bash
git branch -vv
```

업스트림 변경:

```bash
git branch --set-upstream-to=origin/main
```

---

## 3. 브랜치별 푸시 시나리오

- **새 브랜치 만들고 원격에 올리기**

```bash
git checkout -b feature/login
git push -u origin feature/login
```

- **브랜치 이름 변경 후 원격에도 적용**

```bash
git branch -m old-name new-name
# 원격의 옛 브랜치 삭제
git push origin :old-name
# 새 브랜치 업로드
git push -u origin new-name
```

- **다른 이름으로 푸시**

```bash
git push origin local-branch:remote-branch
```

---

## 4. 자주 쓰는 옵션

- `-u` : 업스트림 설정
- `--force` : 원격 이력 덮어쓰기(위험)
- `--force-with-lease` : 더 안전한 강제 푸시(원격 변경 시 거부)
- `--dry-run` : 실제로는 올리지 않고 미리보기
- `--tags` : 모든 태그 올리기
- `--follow-tags` : 연결된 태그만 올리기
- `--delete` : 원격 브랜치 삭제

```bash
git push origin --delete branch-name
```

---

## 5. 기본 동작 모드 설정

`push.default`는 푸시 방식의 기본 동작을 정합니다.

```bash
git config --global push.default simple
```

- `simple`(권장): 현재 브랜치를 같은 이름의 업스트림 브랜치로만 푸시
- `current`: 현재 브랜치를 같은 이름의 원격 브랜치로 푸시

---

## 6. 인증 방식

- **HTTPS**: GitHub 로그인 시 비밀번호 대신 **PAT(토큰)** 필요
- **SSH**: SSH 키 등록 후 비밀번호 없이 푸시 가능

---

## 7. 자주 발생하는 오류와 해결

- ``** / **``
  - 원격에 내가 없는 커밋이 있을 때
  - 해결:
    ```bash
    git pull --rebase origin main
    git push
    ```
- ``
  - 커밋이 없거나 브랜치 이름 오타 → 커밋 만들고 재시도
- **대용량 파일 에러**
  - 100MB 초과 파일은 GitHub에서 거부 → Git LFS 사용
- **보호된 브랜치**
  - 직접 푸시 금지 → Pull Request로 병합

---

## 8. 푸시 후 확인 방법

- 로컬에만 있는 커밋:

```bash
git log origin/main..HEAD --oneline
```

- 원격에만 있는 커밋:

```bash
git log HEAD..origin/main --oneline
```

- 원격 브랜치 목록:

```bash
git ls-remote origin
```

---

## 9. 태그 푸시

```bash
git push origin v1.0.0      # 특정 태그
git push --follow-tags       # 커밋에 연결된 태그
git push --tags              # 모든 태그
```

---

## 10. 실전 예시 모음

```bash
git checkout -b feature/x
git push -u origin feature/x   # 새 브랜치 올리기
git pull --rebase && git push   # 최신 상태 반영 후 푸시
git push --force-with-lease     # 안전 강제 푸시
git push origin --delete x      # 원격 브랜치 삭제
git push --dry-run              # 미리보기
```

> 강제 푸시 시에는 항상 팀원과 상의하고, 가능하면 `--force-with-lease`를 사용하세요.

