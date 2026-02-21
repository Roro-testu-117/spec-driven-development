---
name: spec-reviewer
description: 仕様レビューと承認ゲートを管理するスキル
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task, AskUserQuestion
---

# 仕様レビュースキル (spec-reviewer)

**コンセプト**: plan.md・design.md・tasklist.md の整合性を評価し、承認ゲートを管理する

**引数**:
- 機能名（必須）: `.steering` 配下ディレクトリ名に使う

**使用例**:
```bash
/spec-reviewer rag-ui
```

---

## 参照ドキュメント

| 作業 | 常に開くドキュメント | 参照するドキュメント |
|------|---------------------|---------------------|
| 仕様レビュー | plan.md, design.md, tasklist.md | evaluation-criteria.md |

**レビュー時は3つのドキュメントを並行して確認し、整合性を評価する**

---

## 手順

1. **対象ディレクトリの決定**
   - `.steering/.steering-{機能名}-{YYYYMMDD}` を特定する。
   - 複数候補がある場合はユーザーに確認する。

2. **入力ドキュメントの確認**
   - `plan.md`、`design.md`、`tasklist.md` を読み込む。
   - 不足があればユーザーに作成を依頼する。

3. **仕様レビューの実施**
   - 評価軸は以下を参照:
     ```
     Read('.codex/skills/spec-reviewer/templates/evaluation-criteria.md')
     ```
   - 各観点を ✓ / △ / ✗ で評価し、コメントを付与する。

4. **spec-reviewed.md への記録**
   - レビュー結果を `spec-reviewed.md` に追記する。
   - 「第N回レビュー（YYYY-MM-DD HH:MM）」の形式で追記する。
   - テンプレートは以下を参照:
     ```
     Read('.codex/skills/spec-reviewer/templates/spec-reviewed.md')
     ```

5. **承認ゲート管理**
   - 全項目が ✓ または △ の場合は「承認可能」と判定する。
   - ✗ がある場合は「修正後に再レビュー」と判定する。

---

## 修正ループ

✗ がある場合の処理:

1. **修正依頼の記録**
   - `spec-reviewed.md` の「指摘事項」に修正が必要な箇所を具体的に記載する。
   - 対象ファイル（design.md または tasklist.md）と修正内容を明記する。

2. **ユーザーへの報告**
   ```
   仕様レビュー完了: 修正が必要です。

   【修正が必要な箇所】
   - {対象ファイル}: {修正内容}

   修正後、再度 /spec-reviewer {機能名} を実行してください。
   ```

3. **再レビュー**
   - ユーザーが修正後に `/spec-reviewer` を再実行する。
   - 「第N回レビュー」として追記形式で記録する。

---

## 承認後の次ステップ

全項目が ✓ または △ の場合:

1. **ユーザーへ承認確認**
   ```
   仕様レビュー完了: 承認可能です。

   【レビュー結果サマリー】
   - 要件網羅性: ✓
   - ファイル一覧: ✓
   - 実装方針: ✓
   - タスク粒度: ✓
   - 完了条件: ✓

   この仕様で実装を開始してよろしいですか？
   承認後、/spec-code-applier {機能名} を実行してください。
   ```

2. **承認後のアクション**
   - ユーザーが承認したら、`spec-reviewed.md` の「総合判定」を「承認可能」にチェックする。
   - 次のスキル `/spec-code-applier {機能名}` の実行を案内する。
