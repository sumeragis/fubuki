---
name: report
description: パフォーマンスレポートを生成する
argument-hint: "[期間: week | month | all]"
allowed-tools: Read, Glob, Grep, Bash, Write, Agent(analyst)
---

## 手順

1. `$ARGUMENTS` から期間を受け取る（デフォルト: week）
2. analyst エージェントを起動
3. `data/posts/` から指定期間の投稿ログを集計
4. パフォーマンス分析を実施
5. `data/reports/YYYY-MM-DD.json` に詳細レポートを保存
6. サマリーをターミナルに出力
