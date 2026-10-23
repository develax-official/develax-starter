# develax-starter

디벨AX 바이브코딩 교재 시리즈가 공통으로 쓰는 Claude Code 플러그인입니다. 비개발자 수강생이 Claude Code에서 "홈페이지 만들어서 올려줘" 한 마디로 홈페이지를 만들고 GitHub Pages에 공개할 수 있게 해 줍니다. 코드는 없고, Claude에게 주는 작업 설명서(SKILL.md) 5개로 이루어져 있습니다.

## 설치

Claude 데스크톱 Code 탭(또는 Claude Code CLI)에서 두 줄을 차례로 입력합니다.

```
/plugin marketplace add develax-official/develax-starter
/plugin install develax-starter@develax
```

환경 세팅(Claude 데스크톱, Git, GitHub CLI 로그인)은 Develog "바이브코딩 환경 세팅" 시리즈를 따릅니다: https://develog.develrocket.com/@web7722/topic/vibe-coding-setup

## 스킬 5개

| 스킬 | 하는 일 |
|---|---|
| `/launch` | 홈페이지 만들기부터 GitHub Pages 공개까지 한 번에. 질문 3개 → index.html → 미리보기·수정 → 보관 → 배포 → 주소 |
| `/setup-check` | git, GitHub CLI, GitHub 로그인, git 이름·이메일 점검. 설치는 하지 않고 Develog 시리즈로 안내 |
| `/save` | 현재 폴더를 GitHub 저장소에 보관(기본 비공개). 기록 보기, 되돌리기, 다른 컴퓨터에서 받기 |
| `/deploy-pages` | GitHub Pages 배포. 저장소 생성부터 Pages 활성화까지 자동 |
| `/deploy-cloudflare` | Cloudflare Pages 배포. GitHub까지 자동, Cloudflare 연결은 대시보드 안내 |

모든 스킬은 한국어로 짧게 안내하고, 비밀번호·sudo 입력, 토큰 파일 저장, `reset --hard`, `push --force`를 쓰지 않습니다.

## 업데이트

```
/plugin marketplace update develax
```

이후 `/reload-plugins` 또는 새 세션에서 적용됩니다.

## 더 알아보기

- 플러그인 소개와 설계 배경: https://develog.develrocket.com/@web7722/develax-starter-plugin
- 문의: dev@kiweb.or.kr

## 라이선스

MIT
