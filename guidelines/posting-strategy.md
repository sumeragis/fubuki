# 投稿戦略マニュアル

投稿頻度・タイミング・フォーマット戦略の基準です。
planner / director が参照します。
**具体的な設定値は `.claude/sns-profile.json` の `posting` セクションを参照してください。**
**コンテンツの種類・比率・成功パターンは `.claude/knowledge/planner.md` を参照してください。**

## 投稿頻度

- 1日の投稿数: `sns-profile.json` の `posting.frequency_per_day`（デフォルト: 3件）
- 月間上限: 1,500件（X API Free tier）
- 月末2週間は月間残数を確認し、ペースを調整する
- 残数が100件を切ったら director に報告し、今月の方針を確認する

## 投稿タイミング

`sns-profile.json` の `posting.best_times` に従う。
実績に基づく最新の傾向は `knowledge/planner.md` を参照する。

## フォーマット戦略

- **スレッド比率**: `posting.thread_ratio`（デフォルト: 30%）
  - 深い洞察、手順解説、体験談の続きに使う
  - 単発より拡散しにくいが、フォロワー化率が高い
- **画像付き比率**: `posting.image_ratio`（デフォルト: 20%）
  - 視覚的な情報整理、データグラフに使う

## コンテンツ構成

コンテンツの種類と推奨比率は `knowledge/planner.md` で管理する。
optimizer が実績データに基づいて更新するため、常に最新版を参照すること。

## トレンドジャック方針

- タイムリーなトレンドは積極的に活用する（analyst のリサーチを活用）
- ただし、自アカウントのジャンルと無関係なトレンドには乗らない
- 炎上中のトピックには触れない（`escalation-rules.md` 参照）
- トレンドジャックの成功・失敗事例は `knowledge/planner.md` に蓄積する

## 週次レビュー基準

evaluator の評価レポートで以下を確認し、次週の戦略に反映する:
- `knowledge/planner.md` のコンテンツ比率が実態に合っているか
- 投稿時間帯のパフォーマンス差
- どのフォーマット（単発・スレッド）が効いているか
