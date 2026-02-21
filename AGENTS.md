# プロジェクトメモリ

## 技術スタック

- 開発環境: devcontainer
- Python 3.12
- Node.js 24 LTS
- LangChain 1.0+
- パッケージマネージャー: **uv** (Rustベースの高速パッケージマネージャー)
- Linter/Formatter: **Ruff 0.8.0+** (black + isort + flake8を統合)
- 主要ライブラリ:
  - LangChain, LangGraph
  - OpenAI, Anthropic, Google Generative AI
  - Chroma (ベクトルDB)
  - Transformers (Hugging Face)
  - 機械学習: NumPy, Pandas, scikit-learn

### ツールスタック

| 用途 | ツール |
|------|--------|
| パッケージ管理 | uv |
| Formatter | ruff format |
| Linter | ruff |
| Import整理 | ruff (I) |
| 型チェック | Pylance (basic) |

## 開発スタイル

**担当機能のみを実装する軽量スタイル**

- 永続ドキュメント（PRD、アーキテクチャ設計書など）の存在を前提としない
- 既存コードからパターンを自動検出して従う
- 担当機能のスコープに集中

## 実装フロー

`spec-*` スキル（`spec-design` / `spec-reviewer` / `spec-code-applier` / `spec-code-reviewer`）を**使用**して進める。

1. **Plan Mode 実施** → 結果を `.steering/.steering-{機能名}-{YYYYMMDD}/plan.md` に保存（`idea.md` があれば参照）
2. **設計作成** → `/spec-design {機能名} [--ref ...]` で `design.md` と `tasklist.md` を生成
3. **仕様レビュー** → `/spec-reviewer {機能名}` でレビューと承認ゲート管理
4. **実装** → `/spec-code-applier {機能名}` で `tasklist.md` に従って実装
5. **検証/コードレビュー** → `/spec-code-reviewer {機能名}` で品質確認とレビュー記録

## .steering/ 構造

```
.steering/
└── .steering-{機能名}-{YYYYMMDD}/
    ├── idea.md          # オプション: 事前に作成する自由メモ
    ├── plan.md          # Plan Mode結果
    ├── design.md        # spec-design で生成
    ├── tasklist.md      # spec-design で生成
    ├── spec-reviewed.md # spec-reviewer のレビュー記録
    └── code-reviewed.md # spec-code-reviewer のレビュー記録
```

## 基本ルール

- コメントやドキュメントは日本語
- `spec-*` スキルの実行前に、必ず `plan.md` を保存する
- `/spec-reviewer` の承認後に `/spec-code-applier` へ進む
- Grep で既存の類似実装を検索し、既存パターンを理解してから開始

## コミットメッセージ

コミットメッセージを作成する際は、以下のスキルを使用:

```
/create-commit
```

このスキルが規約に従った1行のみのコミットメッセージを生成し、コミットを実行します。
