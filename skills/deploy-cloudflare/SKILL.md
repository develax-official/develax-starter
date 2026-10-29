---
name: deploy-cloudflare
description: 현재 폴더의 정적 홈페이지(index.html)를 GitHub 저장소에 올리고 Cloudflare(Workers 및 Pages)로 배포한다. 비공개 저장소도 되고 내 도메인 연결이 쉽다. 사용자가 "/deploy-cloudflare", "Cloudflare로 배포해줘", "Cloudflare에 올려줘", "workers.dev 주소 만들어줘", "pages.dev 주소 만들어줘" 라고 할 때, 또는 DEPLOY.md에 Cloudflare가 적힌 폴더에서 "수정한 거 올려줘", "배포해줘" 라고 할 때 사용. 첫 배포는 저장소 생성 + Cloudflare 대시보드 연결 안내(한국어 메뉴 기준)까지, 이후 배포는 commit + push만. GitHub Pages로 올리려면 /deploy-pages.
---

# deploy-cloudflare: 홈페이지를 GitHub + Cloudflare에 올리기

대상 사용자는 비개발자입니다. 한국어로, 짧게, 다음에 뭘 누르면 되는지만 말합니다.
이 스킬은 **GitHub까지는 자동**, **Cloudflare는 안내**합니다. Cloudflare의 GitHub 연결은 대시보드에서만 할 수 있기 때문입니다.
토큰이나 비밀번호를 파일에 저장하지 않습니다. sudo가 필요한 명령은 실행하지 않습니다.

## 주소 규칙 (가장 중요)

**사이트 주소를 추측해서 말하지 않습니다.** Cloudflare 주소는 `https://<프로젝트 이름>.<계정마다 다른 서브도메인>.workers.dev` 형식이라 계정 서브도메인을 Claude가 알 수 없습니다. 예전 방식(Pages)이면 `https://<이름>.pages.dev` 이고 이름이 겹치면 뒤에 글자가 붙습니다. 어느 쪽이든 짐작으로 만든 주소는 틀립니다.

- 주소는 **사용자가 Cloudflare 화면에서 복사해 붙여 준 것만** 씁니다.
- 붙여 준 주소도 `curl -sI <주소>` 로 HTTP 200 을 확인한 뒤에만 "배포 완료"라고 말합니다.
- 확인 전에는 "주소는 배포가 끝나면 Cloudflare 화면에 나옵니다. 복사해서 붙여 주세요"라고만 말합니다. 예시 주소를 실제 주소처럼 보여주지 않습니다.
- 확인한 주소는 `DEPLOY.md` 에 적고, 이후 배포에서는 그 주소만 씁니다.

## 0. 사전 점검

`setup-check` 스킬의 1번(점검 실행)을 먼저 수행합니다. git, gh, GitHub 로그인 중 하나라도 ❌면 **여기서 멈추고** setup-check의 대응 절차로 안내합니다. 배포를 억지로 진행하지 않습니다.

## 1. 폴더 점검

0. 폴더에 `PORTFOLIO.md` 가 있으면 포트폴리오 작업 폴더입니다. 공개 대상은 **`site/` 폴더뿐**이므로 이후 모든 단계(git 포함)를 `site/` 안에서 합니다. 원본 기록(프로젝트 폴더, 채용공고.md, 작업/)은 올리지 않습니다.
1. 현재 폴더에 `index.html` 이 **맨 위**에 있는지 확인합니다. 없으면 어디 있는지 찾아서 알려주고, "맨 위로 옮길까요?" 묻습니다.
2. 폴더 안 파일 목록을 보여주고, 개인정보(전화번호, 주소, 주민번호)나 비밀번호·API 키가 들어 있을 만한 파일(`.env`, `*.key`, `secrets*`)이 있으면 경고하고 올리기 전에 확인을 받습니다.
3. 이미 git 저장소인지 확인합니다: `git rev-parse --is-inside-work-tree`, `git remote -v`.
   - origin 이 이미 있고 `DEPLOY.md` 에 Cloudflare 주소가 있으면 → **5. 이후 배포** 로 갑니다.
   - origin 은 있는데 Cloudflare 연결 기록이 없으면(예: `/save` 로 먼저 보관한 폴더) → 2번의 1·5·6항(파일 준비)만 하고 commit·push 한 뒤 **4번** 으로 갑니다.
   - 없으면 → 2번부터 진행합니다.

