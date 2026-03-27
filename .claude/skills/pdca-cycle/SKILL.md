---
name: pdca-cycle
description: PDCAサイクルを1回分自律実行する。Plan→Do→Check→Actの全工程をdirectorが統括し、投稿生成から評価・最適化まで一気通貫で回す。
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebSearch, WebFetch, Agent(director)
---

PDCAサイクルを1回分実行します。

## 実行フロー

director エージェントを起動し、以下を順に実行させてください。

### Phase 1: Plan
1. `data/eval/reports/` の最新評価レポートを確認（あれば）
2. `data/prompt-versions/changelog.json` で前回の最適化内容を確認
3. analyst と monitor を**並列起動**（外部動向 + 内部メトリクスを同時取得）
4. 両結果が揃ってから planner にコンテンツ企画を依頼
5. 企画を `data/campaigns/` に保存

### Phase 2: Do
6. writer に企画に基づく投稿を**最低5案**執筆させる
7. `/content-review` に全案を渡し、各案を評価・ランキング → 最高評価案を自動選定
   - block 判定の案は除外、全案 block の場合はその投稿をスキップしてログ記録
8. 選定案を `data/posts/` に保存（status: "scheduled" または即時投稿）
9. engagement にエンゲージメントアクション案を作成させ、`data/engagement/` に保存する

### Phase 3: Check
10. monitor に過去投稿のメトリクスを収集させる（対象データがあれば）
11. evaluator にパフォーマンス評価を依頼（投稿メトリクス + エンゲージメントアクション結果）
12. 評価レポートを `data/eval/reports/` に保存
13. `data/eval/baseline.json` を最新実績で更新

### Phase 4: Act
14. evaluator の `recommendations_for_optimizer` を確認
15. confidence: high の提案を optimizer に渡す
16. optimizer がナレッジ・プロンプトを更新
17. 変更を `data/prompt-versions/` にバージョン記録

### 完了時出力

サイクル実行サマリーを以下の形式で出力:

```
## PDCA Cycle Summary - YYYY-MM-DD

### Plan
- 企画数: N件
- テーマ: ...

### Do
- 投稿数: N件
- content-review結果: pass N / warn N / block N
- エンゲージメントアクション数: N件

### Check
- 対象投稿数: N件
- 平均エンゲージメント率: X%
- ベースライン比: +/-X%

### Act
- 最適化実施: あり/なし
- 変更ファイル: ...
- プロンプトバージョン: vXXX
```
