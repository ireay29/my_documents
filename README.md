# document_hubs

`document_hubs` は、調査レポート、業界リサーチ、エージェント用プロンプトを MkDocs で整理・公開するためのナレッジハブです。

## このリポジトリで管理しているもの

- `docs/`: MkDocs のソースとなる Markdown、添付資料、参照データ
- `mkdocs.yml`: サイト名、テーマ、ナビゲーションなどの MkDocs 設定
- `site/`: `mkdocs build` で生成される公開用サイト

現在の主な収録カテゴリは次の3つです。

- `crowdfunding/`: クラウドファンディング関連ガイド、調査資料、Agent Prompt
- `logistics_research/`: 物流・運送業界の企業リサーチと関連データ
- `openclaw-research/`: OpenClaw エコシステム調査レポートと参照資料

## 基本の運用方針

- 編集対象は `main` ブランチ上の `docs/` と `mkdocs.yml`
- `site/` は MkDocs のビルド成果物として扱う
- 公開先ブランチは `gh-pages`

## ローカル確認

ローカルで内容を確認する場合は、プロジェクトルートで次を実行します。

```bash
mkdocs serve
```

ビルド成果物を更新する場合は次を実行します。

```bash
mkdocs build
```

## 公開手順

`gh-pages` を公開先として反映する場合は、必要に応じて次のコマンドを利用します。

```bash
mkdocs gh-deploy
```

公開前には、`docs/` の内容、`mkdocs.yml` のナビゲーション、`site/` の生成結果が一致していることを確認してください。
