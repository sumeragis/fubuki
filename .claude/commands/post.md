---
name: post
description: 単発ツイートを生成・投稿する
argument-hint: "[テーマやキーワード]"
allowed-tools: Read, Write, Glob, Grep, Bash, WebSearch, WebFetch, Agent(writer), Skill
---

## 手順

1. `$ARGUMENTS` からテーマを受け取る（なければ planner に企画を依頼）
2. writer エージェントでツイートを執筆
3. /content-review スキルで品質チェック
4. チェック通過後、`data/posts/` に投稿ログをJSON保存
5. X APIで投稿を実行（API連携未設定の場合はログ保存のみ）

## 投稿ログフォーマット

ファイル名: `data/posts/YYYY-MM-DD_HHmmss.json`

```json
{
  "created_at": "ISO8601",
  "content": "投稿本文",
  "type": "tweet | thread",
  "hashtags": [],
  "status": "posted | draft",
  "metrics": {}
}
```
