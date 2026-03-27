# Fubuki アーキテクチャ

最終更新: 2026-03-27

---

## 設計思想

大滝さんの「仮想チーム」設計手法（[参考記事](https://x.com/showheyohtaki/status/2037465089318793570)）を適用し、X SNS自律運用組織をゼロベースで再設計した。

### 3つの核心原則

**1. 司令塔は動かない**
CLAUDE.md はルーティングのみ。自分でアウトプットを作らない。
「1つのAIに全部やらせると70点の人になる」を避けるための設計。

**2. 生成と評価を分離する**
writerが書いたコンテンツをwriterが評価しない。
evaluatorは常に生成者とは独立した視点で評価する。

**3. ナレッジは外部ファイルで育てる**
エージェント定義ファイルに選択肢リストを書かない。
フックの型・コンテンツ種類・交流アクション等は `knowledge/` に置き、
ユーザーと optimizer が実績に基づいて育てる。

---

## ファイル構成

```
fubuki/
├── CLAUDE.md                        # 司令塔（軽量ルーティングテーブル）
├── guidelines/                      # 全エージェント共通の研修マニュアル
│   ├── account-overview.md          # 共通ルール・設定ファイルの読み方
│   ├── brand-guidelines.md          # トーン規則・絶対NG表現
│   ├── content-standards.md         # 良い投稿の定義・content-review判定基準
│   ├── posting-strategy.md          # 頻度・タイミング・フォーマット戦略の原則
│   ├── collaboration-protocol.md    # エージェント間フロー・ファイル命名規則
│   └── escalation-rules.md          # 自律判断OK / 要確認 / 即停止の分類
├── templates/                       # 出力フォーマットの統一テンプレート
│   ├── tweet-drafts.json            # 単発ツイート複数案
│   ├── thread-drafts.json           # スレッド複数案
│   ├── content-plan.json            # 週次コンテンツ計画
│   ├── eval-report.json             # 評価レポート
│   └── pdca-summary.md              # PDCAサイクルサマリー
├── .claude/
│   ├── agents/                      # エージェント定義（人格・役割・判断基準）
│   │   ├── director.md              # opus / PDCA統括オーケストレーター
│   │   ├── analyst.md               # sonnet / 外部調査専門
│   │   ├── planner.md               # sonnet / コンテンツ企画
│   │   ├── writer.md                # sonnet / 投稿執筆
│   │   ├── engagement.md            # sonnet / コミュニティ交流
│   │   ├── monitor.md               # sonnet / メトリクス収集
│   │   ├── evaluator.md             # sonnet / 成果評価
│   │   └── optimizer.md             # opus / ナレッジ最適化
│   ├── commands/                    # スラッシュコマンド（ユーザー起動）
│   │   ├── post.md                  # /post  単発ツイート生成
│   │   ├── campaign.md              # /campaign  一括コンテンツ生成
│   │   ├── report.md                # /report  パフォーマンスレポート
│   │   └── autopilot.md             # /autopilot  継続自律実行
│   ├── skills/                      # スキル（エージェントまたはユーザーが起動）
│   │   ├── content-review/          # /content-review  投稿品質チェック
│   │   ├── pdca-cycle/              # /pdca-cycle  PDCAサイクル1回実行
│   │   └── trend-research/          # /trend-research  トレンド調査
│   ├── knowledge/                   # optimizerが育てるナレッジ（ユーザーも編集可）
│   │   ├── writer.md                # フックの型・成功パターン・NG表現
│   │   ├── planner.md               # コンテンツ種類・比率・企画成功事例
│   │   ├── engagement.md            # 交流アクション種類・成功パターン
│   │   ├── content-review.md        # 品質チェック基準の追加ルール
│   │   └── analyst.md               # 調査フレームワーク・業界固有知識
│   └── sns-profile.json             # アカウント設定（ペルソナ・禁止設定・投稿頻度）
└── data/
    ├── posts/                       # 投稿ログ（YYYY-MM-DD_HHmmss.json）
    ├── metrics/                     # メトリクス収集結果
    ├── eval/
    │   ├── baseline.json            # パフォーマンス基準値
    │   └── reports/                 # 評価レポート（YYYY-MM-DD.json）
    ├── campaigns/                   # キャンペーン別コンテンツ
    ├── prompt-versions/             # ナレッジ変更の差分・バージョン履歴
    └── reports/                     # 自動運用ログ・エラーログ
```

---

## 組織図

```
                    ┌──────────────┐
                    │   CLAUDE.md  │  司令塔（ルーティングのみ・自分で作業しない）
                    └──────┬───────┘
                           │
        ┌──────────┬───────┼───────┬──────────┐
        │          │       │       │          │
   ┌────▼───┐ ┌───▼───┐ ┌▼─────┐ │    ┌─────▼─────┐
   │analyst │ │planner│ │writer│ │    │engagement │
   │外部調査│ │企画   │ │執筆  │ │    │交流       │
   └────────┘ └───────┘ └──────┘ │    └───────────┘
                                  │
                    ┌─────────────┼─────────────┐
                    │             │             │
               ┌────▼───┐  ┌────▼─────┐  ┌────▼─────┐
               │monitor │  │evaluator │  │optimizer │
               │収集    │  │評価      │  │改善      │
               └────────┘  └──────────┘  └──────────┘
```

---

## PDCAワークフロー

```
[Plan]
  analyst（外部調査）──┐  ← 並列起動
                        ├→ planner（企画立案）→ content-plan.json
  monitor（過去データ）──┘

[Do]
  planner → writer（複数案生成）→ 選定（director or ユーザー）
  → /content-review → data/posts/ → engagement（交流アクション）

[Check]
  monitor（メトリクス収集）→ evaluator（成果評価）→ data/eval/reports/

[Act]
  evaluator → optimizer（ナレッジ更新）→ data/prompt-versions/
  ↑___________________________次サイクルへ___________________________↑
```

---

## エージェント詳細

| エージェント | モデル | 人格 | 主な役割 |
|-------------|--------|------|---------|
| **director** | opus | 経営者視点の実行者 | PDCA全体を統括。自分では動かない |
| **analyst** | sonnet | データジャーナリスト | 外部情報のみ調査。事実と推測を区別 |
| **planner** | sonnet | 編集長 | トレンド×過去データで企画立案 |
| **writer** | sonnet | コピーライター | 複数案生成（最低5案）。1行目に執着 |
| **engagement** | sonnet | コミュニティマネージャー | 宣伝臭なしの本物の交流 |
| **monitor** | sonnet | データエンジニア | 数字を正確に記録。解釈しない |
| **evaluator** | sonnet | データサイエンティスト | 生成者と独立した視点で評価。相関と因果を区別 |
| **optimizer** | opus | チーフエディター | 最小単位で変更。常にロールバック可能な状態を保つ |

---

## 重要な設計判断とその理由

### writer は必ず複数案を出す

1案だけ出すのは「最初に思いついたものが最善」という前提に立つことになる。
SNS投稿の品質はフックでほぼ決まるため、切り口の異なる複数案（最低5案）を出し、
比較・選定してから content-review に渡す。

**フロー:** writer（5案以上）→ 選定 → content-review（1案）→ 投稿

### 選択肢リストはエージェント定義に書かない

エージェント定義に「フックの型はこの6種類」と書くと、AIはその6種類しか使わなくなる。
フックの型・コンテンツ種類・交流アクション等は `knowledge/` に置き、
「knowledge を参照しつつ、そこにない型も試せ」という設計にする。

### 設定とナレッジは2層に分ける

| レイヤー | ファイル | 更新者 | 内容 |
|---------|---------|--------|------|
| 設定層 | `sns-profile.json` | ユーザー | ペルソナ・禁止設定・投稿頻度 |
| ナレッジ層 | `knowledge/*.md` | optimizer + ユーザー | 実績ベースの成功パターン |
| 戦略層 | `guidelines/*.md` | ユーザー | 普遍的な原則・ルール |

### optimizer の変更は最大2ファイル/回

変数を統制して効果測定を可能にするため。
同時に多数のファイルを変えると「何が効いたか」がわからなくなる。

---

## ガードレール

| ルール | 理由 |
|--------|------|
| 月1,500投稿上限 | X API Free tier 制限 |
| `prohibited_topics` / `prohibited_expressions` 違反 → 即ブロック | ブランド保護 |
| optimizer 自動適用は `confidence: high` のみ | 品質変化の責任所在を明確にする |
| content-review を必ず通す | 自動化の暴走を防ぐ最後の砦 |
| block が連続3回 → 一時停止 | 根本的な問題が起きているサインのため |

---

## knowledge/ の育て方

`knowledge/` は「作って終わり」ではなく運用しながら育てるもの。

- **optimizer が追記する**: evaluator の分析結果から成功パターンを蓄積
- **ユーザーが追記する**: 「この表現はNGだな」「この型は反応が良い」と気づいたとき
- **定期的に見直す**: 古くなったパターンを削除・更新する

knowledge が育つほど、エージェントの出力が「このアカウントらしい」ものになっていく。
