---
name: save
description: 현재 폴더의 작업을 GitHub 저장소에 보관(백업)한다. 배포는 하지 않는다. 사용자가 "/save", "저장해줘", "GitHub에 보관해줘", "백업해줘", "지금 상태 저장", "기록 남겨줘" 라고 할 때 사용. 저장소가 없으면 만들고(기본 비공개), 있으면 commit + push만. "기록 보여줘", "되돌려줘", "다른 컴퓨터에서 받기" 요청도 이 스킬로 처리한다.
---

# save: 내 작업을 GitHub에 보관하기

대상 사용자는 비개발자입니다. 한국어로, 짧게. "커밋", "푸시" 같은 말은 처음 한 번만 괄호로 설명하고 이후엔 "저장", "올리기"라고 합니다.
이 스킬은 **보관만** 합니다. 웹사이트로 공개하지 않습니다(그건 `/deploy-pages`, `/deploy-cloudflare`). 기본은 **비공개 저장소**입니다.
토큰·비밀번호를 파일에 저장하지 않습니다. sudo를 쓰지 않습니다. `reset --hard`, `push --force` 는 쓰지 않습니다.

## 0. 사전 점검

`setup-check` 스킬의 1번(점검 실행)을 수행합니다. git, gh, GitHub 로그인 중 하나라도 ❌면 멈추고 setup-check의 대응 절차로 안내합니다.

## 1. 처음 저장 (저장소가 없을 때)

`git rev-parse --is-inside-work-tree` 와 `git remote -v` 로 확인합니다. origin 이 있으면 **2번**으로.

1. 파일 목록을 보여주고, 비밀 파일(`.env`, `*.key`, `*.pem`, `secrets*`, 비밀번호가 적힌 메모)이 있으면 **올리지 않도록** `.gitignore` 에 넣겠다고 알리고 동의를 받습니다.
2. `.gitignore` 가 없으면 만듭니다:
   ```
   .DS_Store
   Thumbs.db
   .env
   *.key
   *.pem
   node_modules/
   ```
3. `git init -b main`, `git add -A`, `git commit -m "처음 저장"`.
4. 저장소 이름은 폴더 이름(영문 소문자, 공백은 `-`). "저장소 이름은 `<이름>` 으로, **비공개**로 만들까요?" 한 번 확인합니다. 공개를 원하면 `--public`.
5. ```
   gh repo create <이름> --private --source=. --remote=origin --push
   ```
6. 출력: 저장소 주소 `https://github.com/<아이디>/<이름>` 와 "이제부터 `/save` 라고만 하면 됩니다".

## 2. 이후 저장 (origin 이 있을 때)

1. `git status --short` 로 바뀐 파일을 보여줍니다. 없으면 "저장할 변경이 없습니다. 마지막 저장: <git log -1 --format='%cr'>".
2. 커밋 메시지는 바뀐 내용을 한 줄로 요약해서 한국어로. 사용자가 메시지를 말했으면 그걸 씁니다.
3. `git add -A && git commit -m "<메시지>" && git push`
4. 출력: "저장했습니다: <메시지>. 기록 n개째." (`git rev-list --count HEAD`)
5. 공개 여부는 추측하지 말고 `gh repo view --json visibility -q .visibility` 로 읽어서 말합니다.
6. 폴더에 `DEPLOY.md` 가 있으면 이미 배포된 홈페이지입니다. GitHub Pages 면 push 만으로 1~2분 뒤 사이트에 자동 반영되므로 "1~2분 뒤 <사이트 주소>에 반영됩니다. 안 바뀌면 Ctrl+Shift+R (Mac: Cmd+Shift+R)" 라고 덧붙입니다. Cloudflare 면 "Cloudflare 가 1~2분 안에 새 버전을 배포합니다". "사이트에는 반영되지 않았습니다" 라고 말하지 않습니다.

## 3. 기록 보기

"기록 보여줘", "뭐 저장했었지" → `git log --format='%h  %cd  %s' --date=format:'%m/%d %H:%M' -15` 를 표로 보여줍니다. 열: 번호(짧은 해시), 날짜, 내용.

## 4. 되돌리기

"어제 상태로", "방금 저장 전으로" 요청:
1. 3번의 기록을 보여주고 어느 지점으로 갈지 확인합니다.
2. 특정 저장 하나만 취소: `git revert <해시> --no-commit` → `git commit -m "되돌림: <그 저장의 메시지>"` → push. 기록 이름은 항상 한국어로 "되돌림: ..." 입니다(git 기본값 "Revert ..." 를 쓰지 않습니다).
3. 어느 시점의 파일 하나만 되살리기: `git checkout <해시> -- <파일>` → 커밋 → push.
4. 폴더 전체를 과거 시점으로: `git revert --no-commit <해시>..HEAD` → 커밋 "<날짜> 상태로 되돌림" → push. 기록은 지우지 않고 **새 기록으로 되돌립니다**.
5. 끝나면 "되돌렸습니다. 되돌린 것도 기록에 남아 있어서 다시 앞으로 갈 수 있습니다"라고 말합니다. `DEPLOY.md` 가 있는 폴더면 2번 6항과 같이 "1~2분 뒤 사이트에도 반영됩니다" 를 덧붙입니다.

## 5. 다른 컴퓨터에서 받기

"다른 컴퓨터에서 이어서 하고 싶어" → 그 컴퓨터에서 Claude Code 를 열고 이렇게 말하라고 안내합니다:
> "GitHub 저장소 `<아이디>/<이름>` 을 바탕화면에 받아줘" → Claude가 `gh repo clone <아이디>/<이름>` 을 실행합니다.
작업 후에는 그쪽에서도 `/save`, 원래 컴퓨터로 돌아오면 "최신으로 맞춰줘"(`git pull`).

## 6. 공개/비공개 바꾸기

"공개로 바꿔줘" → `gh repo edit --visibility public --accept-visibility-change-consequences` 전에 "폴더 안 파일 전부가 누구에게나 보입니다. 개인정보 없는지 확인했나요?" 묻습니다. 비공개로: `--visibility private`.
