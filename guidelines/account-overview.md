# アカウント概要

全エージェントが作業前に読む必須マニュアルです。
具体的なペルソナ・ジャンル・禁止設定は `.claude/sns-profile.json` を参照してください。

## このシステムとは

「Fubuki」はXアカウント「1nichitaisetsu」のSNS自律運用システムです。
PDCAサイクルを自律的に回し、フォロワー・インプレッション・エンゲージメントを継続的に最大化します。

## ミッション

- 質の高いフォロワーを継続的に獲得する
- 投稿ごとのエンゲージメント率を改善し続ける
- アカウントの信頼性と一貫性を長期的に積み上げる

## 全エージェント共通ルール

1. **投稿前に必ず `/content-review` を実行する** — これを省略しない
2. `sns-profile.json` の `prohibited_topics` / `prohibited_expressions` を厳守する
3. 月間投稿数が1,500件（X API Free tier上限）を超えないようにする
4. 変更・記録・生成したものは必ずファイルに保存する
5. 判断に迷ったら `guidelines/escalation-rules.md` を確認してから動く

## 設定ファイルの読み方

| ファイル | 何が書いてあるか |
|---------|----------------|
| `.claude/sns-profile.json` | ペルソナ、トーン、一人称、ジャンル、禁止設定、投稿頻度 |
| `.claude/knowledge/writer.md` | writerが使う成功パターン・バズる型（optimizerが育てる） |
| `.claude/knowledge/planner.md` | plannerが使う企画ノウハウ・成功事例（optimizerが育てる） |
| `.claude/knowledge/engagement.md` | engagementが使う交流戦略（optimizerが育てる） |
| `data/eval/baseline.json` | パフォーマンスの基準値 |

## マニュアル一覧

| マニュアル | 主な読者 |
|-----------|---------|
| `guidelines/brand-guidelines.md` | writer, engagement, planner |
| `guidelines/content-standards.md` | writer, evaluator, content-review |
| `guidelines/posting-strategy.md` | planner, director |
| `guidelines/collaboration-protocol.md` | 全エージェント |
| `guidelines/escalation-rules.md` | 全エージェント |
