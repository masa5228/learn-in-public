---
title: "Claude Code と MCP で「記事を書く→Zenn公開→経歴に蓄積」を自動化した"
emoji: "🚀"
type: "tech"
topics: ["claudecode", "mcp", "zenn", "github", "個人開発"]
published: true
---

## はじめに

転職や対外アピールのために技術記事を書きたい。でも「ネタを書く → 媒体に公開する → 書いた実績をどこかに残す」を毎回手作業でやるのは、正直続かない。

そこで AI エージェントの **Claude Code** と **MCP（Model Context Protocol）** を使って、記事執筆から公開、実績の蓄積までを一気通貫で回す仕組みを作った。**この記事自体も、その仕組みで公開している。**

一言でいうと、こうなった:

> Claude に依頼すると、記事を書き、Zenn に公開し、GitHub の経歴リポジトリにタイトルと URL を自動で積む。

## 全体像

```
依頼 ──▶ Claude Code（司令塔）
          ├─ 調査・構成
          ├─ Zenn形式で執筆（下書き）
          ├─ ★公開前に内容を確認
          └─ GitHub MCP
               ├─ articles/ に push（= Zenn が自動公開）
               └─ career/index.json と README の実績一覧を更新
```

ポイントは2つ。

- **発信媒体に Zenn を選んだ**こと（理由は後述）
- **経歴リポジトリと記事ソースを同じ GitHub リポジトリに同居**させたこと

## なぜ Zenn を選んだのか

出発点は「Note みたいな媒体で発信したい」だった。ところが *自動化* の観点で各媒体を比べると、景色が変わる。

| 媒体 | 投稿の自動化 | 連携 |
|---|---|---|
| Zenn | ◎ GitHub の main に push すると自動公開。URL も `zenn.dev/<user>/articles/<slug>` で**事前に確定** | GitHub 連携がネイティブ |
| Qiita | ◯ 公式 API / CLI / MCP サーバーあり | API・MCP |
| Note | △ **公式の投稿 API が無い**。非公式 API や Selenium は規約リスク | 実用的な手段なし |

意外にも、出発点だった **Note が最も自動化に不向き**（公式投稿 API が無い）。一方 **Zenn は「push = 公開」かつ URL が slug から確定する**ので、記事ソースも実績インデックスも同じ Git の世界で完結できる。これがこの仕組みと噛み合った。

## リポジトリ構成

```
learn-in-public/
├─ articles/                ← Zenn が読む記事（push = 公開）
│   └─ 2026-06-20-....md
├─ career/index.json        ← 発信実績の正本（機械可読）
├─ README.md                ← index.json から生成する実績一覧
└─ docs/article-template.md ← 記事テンプレート
```

`career/index.json` を実績の「正本」にして、README の一覧表はそこから生成する。JSON を機械可読にしておくと、あとでポートフォリオサイトに流用したり集計したりが楽になる。エントリはこんな形:

```json
{
  "title": "...",
  "platform": "zenn",
  "url": "https://zenn.dev/muramasa0228/articles/2026-06-20-...",
  "published_at": "2026-06-20",
  "topics": ["mcp", "claude"]
}
```

Zenn は **ファイル名（拡張子なし）がそのまま slug** になり、公開 URL に使われる。つまり push する前から URL が分かるので、実績の記録まで自動化できる。

## ハマったところ

**1. GitHub MCP の権限（403）**

最初、MCP のトークンでリポジトリ作成やファイル書き込みが `403 Resource not accessible` で弾かれた。fine-grained token に次を付ける必要があった。

- `Administration`（リポジトリ作成）
- `Contents: Read and write`（ファイル push）
- 対象リポジトリへのアクセス（All repositories もしくは対象を選択）

**2. `articles/` と `books/` の違い**

Zenn のディレクトリは固定名で、用途が分かれている。

- `articles/` … 単発の記事
- `books/` … 複数チャプターの本（Zenn Book）

単発の記事は `articles/` が正解。`books/` は本を書くとき専用なので、ブログ的な記事には不要。

## スキルにする

最後に、この一連の流れを Claude Code の **スキル（`/post-article`）** として手順書化した。肝は **「変更・公開・コミットは実行前に必ず確認する」確認ゲートを挟む**こと。AI に丸投げして勝手に公開されると怖いので、下書きと公開 URL を提示し、人間が OK を出してから push する設計にした。

## まとめ

- 発信媒体は「自動化したいか」で選ぶと、定番の Note より **Zenn が刺さる**ことがある
- **「push = 公開」「URL が確定」**という Zenn の性質は、実績の自動蓄積と相性が良い
- AI エージェント ＋ MCP で「書く → 出す → 残す」をパイプライン化すると、発信のハードルが大きく下がる

今後は Qiita への同時投稿（Qiita は API/MCP あり）や、Note への下書き提供（手動公開＋URL 記録のハイブリッド）も足していきたい。
