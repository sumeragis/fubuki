---
name: content-review
description: 投稿前の品質チェック。炎上リスク、ブランドトーン一貫性、文字数、NG表現を検証する。
allowed-tools: Read, Grep
---

投稿コンテンツを以下の観点でチェックし、判定を返してください。

## チェック項目

1. `.claude/sns-profile.json` の `prohibited_topics` / `prohibited_expressions` に該当しないか
2. `persona.tone` とトーンが一致しているか
3. 文字数制限（140字 / スレッド各ツイート）を超えていないか
4. 攻撃的・差別的・誤解を招く表現がないか
5. `.claude/knowledge/content-review.md` の基準に適合しているか

## 入力

$ARGUMENTS に投稿コンテンツのJSONを受け取る。

## 出力

```json
{
  "verdict": "pass | warn | block",
  "issues": ["問題点の一覧（なければ空配列）"],
  "suggestions": ["修正提案（なければ空配列）"]
}
```

- `pass`: 投稿OK
- `warn`: 注意点あり（修正推奨だが投稿可）
- `block`: 投稿NG（要修正、投稿してはならない）