## 2. 저장소 준비 (첫 배포)

1. `.gitignore` 가 없으면 아래 내용으로 만듭니다.
   ```
   .DS_Store
   Thumbs.db
   .env
   *.key
   *.pem
   node_modules/
   .wrangler/
   ```
2. 저장소 이름을 정합니다. 기본값은 현재 폴더 이름(영문 소문자, 숫자, `-`만. 한글·공백이면 영문으로 제안). 사용자에게 "저장소 이름은 `my-homepage` 로 할까요? 이 이름이 사이트 주소 앞부분이 됩니다" 한 번 확인합니다. `gh auth status` 에 로그인된 계정이 둘 이상이면 "GitHub 계정이 n개 로그인되어 있습니다. 어느 계정에 올릴까요?" 를 이어서 한 번 묻고, 하나면 묻지 않습니다. 질문 순서는 항상 저장소 이름 → (계정) → 공개 여부입니다.
3. 공개 여부: 기본 **public**. 비공개로 하고 싶다고 하면 `--private`. (Cloudflare는 둘 다 됩니다.)
4. `git init -b main` (이미 저장소면 생략).
5. Cloudflare 설정 파일 `wrangler.jsonc` 를 만듭니다. `name` 은 저장소 이름과 **똑같이** 씁니다(대시보드의 프로젝트 이름과 다르면 배포가 실패합니다).
   ```jsonc
   {
     "name": "<저장소 이름>",
     "compatibility_date": "2026-10-01",
     "assets": { "directory": "." }
   }
   ```
   이 파일이 있으면 Cloudflare가 설정을 자동으로 만들겠다며 저장소에 PR(변경 요청)을 보내는 일이 없고, 바로 배포됩니다.
6. 사이트에 올라가면 안 되는 파일을 빼는 `.assetsignore` 를 만듭니다.
   ```
   .git
   .wrangler
   node_modules
   wrangler.jsonc
   .assetsignore
   .gitignore
   DEPLOY.md
   ```
7. `git add -A`, `git commit -m "첫 배포"`.

## 3. GitHub에 올리기

```
gh repo create <이름> --public --source=. --remote=origin --push
```

- 성공하면 `gh repo view --web` 은 열지 말고, 저장소 주소(`https://github.com/<아이디>/<이름>`)만 출력합니다.
- "already exists" 오류면 이름 뒤에 `-2` 를 붙여 다시 묻고, `wrangler.jsonc` 의 `name` 도 같이 바꿉니다.
- 권한 오류(permission denied, 403)면 `gh auth status` 를 다시 보고 setup-check의 로그인 절차로 안내합니다.

## 4. Cloudflare 연결 안내 (첫 배포)

여기부터는 사용자가 브라우저에서 직접 합니다. 아래 안내를 **그대로** 출력합니다. `<아이디>`, `<이름>` 은 실제 값으로 채웁니다. 메뉴 이름은 한국어 화면 기준이고, 영어 화면이면 괄호 안 이름을 찾으면 됩니다.

> **Cloudflare에서 저장소를 연결합니다 (5분)**
>
> 1. https://dash.cloudflare.com 에 로그인합니다. (계정이 없으면 먼저 가입: https://dash.cloudflare.com/sign-up)
> 2. 왼쪽 사이드 메뉴 **컴퓨트**(Compute) → **Workers 및 Pages**(Workers & Pages) → 오른쪽 위 **애플리케이션 생성**(Create application)
> 3. **Continue with GitHub** 를 누릅니다.
>    - 처음이면 GitHub 창이 열립니다: 계정 `<아이디>` 선택 → **All repositories** → **Install & Authorize**. 비밀번호를 다시 물으면 입력합니다.
>    - 이미 연결해 둔 적이 있으면 GitHub 창 없이 바로 저장소 목록이 나옵니다. 4번으로 갑니다.
> 4. 저장소 목록에서 `<아이디>/<이름>` 을 고릅니다. (검색창에 `<이름>` 입력)
>    - 목록에 없으면: 예전에 "선택한 저장소만" 허용해 둔 경우입니다. 목록 아래 **GitHub 앱 권한 편집**(Edit GitHub app permissions) 같은 링크 → GitHub 화면에서 Repository access 에 `<이름>` 추가 → **Save** → Cloudflare 화면으로 돌아와 새로고침.
> 5. 설정 화면이 나오면 아래처럼 둡니다. **대부분 기본값 그대로**입니다.
>
> | 항목 | 값 |
> |---|---|
> | 프로젝트 이름 (Project name) | `<이름>` ← 저장소 이름과 똑같이. 다르면 배포 실패 |
> | 빌드 명령 (Build command) | 비워 둠 |
> | 배포 명령 (Deploy command) | `npx wrangler deploy` (기본값 그대로) |
> | 그 밖의 항목 (고급 설정 등) | 건드리지 않음 |
>
> 6. **배포**(Deploy)를 누릅니다. 1~2분 기다리면 "성공" 표시와 함께 사이트 주소가 나옵니다. 주소는 보통 `....workers.dev` 로 끝납니다.
> 7. 화면에 나온 **주소를 복사해서** 저에게 붙여넣어 주세요. 열리는지 확인해 드립니다. 주소가 안 보이면 프로젝트 화면의 **방문**(Visit) 버튼이나 **설정 → 도메인 및 경로**(Domains & Routes)에 있습니다.

