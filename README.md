<p align="center">
  <img src="assets/develax-logo.png" alt="DEVELAX" width="320">
</p>

<h1 align="center">develax-starter</h1>

<p align="center">
  <b>"홈페이지 만들어서 올려줘" 한 마디면, 만들기부터 인터넷 공개까지.</b><br>
  비개발자를 위한 디벨AX 바이브코딩 Claude Code 플러그인
</p>

<p align="center">
  <i>A Claude Code plugin by DEVELAX that lets non-developers build a homepage and publish it to GitHub Pages in one conversation (Korean).</i>
</p>

<p align="center">
  <a href="https://github.com/develax-official/develax-starter/releases"><img alt="version" src="https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Fdevelax-official%2Fdevelax-starter%2Fmain%2F.claude-plugin%2Fplugin.json&query=%24.version&label=version&color=004FFF"></a>
  <a href="LICENSE"><img alt="license" src="https://img.shields.io/badge/license-MIT-004FFF"></a>
  <a href="https://develax.develrocket.com"><img alt="DEVELAX" src="https://img.shields.io/badge/by-%EB%94%94%EB%B2%A8AX-030302"></a>
</p>

## 설치

Claude 데스크톱 앱의 **Code 탭**(또는 Claude Code CLI) 입력창에 두 줄을 차례로 입력합니다.

```
/plugin marketplace add develax-official/develax-starter
```

```
/plugin install develax-starter@develax
```

두 번째 명령 뒤에 설치 범위를 묻는 창이 뜨면 **user**를 고르면 됩니다. 설치가 끝나면 아무 폴더에서나 `/launch`라고 입력해 보세요.

## 스킬 5개

| 명령 | 하는 일 | 이럴 때 |
|---|---|---|
| `/launch` | 질문 3개 → 홈페이지 생성 → 미리보기·수정 → GitHub 보관 → GitHub Pages 공개 → 주소 전달 | 처음 홈페이지를 만들 때. "홈페이지 만들어서 올려줘" |
| `/setup-check` | git, GitHub CLI, GitHub 로그인, git 이름·이메일을 점검해 표로 알려 줌 | 시작 전, 또는 뭔가 안 될 때. "환경 점검해줘" |
| `/save` | 지금 폴더를 GitHub 저장소에 보관(기본 비공개). 기록 보기, 되돌리기, 다른 컴퓨터에서 받기 | 작업을 백업하고 싶을 때. "저장해줘", "되돌려줘" |
| `/deploy-pages` | GitHub Pages로 공개. 저장소 생성부터 Pages 켜기까지 자동 | 수정한 홈페이지를 다시 올릴 때. "배포해줘" |
| `/deploy-cloudflare` | Cloudflare Pages로 공개. GitHub까지 자동, Cloudflare 연결은 화면 안내 | 비공개 저장소로 공개하거나 내 도메인을 붙일 때 |

명령어를 외우지 않아도 됩니다. "저장해줘", "배포해줘"처럼 말로 해도 Claude가 맞는 스킬을 고릅니다.

## 시작하기 전에 필요한 것

- Claude 데스크톱 앱(Code 탭)
- Git 설치
- GitHub 계정과 GitHub CLI(`gh`) 로그인

설치 방법은 Develog **바이브코딩 환경 세팅** 시리즈에 화면과 함께 정리되어 있습니다.

- [1편: Claude 데스크톱 설치와 Code 탭](https://develog.develrocket.com/@web7722/vibe-setup-1-claude-desktop)
- [시리즈 전체 보기](https://develog.develrocket.com/@web7722/topic/vibe-coding-setup)

준비가 됐는지 모르겠다면 `/setup-check`를 먼저 실행하세요. 빠진 것이 있으면 시리즈의 해당 편으로 안내합니다.

## 업데이트

새 버전이 나오면 아래 명령으로 받은 뒤, `/reload-plugins`를 입력하거나 새 세션을 열면 적용됩니다.

```
/plugin marketplace update develax
```

변경 내용은 [Releases](https://github.com/develax-official/develax-starter/releases)에서 볼 수 있습니다.

## 안전 원칙

이 플러그인은 코드 없이 Claude에게 주는 작업 설명서(SKILL.md) 5개로만 이루어져 있어, 무엇을 하는지 누구나 [`skills/`](skills/) 폴더에서 직접 읽어 볼 수 있습니다. 모든 스킬은 다음을 지킵니다.

- 프로그램을 **설치하지 않습니다**. 설치가 필요하면 안내 글로 연결합니다.
- 비밀번호 입력이나 `sudo`를 **요구하지 않습니다**.
- 토큰이나 비밀번호를 **파일에 저장하지 않습니다**.
- 기록을 지우는 `reset --hard`, `push --force`를 **쓰지 않습니다**. 되돌리기는 항상 새 기록으로 남깁니다.
- 저장소를 공개로 만들거나 공개 전환할 때는 **먼저 묻습니다**.

DEVELAX는 어떤 데이터도 수집하지 않습니다. 입력한 내용은 내 컴퓨터와 내 GitHub(선택 시 Cloudflare) 계정에만 저장됩니다. 자세한 내용은 [개인정보 처리방침](PRIVACY.md)을 보세요.

## 문제 신고와 문의

- 설치나 실행이 안 되면 [Issues](https://github.com/develax-official/develax-starter/issues/new/choose)에 남겨 주세요. 어떤 명령을 입력했고 어떤 화면이 나왔는지 적어 주시면 빠르게 확인할 수 있습니다.
- 이메일: develax@develrocket.com
- 플러그인을 만든 배경: [Develog 소개 글](https://develog.develrocket.com/@web7722/develax-starter-plugin)
- 디벨AX: https://develax.develrocket.com

## 라이선스

[MIT](LICENSE) © DEVELAX
