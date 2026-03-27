---
name: campaign
description: 指定期間分のコンテンツを一括生成する
argument-hint: "[期間(days)] [テーマ]"
allowed-tools: Read, Write, Glob, Grep, Bash, WebSearch, WebFetch, Agent(planner), Agent(writer), Skill
---

## 手順

1. `$ARGUMENTS` から期間とテーマを受け取る（デフォルト: 7日）
2. planner エージェントで期間分のコンテンツカレンダーを作成
3. writer エージェントで各投稿を執筆
4. /content-review スキルで全投稿を品質チェック
5. `data/campaigns/YYYY-MM-DD/` に保存
6. サマリーを出力

## 出力ファイル

- `data/campaigns/YYYY-MM-DD/calendar.json` - カレンダー全体
- `data/campaigns/YYYY-MM-DD/posts/` - 各投稿ファイル
