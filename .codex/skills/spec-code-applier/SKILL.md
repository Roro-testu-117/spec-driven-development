---
name: spec-code-applier
description: tasklist.md に従って実装するスキル
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task, AskUserQuestion
---

# 仕様実装スキル (spec-code-applier)

**コンセプト**: tasklist.md をそのまま順番に実装し、チェックボックスを更新する
**引数**:
- 機能名（必須）: `.steering` 配下ディレクトリ名に使う

**使用例**:
```bash
/spec-code-applier rag-ui
```

---

## 参照ドキュメント

| 作業 | 常に開くドキュメント | 参照するドキュメント |
|------|---------------------|---------------------|
| タスク実装 | **tasklist.md**（常時表示） | design.md（実装方針確認時） |
| 品質チェック | tasklist.md | - |

**重要**: tasklist.md は実装中ずっと開いた状態を維持し、タスク完了ごとに `[x]` を更新する

---

## 手順

1. **対象ディレクトリの決定**
   - `.steering/.steering-{機能名}-{YYYYMMDD}` を特定する。
   - 複数候補がある場合はユーザーに確認する。

2. **入力ドキュメントの確認**
   - `design.md` と `tasklist.md` を読み込む。
   - 不足があればユーザーに作成を依頼する。

3. **タスク実装**
   - `tasklist.md` の未完了タスクを順番に実装する。
   - タスク完了ごとに `[x]` へ更新する。
   - タスクが不明瞭な場合はユーザーに確認する。

4. **品質チェック**
   - `uv run pytest`
   - `uv run ruff check .`
   - `uv run ruff format --check .`
   - 失敗した場合は修正して再実行する。

5. **コミット**
   - ユーザーの指示があれば `/create-commit` を使用してコミットする。
   - タスク単位または適切な粒度でコミットする。

---

## エラー時の処理

品質チェック（uv run pytest, uv run ruff）が失敗した場合:

1. **エラー内容の確認**
   - エラーメッセージを確認し、修正が必要な箇所を特定する。

2. **修正と再実行**
   - 該当箇所を修正する。
   - 品質チェックを再実行する。
   - 成功するまで繰り返す。

3. **tasklist.md の更新**
   - 品質チェック成功後、フェーズ4のタスクを `[x]` に更新する。

---

## 完了報告

全タスク完了後:

1. **完了状態の確認**
   - `tasklist.md` の全タスクが `[x]` になっていることを確認する。
   - 品質チェック（uv run pytest, uv run ruff）がすべて成功していることを確認する。

2. **ユーザーへの報告**
   ```
   実装が完了しました。

   【完了状態】
   - tasklist.md: 全タスク完了
   - uv run pytest: 成功
   - uv run ruff check: 成功
   - uv run ruff format: 成功

   次に /spec-code-reviewer {機能名} を実行してコードレビューを受けてください。
   ```
