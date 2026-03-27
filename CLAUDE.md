# Fubuki - X SNS自律運用システム

Xアカウント「1nichitaisetsu」の運用を完全自動化する。
PDCAサイクルを自律的に回し、フォロワー・インプレッション・エンゲージメントを最大化する。

## ルール

- 投稿前に必ず `/content-review` で品質チェックする
- X API Free tier: 月1,500投稿を超えない
- `sns-profile.json` の `prohibited_topics` / `prohibited_expressions` に該当する投稿は絶対にブロック
- プロンプト・ナレッジの変更は `data/prompt-versions/` にバージョン記録する
- optimizer の自動適用は confidence: high のみ。medium は director が判断

## 設定ファイル

- アカウント設定: `.claude/sns-profile.json`
- ナレッジベース: `.claude/knowledge/*.md`（optimizerが自動更新する）
- 評価ベースライン: `data/eval/baseline.json`
