---
name: implement-feature
description: .steering 前提で設計から実装レビューまでを一気通貫で進める公式スキル
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task, AskUserQuestion
references:
  - docs/llm/README.md
  - docs/llm/architecture.md
  - docs/llm/conventions.md
  - .claude/agents/spec-designer.md
  - .claude/agents/spec-reviewer.md
  - .claude/agents/task-executor.md
  - .claude/agents/quality-checker.md
---

# 機能実装スキル (implement-feature)

## コンセプト

- 公式導線として `.steering` 配下で仕様駆動開発を完結する
- skill は手順と契約のみを保持し、ドメイン詳細は `docs/llm/` 参照で補う
- Agents モードを標準運用とする（`spec-designer` / `spec-reviewer` / `task-executor` / `quality-checker`）
- `templates/` と `supporting-files/` は使用しない（本ファイルと agent 定義にフォーマットを内包する）
- `task-decomposer` は互換のため残置しても、標準フローでは使用しない

## 引数

- 機能名（必須）: `.steering/.steering-{機能名}-{YYYYMMDD}` の解決に使用
- `--ref`（任意）: 既存実装の参照パス。設計・レビューの根拠に使う

## 前提条件

1. `plan.md` が存在すること
2. 参照が必要なドメイン知識は `references` に列挙された `docs/llm/*.md` から取得すること
3. 既存コード探索（Grep）でパターンを確認してから設計・実装すること

## 成果物

対象ディレクトリ: `.steering/.steering-{機能名}-{YYYYMMDD}/`

- `design.md`
- `tasklist.md`
- `spec-reviewed.md`
- `code-reviewed.md`

## 成果物フォーマット（厳守）

### design.md

```markdown
# Design: {機能名}

## 背景
## 目的
## スコープ
## 非スコープ
## 変更対象ファイル
- `path/to/file`

## 実装方針
## テスト方針
## リスクと対策
```

### tasklist.md

```markdown
# Tasklist: {機能名}

## 実装タスク
- [ ] T1: {タスク名}
- [ ] T2: {タスク名}

## タスク詳細
| Task ID | 内容 | 対象ファイル | Done条件 | 状態 | NG回数 |
|---------|------|--------------|----------|------|--------|
| T1 | ... | ... | ... | 未着手 | 0 |

## 検証タスク
- [ ] V1: `uv run pytest`
- [ ] V2: `uv run ruff check .`
- [ ] V3: `uv run ruff format --check .`
```

### spec-reviewed.md

```markdown
# Spec Review Log: {機能名}

## 第1回レビュー（YYYY-MM-DD HH:MM）
### 観点別評価
| 観点 | 評価 | コメント |
|------|------|----------|
| 要件適合 | OK / Needs Fix | ... |
| 設計整合 | OK / Needs Fix | ... |
| タスク妥当性 | OK / Needs Fix | ... |

### 指摘事項
- {指摘内容}

### 総合判定
Approved / Changes Requested
```

### code-reviewed.md

```markdown
# Code Review Log: {機能名}

## タスク別判定履歴
| Task ID | 判定 | 重大度 | NG回数 | 対応状態 | 備考 |
|---------|------|--------|--------|----------|------|
| T1 | OK / NG | Blocker / Major / Minor / - | 0 | Open / Fixed | ... |

## 指摘事項と修正結果
- {ファイル}:{行} - {指摘} -> {修正結果}

## 総合判定
OK / Needs Fix
```

## 実行フロー

1. **対象ディレクトリ解決**
   - 当日の日付を使って `.steering/.steering-{機能名}-{YYYYMMDD}/` を決定する
   - `plan.md` の存在を確認し、不在なら停止する

2. **仕様設計**
   - `spec-designer` を実行して `design.md` と `tasklist.md` を生成する

3. **仕様レビュー（承認ゲート）**
   - `spec-reviewer` を実行して `spec-reviewed.md` に結果を記録する
   - 判定が `Changes Requested` の間は設計フェーズに戻る

4. **タスク実装ループ（tasklist.md ベース）**
   - 未完了タスク（`[ ]`）を1件選択し `task-executor` を実行する
   - 実装後に `quality-checker` を実行し判定を受ける
   - `OK`: 対象タスクを `[x]` に更新して次のタスクへ進む
   - `NG` 1〜2回: 同一タスクを修正して再レビュー
   - `NG` 3回: 対象タスクを `tasklist.md` 上で再分割し、`spec-reviewer` の再承認を受ける

5. **完了処理**
   - 全タスク完了後、`tasklist.md` の対応項目を `[x]` に更新
   - `code-reviewed.md` の総合判定を更新
   - 必要時のみ `/create-commit` を案内する（自動コミットしない）

## 異常系

- `plan.md` 不在: 処理を中止して作成を依頼
- `spec-reviewer` 非承認: 実装フェーズへ進めない
- 品質チェックが失敗: 問題点を記録して再実装
- 同一タスクで NG 3回: タスクを再分割し、レビューゲートに戻る
- スコープ逸脱が必要: 設計を更新してレビューゲートへ戻る

## 完了条件

1. `design.md`, `tasklist.md`, `spec-reviewed.md`, `code-reviewed.md` が存在
2. `tasklist.md` の実装タスクと検証タスクが完了状態
3. `code-reviewed.md` の総合判定が `OK`
4. ユーザーに `/create-commit` 実行を案内済み

## 使用例

```bash
/implement-feature sample-feature --ref src
```

実行時は `references` に列挙された `docs/llm/*.md` を必要に応じて参照し、ドメイン判断を行う。
