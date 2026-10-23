---
name: deploy-pages
description: 현재 폴더의 정적 홈페이지(index.html)를 GitHub 저장소에 올리고 GitHub Pages로 배포한다. Cloudflare 계정 없이 GitHub만으로 끝나는 가장 단순한 배포. 사용자가 "/deploy-pages", "GitHub Pages로 배포해줘", "깃허브 페이지로 올려줘", "github.io 주소 만들어줘" 라고 할 때, 그리고 어디에 올릴지 말하지 않고 "배포해줘", "인터넷에 올려줘", "사이트 공개해줘", "수정한 거 올려줘" 라고만 할 때도 이 스킬이 기본이다(DEPLOY.md에 Cloudflare가 적혀 있으면 /deploy-cloudflare). 첫 배포는 저장소 생성 + Pages 활성화까지 전부 자동, 이후 배포는 commit + push만.
---

# deploy-pages: 홈페이지를 GitHub Pages에 올리기

대상 사용자는 비개발자입니다. 한국어로, 짧게, 다음에 뭘 하면 되는지만 말합니다.
이 스킬은 **끝까지 자동**입니다. 브라우저에서 클릭할 일이 없습니다. GitHub Pages는 `gh` 명령으로 켤 수 있기 때문입니다.
토큰이나 비밀번호를 파일에 저장하지 않습니다. sudo가 필요한 명령은 실행하지 않습니다.

## 0. 사전 점검

`setup-check` 스킬의 1번(점검 실행)을 먼저 수행합니다. git, gh, GitHub 로그인 중 하나라도 ❌면 **여기서 멈추고** setup-check의 대응 절차로 안내합니다.

## 1. 폴더 점검

1. 현재 폴더 **맨 위**에 `index.html` 이 있는지 확인합니다. 없으면 어디 있는지 찾아 알려주고 "맨 위로 옮길까요?" 묻습니다. GitHub Pages는 맨 위(또는 `docs/`)만 지원하므로, 하위 폴더에 두겠다면 `docs/` 로 옮기도록 권합니다.
2. index.html 안에 `/`로 시작하는 절대 경로 링크(`href="/style.css"`, `src="/img/..."`)가 있으면 경고합니다. GitHub Pages 주소는 `https://아이디.github.io/저장소/` 처럼 하위 경로라서 절대 경로가 깨집니다. "상대 경로(`img/...`)로 고칠까요?" 묻고 동의하면 고칩니다.
3. 파일 목록을 보여주고 개인정보(전화번호, 주소)나 비밀 파일(`.env`, `*.key`, `secrets*`)이 있으면 경고하고 확인을 받습니다. GitHub Pages는 **공개 저장소**가 필요하므로 파일 전부가 공개된다고 분명히 말합니다.
4. 이미 git 저장소이고 origin 이 있으면(예: `/save` 로 먼저 보관해 둔 폴더) Pages 가 켜져 있는지 확인합니다: `gh api "repos/$OWNER/<이름>/pages" -q .status`
   - 켜져 있으면(`built` 등) → **5. 이후 배포** 로 갑니다.
   - 404(안 켜짐)이면 → 저장소가 비공개인지 봅니다(`gh repo view --json visibility -q .visibility`). 비공개면 "GitHub Pages 는 공개 저장소에서만 됩니다. 저장소를 공개로 바꿀까요? 폴더 안 파일 전부가 누구나 볼 수 있게 됩니다" 를 묻고, 동의하면 `gh repo edit --visibility public --accept-visibility-change-consequences`. 그다음 `.nojekyll` 을 만들어 커밋·push 하고 **3번의 Pages 활성화**부터 이어갑니다(`gh repo create` 는 건너뜀). 비공개를 유지하고 싶다면 `/deploy-cloudflare` 를 권합니다.

## 2. 저장소 준비 (첫 배포)

1. `.gitignore` 가 없으면 만듭니다:
   ```
   .DS_Store
   Thumbs.db
   .env
   *.key
   *.pem
   node_modules/
   ```
