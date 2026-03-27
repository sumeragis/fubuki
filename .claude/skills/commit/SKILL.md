---
name: commit
description: コードの変更をコミット＆プッシュする。「コミットして」「変更を保存して」「commitして」「pushして」と言われた場合に使用。
argument-hint: コミットメッセージ（省略可）
---

変更内容をgitコミットし、リモートにプッシュする。

## 手順

1. `git status` で変更ファイルを確認
2. `git diff` と `git diff --staged` で差分を確認
3. `git log --oneline -5` で既存のコミットメッセージスタイルを確認
4. `.env`、credentials、秘密鍵が含まれていないことを確認
5. `git add <specific-files>` で個別にステージング（`git add .` は使わない）
6. 変更内容を分析し、リポジトリのスタイルに合ったメッセージでコミット
7. `git push` でリモートにプッシュ

## コミットメッセージ

```bash
git commit -m "$(cat <<'EOF'
メッセージ本文

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
EOF
)"
```

- 「なぜ」を重視し、「何を」は差分から読み取れるので簡潔に
- add = 新機能、update = 既存機能の改善、fix = バグ修正

## 禁止事項

- `git add .` や `git add -A` は使わない
- `--no-verify` でフックをスキップしない
- `--amend` は原則使わない（新しいコミットを作成する）
- `.env` や秘密鍵をステージングしない
- `git push --force` を main/master に実行しない
