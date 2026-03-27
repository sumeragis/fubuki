---
name: post
description: 単発ツイートを生成・投稿する
argument-hint: "[テーマやキーワード]"
allowed-tools: Read, Write, Glob, Grep, Bash, WebSearch, WebFetch, Agent(writer), Skill
---

## 手順

1. `$ARGUMENTS` からテーマを受け取る（なければ planner に企画を依頼）
2. writer エージェントで複数案を執筆
3. ユーザーに案を提示して選定してもらう
4. 選ばれた案を `/content-review` スキルで品質チェック
5. pass したら X API で即時投稿し、`data/posts/` にログ保存

## 投稿ログフォーマット

ファイル名: `data/posts/YYYY-MM-DD_HHmmss.json`

```json
{
  "created_at": "ISO8601",
  "posted_at": "ISO8601",
  "content": "投稿本文",
  "type": "tweet | thread",
  "hashtags": [],
  "status": "posted | draft",
  "post_id": null,
  "metrics": {}
}
```
