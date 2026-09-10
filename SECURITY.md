# セキュリティポリシー

## 脆弱性の報告

本リポジトリで配布している skill に関するセキュリティ上の問題を見つけた場合は、**公開の場ではなく、以下のいずれかの非公開の経路でご報告ください。**

- GitHub の [Report a vulnerability](https://github.com/Findy/team-plus-skills/security/advisories/new)（非公開のセキュリティアドバイザリ）
- Findy Team+ の弊社担当者

本リポジトリは Issue を無効にしているため、公開の場での報告手段はありません。Pull Request での修正提案も受け付けておりません。

## 対象範囲

本リポジトリが配布するのは Claude Code 向けの skill 定義（Markdown / YAML）のみで、実行可能なプログラムやサーバーは含まれません。

Findy Team+ 本体、および Team+ MCP サーバー（`mcp.findy-team.io`）に関するご報告は、弊社担当者までお願いいたします。

## API キーの取り扱い

skill の利用には Team+ の API キーが必要ですが、キーは Claude Code 側の `~/.claude/settings.json` に設定するもので、本リポジトリには含まれません。設定ファイルをリポジトリにコミットしないようご注意ください。
