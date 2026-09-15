# team-plus-capital-diag 導入手順

Team+ の開発資本スコアを org 起点でブレイクダウンし、組織の弱いサブカテゴリとそれを牽引するチームを特定して、次アクション（[team-plus-impl-diag](../team-plus-impl-diag/) の実行提案など）を含む薄いトリアージレポートを生成する Claude Code 向けの skill です。

診断ロジックそのものは `SKILL.md` をご覧ください。このファイルは配布・導入のための手順書です。

## impl-diag との使い分け

| skill | 問い | 対象 |
| --- | --- | --- |
| team-plus-capital-diag | 組織のどの領域を・どのチームから改善に着手すべきか | org 全体（月次の開発資本スコア） |
| team-plus-impl-diag | そのチームの実装フェーズ（コミット〜マージ）のどこに課題があるか | 特定チーム（直近 90 日の実データ） |

capital-diag で着手先を決め、impl-diag で該当チームを深掘りする 2 段構えを想定していますが、それぞれ単独でも利用できます。

## 前提条件

### 1. Team+ MCP サーバーへの接続とチームモニタリングの公開

[リポジトリの README](../../README.md) の前提条件と同じです。MCP サーバーの接続設定と、対象チームモニタリングの共有設定「全体公開」を済ませてください。

### 2. 開発資本スコアの提供対象であること

本 skill は開発資本スコア API（`get_org_capital_score` / `get_team_capital_score`）を使用します。開発資本スコアは α 提供のため、組織で有効になっていない場合はスコアが返らず診断を開始できません。有効化についてはお使いの Team+ の担当者にご確認ください。

## インストール

個人で使う場合:

```sh
cp -r team-plus-skills/skills/team-plus-capital-diag ~/.claude/skills/
```

チームで共有する場合は、プロジェクトリポジトリの `.claude/skills/` 配下に置いてください。配置後に Claude Code を再起動すると使えるようになります。

## 使い方

「開発資本スコアで診断して」「組織の弱い領域を特定して」「capital-diag」などで起動します。

牽引チーム特定の対象にするチーム（monitoring 名）のリストが必要です。実行時に指定するか、個人 memory に保存しておくと毎回の指定を省けます。

```yaml
# ~/.claude/team-plus-capital-diag/memory.yml
target_teams:
  - チームA
  - チームB
  - チームC
```

いずれも無い場合は、monitoring の一覧を提示して対象を選んでいただきます。

## フィードバック

[リポジトリの README](../../README.md) のフィードバック・貢献の節をご覧ください。
