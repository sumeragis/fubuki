---
name: post
description: 単発ツイートを生成・投稿する
argument-hint: "[テーマやキーワード]"
allowed-tools: Read, Write, Glob, Grep, Bash, WebSearch, WebFetch, Agent(writer), Skill
---

## 手順

1. `$ARGUMENTS` からテーマを受け取る（なければ planner に企画を依頼）
2. writer エージェントで複数案を執筆
3. ユーザーに案を提示して選定してもらう（`posting.auto_post: false`）
4. 選ばれた案を `/content-review` スキルで品質チェック
5. pass したら `data/posts/` に予約投稿ログとして保存（`posting.mode: "scheduled"`）
   - `scheduled_time` は `posting.best_times` から次の枠を自動セット

## 投稿ログフォーマット

ファイル名: `data/posts/YYYY-MM-DD_HHmmss.json`

```json
{
  "created_at": "ISO8601",
  "scheduled_time": "ISO8601",
  "content": "投稿本文",
  "type": "tweet | thread",
  "hashtags": [],
  "status": "scheduled | posted | draft",
  "post_id": null,
  "metrics": {}
}
```

`status` の遷移: `scheduled` → （投稿後）`posted`
