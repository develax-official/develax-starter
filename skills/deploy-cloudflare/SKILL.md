---
name: deploy-cloudflare
description: 현재 폴더의 정적 홈페이지(index.html)를 GitHub 저장소에 올리고 Cloudflare Pages로 배포한다. 비공개 저장소도 되고 내 도메인 연결이 쉽다. 사용자가 "/deploy-cloudflare", "Cloudflare로 배포해줘", "Cloudflare Pages로 올려줘", "pages.dev 주소 만들어줘" 라고 할 때, 또는 DEPLOY.md에 Cloudflare가 적힌 폴더에서 "수정한 거 올려줘", "배포해줘" 라고 할 때 사용. 첫 배포는 저장소 생성 + Cloudflare 대시보드 연결 안내까지, 이후 배포는 commit + push만. GitHub Pages로 올리려면 /deploy-pages.
---

# deploy-cloudflare: 홈페이지를 GitHub + Cloudflare Pages에 올리기

대상 사용자는 비개발자입니다. 한국어로, 짧게, 다음에 뭘 누르면 되는지만 말합니다.
이 스킬은 **GitHub까지는 자동**, **Cloudflare는 안내**합니다. Cloudflare Pages의 Git 연결은 대시보드에서만 할 수 있기 때문입니다.
토큰이나 비밀번호를 파일에 저장하지 않습니다. sudo가 필요한 명령은 실행하지 않습니다.

## 0. 사전 점검

`setup-check` 스킬의 1번(점검 실행)을 먼저 수행합니다. git, gh, GitHub 로그인 중 하나라도 ❌면 **여기서 멈추고** setup-check의 대응 절차로 안내합니다. 배포를 억지로 진행하지 않습니다.

## 1. 폴더 점검

1. 현재 폴더에 `index.html` 이 **맨 위**에 있는지 확인합니다. 없으면 어디 있는지 찾아서 알려주고, "맨 위로 옮길까요?" 묻습니다. 하위 폴더에 두겠다면 그 폴더명을 기억해 둡니다 (Cloudflare 설정에서 씁니다).
2. 폴더 안 파일 목록을 보여주고, 개인정보(전화번호, 주소, 주민번호)나 비밀번호·API 키가 들어 있을 만한 파일(`.env`, `*.key`, `secrets*`)이 있으면 경고하고 올리기 전에 확인을 받습니다.
3. 이미 git 저장소인지 확인합니다: `git rev-parse --is-inside-work-tree`, `git remote -v`.
   - origin 이 이미 있으면 → **5. 이후 배포** 로 갑니다.
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
   ```
2. `git init -b main` (이미 저장소면 생략), `git add -A`, `git commit -m "첫 배포"`.
3. 저장소 이름을 정합니다. 기본값은 현재 폴더 이름(영문 소문자, 공백은 `-`). 사용자에게 "저장소 이름은 `my-homepage` 로 할까요?" 한 번 확인합니다.
4. 공개 여부: 기본 **public**. 비공개로 하고 싶다고 하면 `--private`. (Cloudflare Pages는 둘 다 됩니다.)

## 3. GitHub에 올리기

```
gh repo create <이름> --public --source=. --remote=origin --push
```

- 성공하면 `gh repo view --web` 은 열지 말고, 저장소 주소(`https://github.com/<아이디>/<이름>`)만 출력합니다.
- "already exists" 오류면 이름 뒤에 `-2` 를 붙여 다시 묻습니다.
- 권한 오류(permission denied, 403)면 `gh auth status` 를 다시 보고 setup-check의 로그인 절차로 안내합니다.

## 4. Cloudflare Pages 연결 안내 (첫 배포)

여기부터는 사용자가 브라우저에서 직접 합니다. 아래 안내를 **그대로** 출력합니다. 사용자의 GitHub 아이디와 저장소 이름을 채워 넣습니다.

> **Cloudflare에서 저장소를 연결합니다 (5분)**
>
> 1. 이 주소를 엽니다: https://dash.cloudflare.com/?to=/:account/workers-and-pages/create/pages
>    (계정이 없으면 먼저 가입: https://dash.cloudflare.com/sign-up)
> 2. **Connect to Git** → GitHub 연결 허용 → 저장소 목록에서 `<이름>` 선택
> 3. 설정은 아래처럼. 빌드 명령은 **비워둡니다**.
>
> | 항목 | 값 |
> |---|---|
> | Project name | `<이름>` |
> | Production branch | `main` |
> | Framework preset | None |
> | Build command | (비움) |
> | Build output directory | `/` ← index.html이 하위 폴더에 있으면 그 폴더명 |
>
> 4. **Save and Deploy** → 1~2분 뒤 `https://<이름>-xxx.pages.dev` 주소가 나옵니다.
> 5. 그 주소를 저에게 붙여넣어 주세요. 열리는지 확인해 드립니다.

사용자가 주소를 주면 `curl -sI <주소>` 로 HTTP 200을 확인하고, "배포 완료. 이 주소를 사람들에게 알리세요"라고 끝맺습니다. 404면 Build output directory 를 다시 보라고 안내합니다.

주소를 기억해 두기 위해 폴더에 `DEPLOY.md` 를 만들어 저장소 주소와 사이트 주소를 적어 둡니다 (사용자에게 알리고 commit/push 합니다).

## 5. 이후 배포 (origin 이 이미 있을 때)

1. `git status` 로 바뀐 파일을 보여줍니다. 바뀐 게 없으면 "올릴 변경이 없습니다"로 끝냅니다.
2. 커밋 메시지는 바뀐 내용을 한 줄로 요약해서 한국어로 씁니다. 예: `연락처 섹션 추가`.
3. `git add -A && git commit -m "<메시지>" && git push`
4. 출력:
   > GitHub에 올렸습니다. Cloudflare가 1~2분 안에 자동으로 새 버전을 배포합니다.
   > 확인: `DEPLOY.md` 의 사이트 주소를 새로고침하세요. 안 바뀌면 Ctrl+Shift+R (Mac: Cmd+Shift+R).
5. `DEPLOY.md` 에 사이트 주소가 있으면 60초 뒤 `curl -sI` 로 한 번 확인해 주고 결과를 알립니다. 실패해도 오류로 취급하지 말고 "조금 더 기다려 보세요"라고 합니다.

## 6. 되돌리기 요청이 오면

"방금 올린 거 되돌려줘", "이전 상태로" 라고 하면:
1. `git log --oneline -5` 를 보여주고 어디로 돌아갈지 확인합니다.
2. `git revert <커밋> --no-edit` (히스토리를 지우는 `reset --hard` 는 쓰지 않습니다) → push.
3. 특정 부분만 되돌리고 싶다고 하면 revert 대신 파일을 직접 고쳐서 새 커밋으로 올립니다.
