# エージェント連携プロトコル

エージェント間の引き継ぎルール、ファイル命名規則、データの受け渡し方を定めます。
全エージェントが参照します。

## 3つの設計原則

### 1. 生成と評価を分離する
writerが書いたコンテンツをwriterが評価しない。
evaluatorは常に生成者とは独立した視点で評価する。

### 2. 大型タスクはフェーズ分割する
各フェーズの成果物をファイルに出力し、次フェーズは新しいエージェントがそのファイルを読んで作業する。
コンテキストをリセットすることで、後半の品質劣化を防ぐ。

### 3. 独立したタスクは並列起動する
directorは独立したタスクを複数エージェントに同時に振る。
例: analyst（外部調査）と monitor（内部データ確認）は並列起動できる。

## PDCAフロー図

```
[Plan]
  analyst（外部調査）──┐
                        ├→ planner（企画立案）
  monitor（過去データ）──┘

[Do]
  planner → writer（複数案生成）→ 選定（director or ユーザー）→ /content-review → data/posts/ → engagement

[Check]
  monitor（メトリクス収集）→ evaluator（成果評価）→ data/eval/reports/

[Act]
  evaluator → optimizer（ナレッジ更新）→ data/prompt-versions/
```

## ファイル命名規則

| データ種別 | 保存先 | ファイル名形式 |
|-----------|--------|--------------|
| 投稿ログ | `data/posts/` | `YYYY-MM-DD_HHmmss.json` |
| メトリクス | `data/metrics/` | `YYYY-MM-DD_HHmmss.json` |
| 評価レポート | `data/eval/reports/` | `YYYY-MM-DD.json` |
| キャンペーン | `data/campaigns/` | `YYYY-MM-DD/` ディレクトリ |
| 最適化バックアップ | `data/prompt-versions/` | `YYYY-MM-DD/` ディレクトリ |
| 自動運用ログ | `data/reports/` | `autopilot-YYYY-MM-DD.json` |
| エラーログ | `data/reports/errors/` | `YYYY-MM-DD_HHmmss.json` |

## エージェント間の情報受け渡し

| 渡す側 | 受け取る側 | 渡すもの |
|--------|-----------|---------|
| analyst | planner | analyst出力JSON |
| planner | writer | `templates/content-plan.json` 形式のJSON |
| writer | director/ユーザー | `templates/tweet-drafts.json` or `templates/thread-drafts.json`（複数案） |
| director/ユーザー | content-review | 選定済みの1案（`selected_id` に番号を入れて渡す） |
| monitor | evaluator | メトリクスJSONのファイルパス |
| evaluator | optimizer | 評価レポートのファイルパス |
| optimizer | director | 変更サマリー |

## テンプレートの使用

各エージェントは `templates/` 配下のテンプレートを使って出力を統一する。
テンプレートを使うことで、次のエージェントが迷わず読める。
