# team-plus-impl-diag 導入手順

Team+ MCP のデータを使って、チームの実装〜マージプロセス（コミット〜マージ）を診断し、課題マップと改善 Skills 提案を含む Markdown レポートを生成する Claude Code 向けの skill です。

診断ロジックそのものは `SKILL.md` をご覧ください。このファイルは配布・導入のための手順書です。

## 前提条件

### 0. Team+ MCP のツールセット（2026年8月末時点以降）

本 skill の診断コアは、Team+ MCP の 2026年8月末時点のツールセットを前提とします。具体的には percentile 対応の stats 系ツール（`percentile_lead_time_pr_to_review` / `*_sample_size` / `percentile_rank`）、`get_team_stat_transitions`、`get_team_member_ai_usage_stats`、`get_ai_tool_impact_report`、AI 利用レポートの `start_date` パラメータと `daily[].total_pulls_count` を使用します。Team+ MCP はホスティド提供のため、通常は追加作業なしで利用できます。

### 1. 診断対象 monitoring の共有設定を「全体公開」にする（必須）

Team+ のチームモニタリングは、共有設定が非公開のままでは `get_monitorings` ツールの結果に出てきません。そのため、名前を指定しても診断対象を解決できず、診断が始まりません。

- Team+ の画面: **チームモニタリング設定 > 対象モニタリングの「編集」 > 共有設定を「全体公開」に変更してください**

### 2. Team+ MCP サーバーへの接続

Claude Code の `~/.claude/settings.json` に以下を追加します（サーバー名は任意です）。

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

API キーは Team+ の設定画面から発行してください。設定後に Claude Code を再起動し、`claude mcp list` で接続を確認してから診断を実行してください。MCP の接続は非同期のため、起動直後はツールが見えないことがあります。

本 skill は `allowed-tools` を宣言していません。skill の `allowed-tools` はツールを制限するものではなく「skill を起動したターンだけ有効な事前許可」で、MCP の許可ルールはサーバー名をリテラルで書く必要があるため、サーバー名を任意にしている本 skill では宣言しても多くの環境で一致しないためです。毎回の許可プロンプトを省きたい場合は、`~/.claude/settings.json` の `permissions.allow` にご自分で付けたサーバー名を追加してください。こちらはセッション全体で有効です。

```json
{
  "permissions": {
    "allow": ["mcp__team-plus"]
  }
}
```

本 skill が使う Team+ MCP のツールはすべて読み取り系（`get_*`）です。ツール単位で絞りたい場合は `"mcp__team-plus__get_team_stats"` のように個別に列挙することもできます。

### 3. 個人 memory（任意）

`~/.claude/team-plus-impl-diag/memory.yml` を置くと、よく使う monitoring 名や閾値を既定値にできます。**この設定が無くても、skill に同梱の `config/defaults.yml` だけで動作します。**

```yaml
# config/defaults.yml と同一スキーマです。上書きしたいキーだけ記述してください
monitoring_name: <よく診断するチーム名>
```

データの解決順は「実行時引数 > memory.yml > config/defaults.yml」です。

## インストール

skill のディレクトリ一式を `~/.claude/skills/team-plus-impl-diag/` に配置してください（個人でご利用の場合）。リポジトリのメンバーで共有する場合は `<リポジトリ>/.claude/skills/` 配下でも構いません。配置後に Claude Code を再起動すると、skill の一覧に現れます。

| パス | 役割 | 配布 |
|---|---|---|
| `SKILL.md` | 診断ロジック本体 | 必須 |
| `config/defaults.yml` | 既定値・閾値のスキーマ | 必須 |
| `knowledge/skill_catalog.md` | 提案の元になる知識 | 必須 |
| `knowledge/design_notes.md` | 閾値・判定方式の設計根拠 | 任意（診断の実行時には読み込まれません） |
| `samples/` | 提案の出発点にするテンプレート（プレースホルダ入り） | 任意（診断の実行時には読み込まれません） |
| `README.md` | このファイル | 任意 |

## 使い方

「実装プロセスを診断して」「マージプロセスを診断して」「impl-diag」などで起動します。

| 引数 | 既定 | 説明 |
|---|---|---|
| monitoring_name | memory / 対話で補完 | 診断対象の monitoring 名（fuzzy 検索が可能です） |
| period | quarter（直近 90 日） | 診断期間 |
| start_date | 終端から逆算 | 期間開始日 YYYY-MM-DD |
| scope | all | all / monitorings / repos / members |
| thresholds | 既定値 | シグナル閾値の上書き（キーは `config/defaults.yml` をご覧ください） |

例:

- `impl-diag` — memory の monitoring を既定期間で診断します
- 「<チーム名> の実装プロセスを診断して」
- 「<チーム名> を scope=repos で診断して」— リポジトリ別のレビュー滞留だけを確認します（API 呼び出しを削減できます）

出力は Markdown レポートです（scope=all で 9 章構成。scope を指定すると 3〜6 章が絞られ、1・2・7・8・9 章は常に出力されます）。保存先を指定すると、ファイルに書き出します。
