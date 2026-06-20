# learn-in-public 📚

日々の学びを記事として発信し、その実績を蓄積していくリポジトリです。
記事は **Zenn**（[@muramasa0228](https://zenn.dev/muramasa0228)）と連携して公開し（`articles/` を push すると自動公開）、
発信実績は `career/index.json` で一元管理しています。

## 📝 発信実績

<!-- articles:start -->
| 日付 | タイトル | 媒体 | トピック |
|------|----------|------|----------|
| 2026-06-20 | [Claude Code と MCP で「記事を書く→Zenn公開→経歴に蓄積」を自動化した](https://zenn.dev/muramasa0228/articles/2026-06-20-mcp-zenn-pipeline) | Zenn | claudecode, mcp, zenn, github, 個人開発 |
<!-- articles:end -->

## 🗂 構成

| パス | 役割 |
|------|------|
| `articles/` | Zenn が読む記事ソース（push＝公開） |
| `career/index.json` | 発信実績の正本（機械可読） |
| `docs/article-template.md` | 記事テンプレート（Zenn front matter） |

## 🔄 運用フロー

1. テーマを決めて下書きを作成（`docs/article-template.md` を雛形に）
2. `published: true` にして `articles/` に push → Zenn が自動公開
3. `career/index.json` に `{title, url, platform, published_at, topics}` を追記
4. 上の「発信実績」テーブルを更新

この一連の作業は Claude Code スキル `/post-article` で自動化しています。

## 🔗 Zenn 連携（初回のみ手動）

Zenn ダッシュボード → デプロイ設定 → このリポジトリ（`masa5228/learn-in-public`）を連携。
以降は `main` への push で自動反映されます。
公開 URL は `https://zenn.dev/muramasa0228/articles/<ファイル名（拡張子なし）>`。

---

> 🤖 このリポジトリは Claude Code の発信スキルで運用しています。
