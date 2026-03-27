---
name: autopilot
description: PDCAサイクルを継続的に自律実行する。/loopと組み合わせて定期実行する想定。
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebSearch, WebFetch, Agent(director), Skill
---

自律運用モードを開始します。

## 手順

1. `data/posts/` の投稿数と `sns-profile.json` の API制限を確認
2. 本日の残り投稿枠を計算する
3. `/pdca-cycle` を実行する
4. 実行結果のサマリーを `data/reports/autopilot-YYYY-MM-DD.json` に保存

## 安全装置

- 月間API制限（1,500投稿）の90%に達したら新規投稿を停止し Check/Act のみ実行
- `content-review` で `block` 判定が連続3回出たら一時停止しログ出力
- エラー発生時はスタックトレースを `data/reports/errors/` に保存

## 定期実行

`/loop` と組み合わせて使う:
```
/loop 6h /autopilot
```
