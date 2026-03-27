---
name: generate-daily
description: 翌日分の投稿を自動生成し、スケジュール保存する。毎日23:00にcronで起動される。
allowed-tools: Read, Write, Glob, Grep, Bash, WebSearch, WebFetch, Agent(analyst), Agent(planner), Agent(writer), Skill
---

翌日の投稿コンテンツを生成し、`data/posts/` にスケジュール保存する。

## 手順

1. `sns-profile.json` の `posting.frequency_per_day` と `posting.best_times` を確認する
2. `data/posts/` から当月の投稿数を確認し、月間上限（`api.monthly_post_limit`）の残数を計算する
   - 残数が `frequency_per_day` を下回る場合は上限に収まる件数に減らす
   - 残数が0なら生成を中止し警告を出力する
3. analyst を起動してトレンド・競合情報を取得する（`operations.analyst_interval_days` を確認し前回から7日未満なら省略）
4. planner に翌日分のコンテンツ企画を依頼する（件数 = `frequency_per_day`）
5. writer に各企画の複数案を執筆させる
6. `/content-review` で全案を評価し、各投稿ごとに最高評価の案を自動選定する
   - `block` 判定の案は除外する
   - 全案が `block` の場合はその投稿をスキップしてログに記録する
7. 選定した投稿を `data/posts/YYYY-MM-DD_HHmmss.json` として保存する
   - `scheduled_time` に翌日の `posting.best_times` を割り当てる（件数分を順番に使う）
   - `status: "scheduled"` でセーブする

## 出力ログ

完了後にサマリーを出力:
```
[generate-daily] YYYY-MM-DD 完了
- 生成: N件
- スキップ（全案block）: N件
- 月間残数: N件
- 投稿予定: 7:00 / 12:00 / 19:00（翌日）
```