2. `.nojekyll` 빈 파일을 만듭니다 (GitHub Pages가 파일을 건드리지 않게).
3. `git init -b main` (이미 저장소면 생략), `git add -A`, `git commit -m "첫 배포"`.
4. 저장소 이름: 기본값은 현재 폴더 이름(영문 소문자, 공백은 `-`). "저장소 이름은 `my-homepage` 로 할까요?" 한 번 확인합니다.
   - 사용자가 "주소에 저장소 이름 없이 `아이디.github.io` 로 하고 싶다"고 하면 저장소 이름을 정확히 `<아이디>.github.io` 로 만듭니다 (한 계정에 하나만 가능).

## 3. GitHub에 올리고 Pages 켜기

```
gh repo create <이름> --public --source=. --remote=origin --push
```
- public 이어야 합니다 (무료 계정의 GitHub Pages 조건). private 을 원하면 Cloudflare 쪽(`/deploy-cloudflare`)을 권합니다.
- "already exists" 면 `-2` 를 붙여 다시 묻습니다.

Pages 활성화:
```
OWNER=$(gh api user -q .login)
gh api -X POST "repos/$OWNER/<이름>/pages" -f 'source[branch]=main' -f 'source[path]=/'
```
- index.html 을 `docs/` 에 두었다면 `source[path]=/docs`.
- 409(이미 켜짐)는 성공으로 간주합니다.
- 404/403 이면 저장소가 public 인지, gh 로그인 계정이 맞는지 확인합니다.

HTTPS 강제(실패해도 무시):
```
gh api -X PUT "repos/$OWNER/<이름>/pages" -F https_enforced=true
```

## 4. 주소 확인

주소는 `https://<아이디>.github.io/<이름>/` 입니다 (저장소가 `<아이디>.github.io` 면 `https://<아이디>.github.io/`).

1. 주소를 사용자에게 먼저 보여줍니다. "첫 배포는 1~3분 걸립니다"라고 말합니다.
2. 30초 간격으로 최대 6번 `gh api "repos/$OWNER/<이름>/pages" -q .status` 를 확인합니다. `built` 가 되면 `curl -sI <주소>` 로 200을 확인합니다.
3. 성공하면:
   > 배포 완료. 이 주소를 사람들에게 알리세요: <주소>
4. 3분이 지나도 안 되면 오류로 취급하지 말고 "조금 뒤 이 주소를 열어 보세요. 안 열리면 저장소 Settings → Pages 를 캡처해서 보여주세요"라고 안내합니다.

폴더에 `DEPLOY.md` 를 만들어 저장소 주소, 사이트 주소, 배포 방식(GitHub Pages)을 적고 commit/push 합니다.

## 5. 이후 배포 (origin 이 이미 있을 때)

1. `git status` 로 바뀐 파일을 보여줍니다. 없으면 "올릴 변경이 없습니다".
2. 커밋 메시지는 바뀐 내용 한 줄 요약, 한국어. 예: `연락처 섹션 추가`.
3. `git add -A && git commit -m "<메시지>" && git push`
4. 출력:
   > GitHub에 올렸습니다. 1~2분 안에 자동으로 새 버전이 배포됩니다.
   > 확인: DEPLOY.md 의 사이트 주소를 새로고침. 안 바뀌면 Ctrl+Shift+R (Mac: Cmd+Shift+R).
5. 60초 뒤 `curl -sI` 로 한 번 확인해 알립니다. 실패해도 "조금 더 기다려 보세요".

## 6. 되돌리기

"방금 올린 거 되돌려줘" → `git log --oneline -5` 보여주고 확인 → `git revert <커밋> --no-edit` → push. `reset --hard` 는 쓰지 않습니다.

## 7. 내 도메인 연결 (요청 시)

1. 사용자가 산 도메인(예: `hong.com`)을 묻습니다.
2. `gh api -X PUT "repos/$OWNER/<이름>/pages" -f cname=hong.com`
3. 도메인 업체 DNS 설정을 안내합니다:
   - `www` 를 쓰려면: CNAME `www` → `<아이디>.github.io`
   - 루트(`hong.com`)를 쓰려면: A 레코드 4개 → 185.199.108.153 / 185.199.109.153 / 185.199.110.153 / 185.199.111.153
4. 반영까지 몇 분~하루. 그 뒤 `gh api -X PUT ... -F https_enforced=true`.
