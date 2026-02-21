# docs/llm

LLMアプリケーション向けのドメイン知識を集約するディレクトリ。

## 目的

- `skills` と `agents` を汎用化し、ドメイン固有内容をここへ分離する
- 変更影響を局所化し、更新対象を最小化する

## 参照順序

1. `docs/llm/README.md`（全体方針）
2. `docs/llm/architecture.md`（構成原則）
3. `docs/llm/conventions.md`（実装・レビュー規約）

## 運用ルール

- 新しいドメインルールは `skills/agents` に直接書かず、本ディレクトリへ追加する
- `skills/agents` 側は front matter の `references:` に必要ファイルのみ列挙する
- 汎用ルール（フロー、入出力契約、判定ロジック）は `skills/agents` 側に保持する
