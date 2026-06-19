# Git — 기본 명령부터 브랜치 전략, 자주 만나는 문제 해결

Git을 사용하며 정리한 기본 명령, 푸시가 안 될 때의 진단, `origin` 충돌 해결, 브랜치 전략을 정리했습니다.

## 1. 기본 명령

처음 레포지토리에 올릴 때:

```bash
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/yeotaeho/ifrsseed.git
git push -u origin main
```

이미 있던 레포지토리에 넣을 때:

```bash
git add .
git commit -m "아무거나 적어도 상관없음"
git push
```

## 2. 깃허브에 안 올라갈 때 — 진단

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251015160106.png]]

커밋만 있고 푸시가 제대로 안 된 경우, `git status`로 상태를 확인합니다.

- `Changes not staged for commit` 또는 `Untracked files` → 파일이 제대로 커밋되지 않은 상태
- `Your branch is ahead of 'origin/main' by X commits` → 커밋은 됐지만 아직 푸시가 안 된 상태

**커밋이 안 된 경우**: 현재 폴더의 모든 변경 파일을 추가하고 커밋·푸시합니다(`main`은 브랜치 이름에 맞게).

```bash
git add .
git commit -m "Add project files"
git push origin main
```

**푸시만 안 된 경우**: 폴더 경로를 설정한 뒤 진행합니다.

```bash
cd C:\Users\hi\Documents\project\Spring
```

## 3. `remote origin already exists` 해결

`error: remote origin already exists.`가 뜨면, 기존 `origin`의 주소를 새 레포로 바꾸는 방법이 깔끔합니다.

```bash
git remote set-url origin https://github.com/yeotaeho/Spring-boot.git
git push -u origin main
```

## 4. 브랜치 전략

| 브랜치 | 용도 |
| --- | --- |
| `main` | 운영 배포 |
| `develop` | 통합 테스트 |
| `feature/*` | 기능 개발 |
| `hotfix/*` | 긴급 수정 |
| `release` | 배포 전 버전(배포됐던 것들) |

## 5. 주의 — pnpm / node_modules는 올라가지 않는다

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251121112042.png]]

Git에는 `pnpm`으로 설치한 `node_modules` 등이 올라가지 않습니다. 그래서 pull하면 `node_modules`가 설치되지 않은 상태로 받아지므로, **항상 pull 후 설치를 다시 해 줘야** 합니다.

특정 브랜치만 클론하려면 다음을 사용합니다.

```bash
git clone -b [브랜치명] --single-branch [저장소 URL]
# 예: git clone -b sadf --single-branch https://github.com/yeotaeho/ESG.git
```
