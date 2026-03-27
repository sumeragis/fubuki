---
name: publish-scheduled
description: スケジュール済みの投稿を時間通りにX APIで投稿する。best_timesのcronで起動される。
allowed-tools: Read, Write, Glob, Grep, Bash, WebSearch, WebFetch
---

`data/posts/` からスケジュール済み投稿を探し、X API で投稿する。

## 手順

1. 現在時刻を取得する
2. `data/posts/` から `status: "scheduled"` かつ `scheduled_time <= 現在時刻` の投稿を探す
3. 該当投稿がなければ何もせず終了する
4. 投稿前に `/content-review` で最終チェックを実行する（`pass` または `warn` なら投稿可）
   - `block` の場合は投稿せずにログに記録し、ユーザーに通知する
5. X API で投稿を実行する
6. 成功したら投稿ファイルを更新する:
   - `status: "posted"`
   - `posted_at`: 実際の投稿時刻
   - `post_id`: X API が返す投稿ID
7. 失敗した場合:
   - エラー内容を `data/reports/errors/` に記録する
   - `status: "failed"` に更新する
   - ユーザーに通知する

## 注意

- 1回の起動で投稿するのは1件のみ（同じ時間帯に複数ある場合は最初の1件）
- `api.monthly_post_limit` を超えている場合は投稿せずに警告を出力する
