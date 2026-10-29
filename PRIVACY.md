# 개인정보 처리방침 (Privacy Policy)

**develax-starter** Claude Code 플러그인 · 시행일 2026-10-23 · 운영 DEVELAX

[English version below](#english)

## 요약

- 이 플러그인은 Claude에게 주는 작업 설명서(SKILL.md)와 템플릿(HTML·CSS·마크다운)으로만 이루어져 있습니다. 실행 코드, 서버, MCP 서버, 분석 도구가 없습니다.
- **DEVELAX는 어떤 데이터도 수집·전송받거나 보관하지 않습니다.**
- 사용자가 입력한 내용은 사용자의 컴퓨터와 **사용자 본인의** GitHub 계정(그리고 사용자가 고른 경우 Cloudflare 계정)에만 저장됩니다.

## 플러그인이 다루는 정보

| 정보 | 어디서 쓰이나 | 저장 위치 |
|---|---|---|
| 홈페이지에 넣을 내용(이름, 소개, 연락처 등 사용자가 직접 입력한 것) | `/launch` 가 `index.html` 을 만들 때 | 사용자 컴퓨터의 작업 폴더 → 사용자의 GitHub 저장소 |
| Git 이름·이메일 | `/setup-check` 가 확인하고, 비어 있으면 사용자가 알려준 값으로 설정 | 사용자 컴퓨터의 git 설정, 이후 커밋 기록 |
| GitHub 로그인 상태·아이디 | `/setup-check`, `/save`, `/deploy-pages`, `/deploy-cloudflare` 가 `gh` 명령으로 확인 | 저장하지 않음(GitHub CLI가 관리) |
| 작업 폴더의 파일 | `/save`, `/deploy-pages`, `/deploy-cloudflare` 가 올릴 때 | 사용자의 GitHub 저장소 |
| 포트폴리오 원본 기록(프로젝트 문서, 메모, 캡처, 채용공고, 경력 정보) | `/portfolio`, `/portfolio-project`, `/portfolio-build`, `/portfolio-review` 가 정리·제작·검증할 때 | 사용자 컴퓨터의 포트폴리오 폴더. `/save` 시 비공개 저장소. 공개 배포는 `site/` 폴더만 |

토큰이나 비밀번호는 묻지 않으며 파일에 저장하지 않습니다.

## 데이터가 전송되는 곳

플러그인은 사용자가 요청할 때만, 사용자의 계정으로 다음 서비스에 데이터를 보냅니다. 각 서비스의 처리는 해당 서비스의 개인정보 처리방침을 따릅니다.

- **GitHub** (`git push`, `gh repo create`, `gh api`): 저장소 보관과 GitHub Pages 공개. `/save` 는 기본 **비공개** 저장소를 만듭니다. GitHub Pages는 공개 저장소가 필요하므로, 공개 전에 "파일 전부가 공개된다"고 알리고 동의를 받습니다. `/deploy-cloudflare` 는 저장소를 만들기 전에 공개 여부를 묻습니다. [GitHub 개인정보 처리방침](https://docs.github.com/site-policy/privacy-policies/github-general-privacy-statement)
- **Cloudflare Pages**: `/deploy-cloudflare` 를 쓸 때, 사용자가 Cloudflare 대시보드에서 직접 GitHub 저장소를 연결합니다. 플러그인이 Cloudflare로 직접 보내는 데이터는 없습니다. [Cloudflare 개인정보 처리방침](https://www.cloudflare.com/privacypolicy/)
- **Anthropic (Claude)**: 대화 내용은 Claude 사용에 따라 Anthropic이 처리합니다. 이 플러그인이 추가로 보내는 것은 없습니다. [Anthropic 개인정보 처리방침](https://www.anthropic.com/legal/privacy)

## 공개되는 정보 주의

GitHub Pages나 Cloudflare Pages로 공개한 홈페이지는 누구나 볼 수 있습니다. 플러그인은 올리기 전에 파일 목록을 보여주고, 전화번호·집 주소·주민번호나 `.env`·`*.key` 같은 비밀 파일이 있으면 경고합니다. 연락처는 이메일이나 SNS만 넣기를 권합니다.

## 보관과 삭제

DEVELAX는 데이터를 보관하지 않으므로 삭제 요청할 데이터도 없습니다. 올린 내용을 지우려면 사용자의 GitHub 저장소(또는 Cloudflare 프로젝트)를 직접 삭제하면 됩니다.

## 아동

이 플러그인은 18세 미만을 대상으로 하지 않습니다.

## 변경

이 방침이 바뀌면 이 파일을 수정하고 시행일을 갱신합니다. 변경 기록은 저장소 커밋 기록에서 볼 수 있습니다.

## 문의

- 이메일: develax@develrocket.com
- [GitHub Issues](https://github.com/develax-official/develax-starter/issues/new/choose)

---

<a id="english"></a>

## English

**develax-starter** Claude Code plugin · Effective 2026-10-23 · Operated by DEVELAX

- The plugin consists only of instruction files (SKILL.md) and templates (HTML, CSS, Markdown) for Claude. It ships no executable code, servers, MCP servers, or analytics.
- **DEVELAX does not collect, receive, or retain any data.**
- What the user types stays on the user's computer and in the user's **own** GitHub account (and Cloudflare account, if the user chooses it).

**Data handled.** Homepage content the user provides to `/launch` (which may include their name and contact details); the git name and email that `/setup-check` reads and, if empty, sets to values the user gives; GitHub sign-in status checked through `gh`; and the files in the working folder that `/save`, `/deploy-pages`, and `/deploy-cloudflare` upload; and portfolio source records (project documents, notes, screenshots, job postings, career details) that the `/portfolio` skills organize locally, where only the `site/` folder is ever published and the rest is kept on the user's computer or in a private repository. The plugin never asks for or stores tokens or passwords.

**Where data goes.** Only at the user's request and under the user's own account: **GitHub** (`git push`, `gh repo create`, `gh api`) for storage and GitHub Pages hosting. `/save` creates private repositories by default, and the plugin asks before anything is made public. **Cloudflare Pages**, connected by the user in the Cloudflare dashboard; the plugin sends nothing to Cloudflare directly. **Anthropic** processes the conversation as part of using Claude; the plugin sends nothing extra. Each service's own privacy policy applies.

**Public content.** Sites published to GitHub Pages or Cloudflare Pages are public. Before uploading, the plugin lists the files and warns about phone numbers, home addresses, national ID numbers, and secret files such as `.env` or `*.key`.

**Retention and deletion.** DEVELAX retains nothing. To remove uploaded content, delete the GitHub repository or Cloudflare project.

**Children.** Not intended for users under 18.

**Contact.** develax@develrocket.com · [GitHub Issues](https://github.com/develax-official/develax-starter/issues/new/choose)
