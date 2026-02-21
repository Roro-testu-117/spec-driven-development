---
name: spec-design
description: plan.md から design.md と tasklist.md を生成するスキル
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task, AskUserQuestion
---

# 仕様設計スキル (spec-design)

**コンセプト**: plan.md を基に設計書とタスクリストを最小限で作成する

**引数**:
- 機能名（必須）: `.steering` 配下ディレクトリ名に使う
- `--ref`（推奨）: 参照するファイル・フォルダ

**使用例**:
```bash
/spec-design rag-ui --ref src/ui src/api
```

---

## 参照ドキュメント

| 作業 | 常に開くドキュメント | 参照するドキュメント |
|------|---------------------|---------------------|
| design.md 作成 | plan.md | --ref で指定したファイル |
| tasklist.md 作成 | plan.md, design.md | LLM生成機能の場合: rules/llm.md |

---

## `.steering/` ディレクトリ構造

```
.steering/
└── .steering-{機能名}-{YYYYMMDD}/
    ├── idea.md          # 任意
    ├── plan.md          # 事前に作成済み
    ├── design.md        # 本スキルで生成
    └── tasklist.md      # 本スキルで生成
```

---

## 手順

### ステップ1: 対象ディレクトリの決定

- `.steering/` が存在しなければ作成する。
- `.steering/.steering-{機能名}-{YYYYMMDD}` を作成する（YYYYMMDD は実行日）。
- 既存ディレクトリがある場合はユーザーにどれを使うか確認する。

---

### ステップ2: plan.md の読み込み

**参照**: `plan.md`

```
Read('.steering/.steering-{機能名}-{YYYYMMDD}/plan.md')
```

**作成手順**:
- 対象ディレクトリの `plan.md` を読み込み、要件の要点を把握する。
- `plan.md` が存在しない場合はユーザーに作成を依頼する。

---

### ステップ3: 既存パターンの検出

**参照**: `--ref` で指定されたファイル

- `--ref` が指定されていれば、参照ファイルから既存パターンを抽出する。
- `--ref` がない場合は主要ディレクトリから関連実装を探索する。
- 検出するパターン例:
  - ファイル構造・命名規則
  - エラーハンドリング方式
  - データフローパターン
  - 使用しているライブラリ・フレームワーク

---

### ステップ4: design.md の生成

**参照**: `plan.md`（常に開く）, `--ref ファイル`（パターン確認用）

**テンプレート**:
```
Read('.claude/skills/spec-design/templates/design.md')
```

**作成手順**:
1. plan.md を開いた状態で作業する
2. テンプレートの各セクションを埋める
3. design.md を対象ディレクトリに保存

---

### ステップ5: tasklist.md の生成

**参照**: `plan.md`（ゴール確認）, `design.md`（実装方針確認）

**テンプレート**:
```
Read('.claude/skills/spec-design/templates/tasklist.md')
```

**作成手順**:
1. plan.md と design.md を開いた状態で作業する
2. design.md の「変更ファイル一覧」を基にタスクを分解する
3. タスク構造は必ず以下に従う:
   - 大項目は `N.`、小項目は `N.M` の番号付きチェックリストで記述する
   - 小項目ごとに「成果物（ファイル）」と「Done条件」を必須で記載する
   - 前提タスクがある場合は `Depends on` を任意で記載する
4. タスク分割の基準:
   - 1小項目1責務で分割する
   - 1小項目の成果物（ファイル）が多すぎる場合（目安: 5件超）は分割する
   - Done条件が曖昧（例: 「動くこと」）な場合は条件を具体化する
5. LLM生成機能の場合は追加ルールを適用する
   - plan.md に LLM / プロンプト / 生成 / 評価 に関するキーワードが含まれる場合が対象
   - 判断が曖昧な場合は AskUserQuestion でユーザーに確認する
   ```
   Read('.claude/skills/spec-design/rules/llm.md')
   ```
6. 任意タスクは `[ ]* N.M ...` の形式で記載し、スキップ時は理由を残す
7. tasklist.md を対象ディレクトリに保存

---

### ステップ6: 完了報告

生成完了をユーザーに報告し、次のステップを案内する:

```
design.md と tasklist.md を生成しました。

【生成ファイル】
- .steering/.steering-{機能名}-{YYYYMMDD}/design.md
- .steering/.steering-{機能名}-{YYYYMMDD}/tasklist.md

次に /spec-reviewer {機能名} を実行して仕様レビューを受けてください。
```
