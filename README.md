# team-plus-skills

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Findy Team+ のデータを使って開発プロセスを診断・改善する、[Claude Code](https://claude.com/claude-code) 向けの skill 集です。

## 収録している skill

| skill | 概要 |
| --- | --- |
| [team-plus-impl-diag](skills/team-plus-impl-diag/) | 実装フェーズ（コミット〜マージ）を 11 種類の項目で診断し、課題の整理と改善策の提案を含むレポートを出力します |

## 前提条件

利用には **Findy Team+ のご契約**と、以下 2 つの準備が必要です。

### 1. 診断対象のチームモニタリングを公開する

Team+ の画面で、チームモニタリング設定 > 対象モニタリングの「編集」 > 共有設定を「全体公開」に変更してください。

非公開のままだと外部 API から参照できず、チーム名を指定しても診断が始まりません。「チームが見つからない」と言われた場合は、まずチームモニタリング設定をご確認ください。

### 2. Team+ MCP サーバーに接続する

Claude Code の `~/.claude/settings.json` に以下を追加します。API キーは Team+ の設定画面から発行してください。

```json
{
  "mcpServers": {
    "team-plus": {
      "type": "http",
      "url": "https://mcp.findy-team.io/mcp",
      "headers": {
        "X-Team-Plus-Api-Key": "Team+ で発行した API キー",
        "X-Team-Plus-Organization": "自組織の organization 名"
      }
    }
  }
}
```

設定後に Claude Code を再起動し、`claude mcp list` で接続を確認してください。MCP の接続は非同期のため、起動直後はツールが見えないことがあります。

## 導入手順

### 1. このリポジトリを取得する

```sh
git clone https://github.com/Findy/team-plus-skills.git
```

ZIP でのダウンロードでも構いません。

### 2. 使いたい skill を配置する

個人で使う場合:

```sh
cp -r team-plus-skills/skills/team-plus-impl-diag ~/.claude/skills/
```

チームで共有する場合は、プロジェクトリポジトリの `.claude/skills/` 配下に置いてください。

### 3. Claude Code を再起動する

再起動すると skill の一覧に現れ、使えるようになります。

skill ごとの詳しい使い方・オプションは、各 skill ディレクトリの README をご覧ください。

## 更新

改善版が出た場合は、リポジトリを取得し直して配置し直してください。閾値やナレッジの更新がそのまま次回の診断に反映されます。

## フィードバック・貢献

現時点では試験的な提供です。以下のようなフィードバックを随時歓迎いたします。

- 検出された項目が現場の実感と合っていたか
- 実態に合わないと感じた基準値
- 提案された改善策を実際に作りたいと思ったか

バグ報告や機能リクエストは [Issues](https://github.com/Findy/team-plus-skills/issues) まで。プルリクエストも歓迎します！ いただいたご意見は、基準値と提案内容の改善に反映いたします。Team+ のご契約に関するご相談は、弊社担当者までお寄せください。

セキュリティ上の問題を見つけた場合は、公開の場ではなく [SECURITY.md](SECURITY.md) の手順に沿ってご報告ください。

## ライセンス

Apache License 2.0 — 詳細は [LICENSE](LICENSE) をご覧ください。

Copyright 2026 Findy Inc.
