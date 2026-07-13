---
name: publish
description: 手元の優れた CLAUDE.md・スキル・開発指示書を、マキモノ(AI指示書マーケット)に出品したいときに使う。「このCLAUDE.mdを出品して」「この設定をマキモノに売りたい」などの依頼で起動。機密を除去し、メタデータを起案し、人間の承認を得てから出品APIに送信する。
---

# マキモノに出品する

手元の指示書を、マキモノ (https://makimono-md.vercel.app) に出品候補として整えて送信します。
送信された出品は審査キューに入り、マキモノ運営の審査後に公開されます。

## 手順

1. **対象を確認** — ユーザーが出品したいファイル (CLAUDE.md / SKILL.md / 指示書) を特定する。
   指定がなければ候補を提示して選んでもらう。

2. **機密の除去(最重要)** — 本文から次を削除またはプレースホルダ化する:
   - APIキー / トークン / パスワード / DB接続文字列 / Webhook URL → `<YOUR_API_KEY>` 等
   - 顧客名・社名・個人名・社内固有のID・非公開の事情 → 一般化または削除
   マキモノ側でも秘密情報スキャンが走るが、顧客名などは自動検出できないので必ず自分で除去する。

3. **メタデータ起案** — 次を作る:
   - title(40字以内目安) / summary(20字以上・何ができるか) / category(業務自動化 / Web開発 /
     開発プロセス / Google Workspace / データ収集 / SNS運用 / 経理・バックオフィス / AIのしつけ / その他)
   - scratchTokens(ゼロから作らせた場合の概算) / withMdTokens(このMD使用時の概算。必ず小さく)
   - price(円。無料なら 0)

4. **APIキーを用意** — ユーザーに出品者APIキー (`mk_...`) を尋ねる。持っていなければ発行する:
   ```
   POST https://makimono-md.vercel.app/api/v1/keys   body: {"email": "<ユーザーのメール>"}
   ```
   返ってきた apiKey は再表示されないので、ユーザーに保管を促す。

5. **承認ゲート** — サニタイズ後の本文とメタデータをユーザーに提示し、
   「この内容で出品してよいか」を確認する。承認が出たものだけ送信する。

6. **送信** — WebFetch で出品APIに POST する:
   ```
   POST https://makimono-md.vercel.app/api/v1/listings
   Authorization: Bearer <apiKey>
   Content-Type: application/json
   {"title","summary","category","body","scratchTokens","withMdTokens","price"}
   ```
   制約: body は200文字以上・10万文字以内、summary は20文字以上。
   422 (secrets_detected) が返ったら、指摘箇所を再サニタイズして再送する。

7. **報告** — submissionId と「審査後に公開される」ことをユーザーに伝える。

## 注意

- 会社の業務で作った指示書は、権利が所属組織にある場合があるので、出品前にユーザーに確認する。
- 承認ゲート(手順5)は省略しない。無断で外部に送信しない。