안내를 출력한 뒤에는 **주소 규칙**대로 사용자가 주소를 붙여 줄 때까지 기다립니다. 주소를 짐작해서 먼저 말하지 않습니다.

사용자가 주소를 주면:
1. `curl -sI <주소>` 로 HTTP 200 을 확인합니다. 200이면 "배포 완료. 이 주소를 사람들에게 알리세요: <주소>" 라고 끝맺습니다.
2. 404 면 순서대로 확인을 안내합니다: (1) 배포가 아직 진행 중인지(1분 뒤 다시), (2) 저장소 맨 위에 `index.html` 이 있는지, (3) 프로젝트 이름과 `wrangler.jsonc` 의 `name` 이 같은지. 빌드 실패면 Cloudflare 프로젝트 화면의 **배포**(Deployments) 탭에서 실패한 줄을 눌러 로그를 캡처해 달라고 합니다.
3. 사용자가 저장소에 Cloudflare가 보낸 PR(제목에 "wrangler" 또는 "Cloudflare")이 생겼다고 하면, `wrangler.jsonc` 가 없는 상태로 연결된 것입니다. 2번 5항의 파일을 만들어 push 하면 다음 배포부터 정상입니다. PR은 닫아도 됩니다.

확인된 주소를 기억해 두기 위해 폴더에 `DEPLOY.md` 를 만들어 배포 방식(Cloudflare Workers), 저장소 주소, 사이트 주소를 적어 둡니다 (사용자에게 알리고 commit/push 합니다).

## 5. 이후 배포 (origin 이 이미 있을 때)

1. `git status` 로 바뀐 파일을 보여줍니다. 바뀐 게 없으면 "올릴 변경이 없습니다"로 끝냅니다.
2. 커밋 메시지는 바뀐 내용을 한 줄로 요약해서 한국어로 씁니다. 예: `연락처 섹션 추가`.
3. `git add -A && git commit -m "<메시지>" && git push`
4. 출력:
   > GitHub에 올렸습니다. Cloudflare가 1~2분 안에 자동으로 새 버전을 배포합니다.
   > 확인: `DEPLOY.md` 의 사이트 주소를 새로고침하세요. 안 바뀌면 Ctrl+Shift+R (Mac: Cmd+Shift+R).
5. `DEPLOY.md` 에 사이트 주소가 있으면 60초 뒤 `curl -sI` 로 한 번 확인해 주고 결과를 알립니다. 실패해도 오류로 취급하지 말고 "조금 더 기다려 보세요"라고 합니다. `DEPLOY.md` 에 주소가 없으면 주소를 지어내지 말고 "Cloudflare 화면의 주소를 붙여 주시면 DEPLOY.md 에 적어 두겠습니다"라고 합니다.

## 6. 되돌리기 요청이 오면

"방금 올린 거 되돌려줘", "이전 상태로" 라고 하면:
1. `git log --oneline -5` 를 보여주고 어디로 돌아갈지 확인합니다.
2. `git revert <커밋> --no-edit` (히스토리를 지우는 `reset --hard` 는 쓰지 않습니다) → push.
3. 특정 부분만 되돌리고 싶다고 하면 revert 대신 파일을 직접 고쳐서 새 커밋으로 올립니다.
