---
name: trend-research
description: Web検索でXのトレンド・競合情報をリサーチする。コンテンツ企画の前段階で使う。
allowed-tools: Read, Grep, WebSearch, WebFetch
---

`.claude/sns-profile.json` のジャンル設定に基づき、以下を調査してください。

## 調査項目

1. X上の現在のトレンドトピック
2. ジャンル内で話題のツイート・アカウント
3. 競合アカウントの最近の投稿傾向
4. 関連ニュース・イベント情報

## 出力

```json
{
  "date": "YYYY-MM-DD",
  "trends": [
    {
      "topic": "トピック名",
      "relevance": "high | medium | low",
      "source": "情報源URL",
      "angle": "このアカウントで取り上げる切り口の提案"
    }
  ]
}
```
