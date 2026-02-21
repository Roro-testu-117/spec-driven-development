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

## ドキュメント分離方針

- `CLAUDE.md` はワークフローと運用ルールのみを記載する
- `skills` は手順・入出力契約のみを記載する
- `agents` は判定・生成責務のみを記載する
- 特定ドメイン（LLM）の詳細は `docs/llm/` を唯一の知識ソースとして参照する
- 変更時は対象ドキュメントのみを更新し、影響範囲を局所化する

## 開発スタイル

**担当機能のみを実装する軽量スタイル**

- 永続ドキュメント（PRD、アーキテクチャ設計書など）の存在を前提としない
- 既存コードからパターンを自動検出して従う
- 担当機能のスコープに集中

## 実装フロー（推奨）

`/implement-feature` を**公式導線**として使用する。

1. **Plan Mode 実施** → `.steering/.steering-{機能名}-{YYYYMMDD}/plan.md` を保存
2. **設計生成** → `spec-designer` で `design.md` と `tasklist.md` を作成
3. **仕様レビュー** → `spec-reviewer` で承認ゲート判定（非承認時は設計へ戻る）
4. **実装ループ** → `task-executor` が `tasklist.md` の未完了タスクを順次実装
5. **品質ゲート** → 各タスクごとに `quality-checker` で `OK/NG` 判定
6. **差し戻し規則** → 同一タスクで NG 3回時は `tasklist.md` を再分割し `spec-reviewer` 再承認へ戻す
7. **完了処理** → `code-reviewed.md` 更新後、必要時のみ `/create-commit` を案内

`task-decomposer` は互換のため残置しても、`/implement-feature` 標準フローでは使用しない。

## 互換フロー（非推奨）

以下の `spec-*` は互換のため残置するが、新規機能では推奨しない。

- `/spec-design`
- `/spec-reviewer`
- `/spec-code-applier`
- `/spec-code-reviewer`

新規実装は `/implement-feature` を優先すること。

## `.steering/` 構造

```
.steering/
└── .steering-{機能名}-{YYYYMMDD}/
    ├── idea.md          # オプション: 事前メモ
    ├── plan.md          # Plan Mode結果
    ├── design.md        # 設計
    ├── tasklist.md      # タスク一覧
    ├── spec-reviewed.md # 仕様レビュー記録
    └── code-reviewed.md # コードレビュー記録
```

## 基本ルール

- コメントやドキュメントは日本語
- `plan.md` がない場合は実装に進まない
- `spec-reviewer` 承認前に実装へ進まない
- ドメイン固有判断が必要な場合は `docs/llm/` を参照する

## コミットメッセージ

コミットメッセージを作成する際は以下のスキルを使用する。

```bash
/create-commit
```

このスキルが規約に従った1行のコミットメッセージを生成し、コミットを実行する。
