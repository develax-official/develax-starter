---
name: setup-check
description: 바이브코딩 환경 점검. 사용자가 "/setup-check", "환경 점검", "세팅 확인", "설치 됐는지 봐줘", "git 있어?", "github 로그인 됐어?" 라고 하거나, 다른 스킬이 사전 점검을 요구할 때 사용. git, GitHub CLI(gh), GitHub 로그인, git 사용자 정보를 확인하고 결과를 표로 보고한다. 설치는 하지 않고 Develog "바이브코딩 환경 세팅" 시리즈의 해당 편으로 안내한다.
---

# setup-check: 바이브코딩 환경 점검

이 스킬은 **확인만** 합니다. 무엇도 설치하지 않습니다. 비밀번호를 묻는 작업(sudo, 시스템 설치)은 절대 실행하지 않습니다.
사용자는 비개발자입니다. 모든 보고는 한국어로, 용어는 쉽게, 결과는 표로.

## 1. 점검 실행

아래 명령을 각각 실행합니다. 실패해도 멈추지 말고 전부 실행한 뒤 한 번에 보고합니다.

| 항목 | 명령 | 통과 기준 |
|---|---|---|
| 운영체제 | `uname -s` (Windows면 `ver`) | 정보용 |
| Git | `git --version` | 버전이 출력됨 |
| GitHub CLI | `gh --version` | 버전이 출력됨 |
| GitHub 로그인 | `gh auth status` | "Logged in to github.com" 포함 |
| Git 이름 | `git config --global user.name` | 비어 있지 않음 |
| Git 이메일 | `git config --global user.email` | 비어 있지 않음 |

Mac에서 `git --version` 실행 시 "command line developer tools" 설치 창이 뜰 수 있습니다. 그 경우 사용자에게 **설치 창에서 "설치"를 누르라고** 안내하고, 끝나면 다시 `/setup-check` 하라고 말합니다.

## 2. 결과 보고

아래 형식으로 표를 출력합니다. 통과는 ✅, 실패는 ❌, 확인 불가이거나 이 자리에서 바로 고칠 수 있는 항목(이름·이메일 비어 있음)은 ⚠️.

```
| 항목 | 상태 | 다음 할 일 |
|---|---|---|
| Git | ✅ 2.x | 없음 |
| GitHub CLI | ❌ 없음 | Develog "바이브코딩 환경 세팅" 3편(https://develog.develrocket.com/@web7722/vibe-setup-3-github-cli)을 따라 설치 후 다시 /setup-check |
| GitHub 로그인 | ❌ | GitHub CLI 설치 후 자동으로 안내됩니다 |
| Git 이름/이메일 | ⚠️ 비어 있음 | 아래에서 바로 설정해 드릴 수 있습니다 |
```

표 아래에 한 줄 결론: **"환경 세팅 완료"** 또는 **"n개 항목이 남았습니다"**.

## 3. 실패 항목별 대응

### Git 없음
- 설치하지 않습니다. Develog "바이브코딩 환경 세팅" 시리즈의 Git 설치 편(Mac/Windows)을 따라 하라고 안내합니다: https://develog.develrocket.com/@web7722/vibe-setup-2-git
- Mac: `xcode-select --install` 을 **사용자가 직접** 터미널에서 실행하거나, 설치 창이 뜨면 "설치"를 누르라고 안내.
- Windows: https://git-scm.com/download/win 에서 설치 파일을 받아 전부 "Next"로 설치하라고 안내.

### GitHub CLI 없음
- 설치하지 않습니다. Develog "바이브코딩 환경 세팅" 시리즈의 GitHub CLI 편을 안내합니다: https://develog.develrocket.com/@web7722/vibe-setup-3-github-cli
- Mac: https://github.com/cli/cli/releases/latest 에서 `.pkg` 파일(Apple Silicon은 arm64, Intel은 amd64)을 받아 더블클릭 설치. Homebrew는 권하지 않습니다.
- Windows: PowerShell에서 `winget install --id GitHub.cli` 또는 위 releases 페이지의 `.msi`.
- Apple Silicon인지 Intel인지 모르면 `uname -m` 결과로 알려줍니다 (arm64 → Apple Silicon, x86_64 → Intel).

### GitHub 로그인 안 됨 (gh는 있음)
- 이 항목만은 **바로 도와줍니다**. 사용자에게 "지금 로그인할까요?" 물어보고, 동의하면 실행:
  `gh auth login --hostname github.com --git-protocol https --web`
- 화면에 나오는 **8자리 코드(XXXX-XXXX)** 를 사용자에게 크게 보여주고, 브라우저가 열리면 GitHub에 로그인한 뒤 그 코드를 입력하라고 안내합니다.
- 브라우저가 자동으로 안 열리면 https://github.com/login/device 를 직접 열라고 안내합니다.
- 끝나면 `gh auth status` 로 다시 확인합니다.

### Git 이름/이메일 비어 있음
- 사용자에게 이름(영문 또는 한글, 아무거나)과 GitHub 가입 이메일을 묻습니다.
- 답하면 실행: `git config --global user.name "<이름>"`, `git config --global user.email "<이메일>"`
- 이 값은 "누가 고쳤는지" 기록에만 쓰인다고 설명합니다.

## 4. 끝맺음

모두 ✅이면:
> 환경 세팅 완료. 이제 `/save` 로 작업을 보관하거나, `/deploy-pages` 또는 `/deploy-cloudflare` 로 홈페이지를 올릴 수 있습니다. 처음부터 끝까지 한 번에 하려면 `/launch`.

하나라도 ❌면 Develog 시리즈의 어느 편을 보면 되는지 한 줄로 요약하고(시리즈 입구: https://develog.develrocket.com/@web7722/vibe-setup-1-claude-desktop), 끝나면 다시 `/setup-check` 하라고 안내합니다.
