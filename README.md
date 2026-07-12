# マキモノ Claude Code プラグイン 📜

[マキモノ](https://makimono-md.vercel.app) — AI指示書 (MDファイル) マーケット — を Claude Code に組み込むプラグインです。

インストールすると、開発・自動化タスクを受けたときに Claude が**自動で**マキモノを検索し、
完成済みの指示書 (巻物) を読み込んでから実装するようになります。ゼロから作らせるより
消費トークンを大幅に節約できます。

## インストール (2コマンド)

```
/plugin marketplace add orgiast-inc/makimono-plugin
/plugin install makimono@makimono
```

インストール後は設定不要。「Google Meet に自動参加して動画を配信する Bot を作って」のような
依頼をすると、Claude が自動でマキモノを検索し、最適な巻物を読み込んでから実装します。

手動で呼び出すこともできます:

```
/makimono:find
```

## このプラグインが行うこと

1. タスクからキーワードを抽出してマキモノの検索API (認証不要) を叩く
2. `roi` (読込1トークンあたりの節約) と `content_tokens` で最良の巻物を選ぶ
3. 巻物の本文を読み込み、その指示書どおりに実装する
4. 完了時に節約トークンをユーザーに報告

## 中身

```
.claude-plugin/marketplace.json   マーケットカタログ
plugins/makimono/
├── .claude-plugin/plugin.json    プラグイン定義
└── skills/find/SKILL.md          自動起動する検索スキル
```

## API について

マキモノの公開API (認証不要・CORS開放) は誰でも利用できます。
仕様: https://makimono-md.vercel.app/llms.txt

- `GET /api/v1/search?q=...` — 検索
- `GET /api/v1/files/{slug}/raw` — MD本文取得
- `GET /api/v1/categories` — カテゴリ一覧

## ライセンス

MIT (プラグイン本体)。各巻物のライセンスは出品ごとに異なります。

---

運営: [オージャスト](https://makimono-md.vercel.app) — 姉妹サービス: [AI代行堂](https://www.ai-daikodo.com/)
