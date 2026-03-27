---
name: content-review
description: 投稿前の品質チェック。炎上リスク、ブランドトーン一貫性、文字数、NG表現を検証する。
allowed-tools: Read, Grep
---

投稿コンテンツを以下の観点でチェックし、判定を返してください。

## 入力パターン

`$ARGUMENTS` は2種類あります:

**A. 複数案（draftsあり）**: writer が出力した `tweet-drafts.json` / `thread-drafts.json` 形式
→ 全案をチェックし、ランク付けして推奨案を返す

**B. 単一案**: 選定済みの1案
→ その案のみチェックし、pass / warn / block を返す

## チェック項目

1. `.claude/sns-profile.json` の `prohibited_topics` / `prohibited_expressions` に該当しないか
2. `persona.tone` とトーンが一致しているか
3. 文字数制限（日本語140字 / スレッド各ツイート）を超えていないか
4. 攻撃的・差別的・誤解を招く表現がないか
5. `.claude/knowledge/content-review.md` の基準に適合しているか
6. フックが読者を止める力があるか（`guidelines/content-standards.md` 参照）

## 出力（パターンA: 複数案）

```json
{
  "mode": "multi",
  "results": [
    {
      "id": 1,
      "verdict": "pass | warn | block",
      "score": 85,
      "issues": [],
      "strengths": ["この案の強み"]
    }
  ],
  "ranking": [1, 3, 2, 5, 4],
  "recommendation": {
    "id": 1,
    "reason": "推奨する理由"
  }
}
```

## 出力（パターンB: 単一案）

```json
{
  "mode": "single",
  "verdict": "pass | warn | block",
  "issues": ["問題点（なければ空配列）"],
  "suggestions": ["修正提案（なければ空配列）"]
}
```

## 判定基準

- `pass`: チェック項目を満たす
- `warn`: 軽微な問題あり（修正推奨・投稿可）
- `block`: prohibited違反、誹謗中傷、根拠のない断言、文字数超過（投稿不可）

全案が `block` の場合は writer に差し戻す。
