# Fubuki エージェント組織図 & ワークフロー

## 組織図

```
                    ┌──────────────┐
                    │   director   │  (opus) 統括・オーケストレーション
                    │   全体PDCA   │
                    └──────┬───────┘
                           │
        ┌──────────┬───────┼───────┬──────────┐
        │          │       │       │          │
   ┌────▼───┐ ┌───▼───┐ ┌▼─────┐ │    ┌─────▼─────┐
   │analyst │ │planner│ │writer│ │    │engagement │
   │外部調査│ │企画   │ │執筆  │ │    │交流       │
   └────────┘ └───────┘ └──────┘ │    └───────────┘
                                  │
                    ┌─────────────┼─────────────┐
                    │             │             │
               ┌────▼───┐  ┌────▼─────┐  ┌────▼─────┐
               │monitor │  │evaluator │  │optimizer │
               │収集    │  │評価      │  │改善      │
               └────────┘  └──────────┘  └──────────┘
```

## PDCAワークフロー

| Phase | エージェント | 役割 |
|-------|-------------|------|
| **Plan** | analyst → planner | 外部トレンド調査 → コンテンツ企画・カレンダー作成 |
| **Do** | writer → `/content-review` → engagement | 執筆 → 品質チェック → 投稿ログ保存 → 交流アクション |
| **Check** | monitor → evaluator | メトリクス収集(imp/eng等) → baseline比較・成功/失敗パターン特定 |
| **Act** | optimizer | evaluatorの提案(high confidence)に基づきナレッジ・プロンプト自動更新 + バージョン管理 |

## エージェント詳細

| エージェント | モデル | 主な役割 |
|-------------|--------|----------|
| **director** | opus | 全体統括。サブエージェント起動・判断(medium confidence提案の採否等) |
| **analyst** | sonnet | 競合・市場・Xアルゴリズム変更を調査 → plannerへ提供 |
| **planner** | sonnet | 投稿ネタ・タイミング・フック案をJSON形式で出力 |
| **writer** | sonnet | sns-profile.jsonのペルソナに沿って投稿本文を執筆 |
| **engagement** | sonnet | リプライ・引用RT候補と文面を作成 |
| **monitor** | sonnet | X API/Webからメトリクス収集 → `data/metrics/` に記録 |
| **evaluator** | sonnet | 内部データのみで成功パターン分析 → optimizerへ改善提案 |
| **optimizer** | opus | ナレッジ更新・プロンプトバージョン管理(max 2ファイル/回) |

## スキル (手動起動)

| スキル | 用途 |
|--------|------|
| `/pdca-cycle` | PDCAサイクル1回分を一気通貫実行 |
| `/content-review` | 投稿前の品質チェック(炎上リスク・NG表現等) |
| `/trend-research` | Webトレンド調査(planner前段) |
| `/post` | 単発ツイート生成 |
| `/campaign` | 期間指定の一括コンテンツ生成 |
| `/report` | パフォーマンスレポート生成 |
| `/autopilot` | PDCAサイクル継続自律実行 |

## ガードレール

- 月1,500投稿上限 (X API Free tier)
- `prohibited_topics` / `prohibited_expressions` 該当 → 絶対ブロック
- optimizer自動適用は `confidence: high` のみ、`medium` はdirector判断
