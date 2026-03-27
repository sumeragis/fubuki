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
3. planner にコンテンツ企画を依頼
4. 企画を `data/campaigns/` に保存

### Phase 2: Do
5. writer に企画に基づく投稿を執筆させる
6. /content-review で品質チェック
7. 通過した投稿を `data/posts/` に保存
8. engagement にエンゲージメントアクション案を作成させる

### Phase 3: Check
9. monitor に過去投稿のメトリクスを収集させる（対象データがあれば）
10. evaluator にパフォーマンス評価を依頼
11. 評価レポートを `data/eval/reports/` に保存
12. `data/eval/baseline.json` を最新実績で更新

### Phase 4: Act
13. evaluator の `recommendations_for_optimizer` を確認
14. confidence: high の提案を optimizer に渡す
15. optimizer がナレッジ・プロンプトを更新
16. 変更を `data/prompt-versions/` にバージョン記録

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

### Check
- 対象投稿数: N件
- 平均エンゲージメント率: X%
- ベースライン比: +/-X%

### Act
- 最適化実施: あり/なし
- 変更ファイル: ...
- プロンプトバージョン: vXXX
```
