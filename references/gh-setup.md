# gh CLI 설정 가이드

이 스킬은 PR 정보를 자동으로 가져오기 위해 GitHub CLI(gh)를 사용합니다.

## 설치

**macOS**
```bash
brew install gh
```

**Linux (apt)**
```bash
sudo apt install gh
```

**Windows**
```bash
winget install GitHub.cli
```

## 인증

```bash
gh auth login
# → GitHub.com 선택 → HTTPS → 브라우저 인증
```

## 확인

```bash
gh auth status   # 인증 계정 확인
gh pr view       # 현재 브랜치의 PR 조회 테스트
```
