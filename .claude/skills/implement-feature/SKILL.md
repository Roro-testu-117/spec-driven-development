---
name: implement-feature
description: 担当機能のみを設計から実装まで完結させる軽量スキル
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Task, AskUserQuestion
---

# 機能実装スキル (implement-feature)

**コンセプト**: 既存プロジェクトへの途中参加に対応する軽量スキル

- 永続ドキュメント（プロダクト要求仕様書、アーキテクチャ設計書など）の存在を前提としない
- 既存コードからパターンを自動検出
- `feature-docs/` 内で設計・実装を完結
- 担当機能スコープに集中

**引数**:
- 機能名（必須）: 実装する機能の名前
- `--ref`（推奨）: コンテキスト収集時に参照するファイル・フォルダを指定

**使用例**:
```bash
# 基本
/implement-feature RAG検索機能

# 参照パスを指定（推奨）
/implement-feature RAG検索機能 --ref src/chains/qa_chain.py src/retrievers/vector_store.py
```

---

## feature-docs/ ディレクトリ構造

```
feature-docs/
├── idea.md          # ステップ0: ユーザーが作成（自由形式、オプション）
├── context.md       # ステップ1: 自動生成（指定パスからの解析結果）
├── requirements.md  # ステップ2: 要件整理
├── design.md        # ステップ3: 設計
└── tasklist.md      # ステップ4: タスクリスト
```

---

## ステップ0: アイデア確認（オプション）

### 目的
ユーザーが事前に作成した `feature-docs/idea.md` があれば読み込み、要求の背景を理解する。

### 手順

1. **feature-docs/ ディレクトリの確認・作成**
   ```
   feature-docs/ が存在しなければ作成
   ```

2. **idea.md の確認**
   - `feature-docs/idea.md` が存在するか確認1
   - 存在する場合: 内容を読み込み、要求の背景・制約・参考情報を理解
   - 存在しない場合: ユーザーに簡単な要求内容をヒアリング

### idea.md の内容例（ユーザーが作成）

```markdown
# RAG検索機能

## 背景
- 現在は単純なプロンプトベースの応答のみ
- ドキュメントベースの正確な回答が必要

## やりたいこと
- ベクトルDB（Chroma）を使った類似文書検索
- LangChainのRetrievalQAを使った回答生成
- 検索結果のソース表示機能

## 参考
- 既存のドキュメントローダーを活用
- LangChain公式ドキュメント: https://python.langchain.com/docs/use_cases/question_answering/

## 制約
- 既存のOpenAI API使用を継続
- メモリ使用量を抑えるため、チャンク分割は512トークンまで
```

---

## ステップ1: コンテキスト収集

### 目的
`--ref` で指定されたパスのコードを読み込み、既存パターンを検出して `feature-docs/context.md` に記録する。

### 手順

1. **参照パスの確認**
   - `--ref` で指定されたファイル・フォルダを特定
   - 指定がない場合: プロジェクトルートの主要ディレクトリ（src/、app/ など）を確認

2. **コード読み込みと解析**

   各参照パスについて以下を解析:
   - ファイル構造・命名規則
   - Chain/Agent/Runnable の実装パターン
   - プロンプトテンプレートのスタイル
   - テストの書き方（存在する場合）
   - LLM API呼び出しパターン（OpenAI、Anthropic、Google等）
   - エラーハンドリング・リトライパターン
   - ベクトルDB・Retriever の使用パターン
   - メモリ管理パターン（ConversationBufferMemory等）
   - ツール/関数呼び出しパターン

3. **context.md の生成**

   以下のテンプレートに従って `feature-docs/context.md` を作成:

   ```
   Read('.claude/skills/implement-feature/templates/context.md')
   ```

4. **ユーザーへの確認**
   ```
   「context.md を生成しました。検出したパターンを確認してください。
   追加の参照ファイルや修正があればお知らせください。」
   ```

**このステップが完了したら、ただちにステップ2に進む。**

---

## ステップ2: 要件整理

### 目的
idea.md（あれば）と context.md の情報を基に、要件を構造化して `feature-docs/requirements.md` に記録する。

### 手順

1. **情報の統合**
   - idea.md からの要求・背景・制約
   - context.md からの技術的制約・既存パターン
   - ユーザーからの追加情報

2. **requirements.md の生成**

   以下のテンプレートに従って `feature-docs/requirements.md` を作成:

   ```
   Read('.claude/skills/implement-feature/templates/requirements.md')
   ```

3. **ユーザー承認を得る（必須）**
   ```
   「requirements.md の作成が完了しました。内容を確認してください。
   承認いただけたら設計フェーズに進みます。」
   ```

**ユーザーの承認を得てからステップ3に進む。**

---

## ステップ3: 設計

### 目的
既存パターンに従った設計を行い、`feature-docs/design.md` に記録する。

### 手順

1. **設計方針の決定**
   - context.md で検出したパターンに従う
   - 既存コードとの一貫性を最優先
   - 影響範囲を最小限に抑える

2. **design.md の生成**

   以下のテンプレートに従って `feature-docs/design.md` を作成:

   ```
   Read('.claude/skills/implement-feature/templates/design.md')
   ```

3. **ユーザー承認を得る（必須）**
   ```
   「design.md の作成が完了しました。内容を確認してください。
   承認いただけたら実装フェーズに進みます。」
   ```

**ユーザーの承認を得てからステップ4に進む。**

---

## ステップ4: 実装

### 目的
tasklist.md を作成し、全タスクを完了させる。

### 手順

1. **tasklist.md の生成**

   以下のテンプレートに従って `feature-docs/tasklist.md` を作成:

   ```
   Read('.claude/skills/implement-feature/templates/tasklist.md')
   ```

   **重要**: 冒頭の「運用ルール（3-agent loop）」セクションは削除・改変しない

2. **実装モードの選択**

   ユーザーに実装モードを確認:

   ```
   AskUserQuestion(
     questions: [
       {
         question: "実装モードを選択してください",
         header: "実装モード",
         multiSelect: false,
         options: [
           {
             label: "モードA: Agents 版（推奨）",
             description: "既存の 3-agent loop を使用。独立した会話コンテキストと権限制御により、安定した実装が可能。"
           },
           {
             label: "モードB: 統合版（実験的）",
             description: "スキル内統合ロジックを使用。Agents を呼び出さず、スキル内で直接実装を進める。"
           }
         ]
       }
     ]
   )
   ```

---

### モードA: Agents 版（3-agent loop）

**このモードは既存の 3-agent loop を使用します。**

#### 4-A-1. task-decomposer でCT分解

```
Task(
  subagent_type: "task-decomposer",
  description: "CT decomposition",
  prompt: "以下のドキュメントを読み込み、tasklist.md をCT（Commit Task）単位に分解してください。
           - feature-docs/requirements.md
           - feature-docs/design.md
           - feature-docs/tasklist.md

           CTテンプレート: .claude/skills/implement-feature/templates/ct-template.md
           出力先: feature-docs/task-decomposed/ct-001.md, ct-002.md, ..."
)
```

#### 4-A-2. CT実装ループの開始

**ループ開始:**

**4-A-2-1. 未完了CTの確認**
- `feature-docs/task-decomposed/` 内のCTファイルを確認
- ステータスが「未着手」または「差し戻し」のCTを特定
- **全CTが「完了」の場合**: ループ終了、ステップ5へ

**4-A-2-2. task-executor でCT実装**
```
Task(
  subagent_type: "task-executor",
  description: "CT-XXX implementation",
  prompt: "以下のCTを実装してください。
           - CT: feature-docs/task-decomposed/ct-XXX.md
           - 設計資料: feature-docs/design.md
           - コンテキスト: feature-docs/context.md

           実装完了後、CTファイルのステータスを「レビュー待ち」に更新し、
           実装サマリーを記入してください。
           **コミットは行わないでください**（quality-checker のレビュー後に実行）"
)
```

**4-A-2-3. quality-checker でレビュー**
```
Task(
  subagent_type: "quality-checker",
  description: "CT-XXX review",
  prompt: "以下のCTのレビューを実施してください。
           - CT: feature-docs/task-decomposed/ct-XXX.md
           - 設計資料: feature-docs/design.md
           - git diff で変更内容を確認

           品質チェックコマンド:
           - ruff check .
           - pytest（該当する場合）

           結論（OK / NG）と根拠を報告してください。"
)
```

**4-A-2-4. レビュー結果に基づく分岐**

- **OK の場合**:
  1. task-executor をモードBで呼び出し（CT完了処理）:
     ```
     Task(
       subagent_type: "task-executor",
       description: "CT-XXX finalization",
       prompt: "quality-checker のOK判定を受けました。
               CTを完了状態にしてください。
               併せて tasklist.md の該当タスクに [x] を付けてください（CT完了時）。
               CT: feature-docs/task-decomposed/ct-XXX.md"
     )
     ```
  2. **tasklist.md の該当タスクに `[x]` が付いていることを確認**
  3. **ユーザーが /create-commit スキルでコミット実行**
  4. 次の未完了CTへ（4-A-2-1に戻る）

- **NG の場合（1〜2回目）**:
  1. task-executor をモードCで呼び出し（修正）:
     ```
     Task(
       subagent_type: "task-executor",
       description: "CT-XXX fix",
       prompt: "quality-checker から指摘を受けました。
                修正を行い、再度レビュー待ち状態にしてください。
                CT: feature-docs/task-decomposed/ct-XXX.md
                quality-checkerの指摘: [指摘内容を含める]"
     )
     ```
  2. 修正完了後、再度 quality-checker にレビュー依頼（4-A-2-3に戻る）

- **NG の場合（3回目以降）**:
  1. task-executor をモードDで呼び出し（差し戻し処理）:
     ```
     Task(
       subagent_type: "task-executor",
       description: "CT-XXX escalation",
       prompt: "quality-checker から3回目のNG判定を受けました。
                差し戻し処理を行ってください。
                CT: feature-docs/task-decomposed/ct-XXX.md"
     )
     ```
  2. task-decomposer にCT再分解を依頼:
     ```
     Task(
       subagent_type: "task-decomposer",
       description: "CT-XXX re-decomposition",
       prompt: "CT-XXX が差し戻されました。
                差し戻し理由を確認し、CTを再分解してください。
                CT: feature-docs/task-decomposed/ct-XXX.md"
     )
     ```
  3. 再分解完了後、4-A-2-1に戻る

#### 例外処理ルール（モードA）

- **ルールA: スコープ拡大が必要な場合**
  - task-executor が実装を停止
  - CTファイルに問題を記録
  - task-decomposer にCT再分解を依頼

- **ルールB: 依存関係の問題が発生した場合**
  - 循環依存や未解決の依存を検出
  - task-decomposer に報告し、CT定義を見直し

- **禁止事項:**
  - quality-checker のレビューを経ずにコミットメッセージを提案
  - NG回数制限を超えて修正を続ける
  - スコープ外の変更を実装
  - **ユーザーの承認なしにコミットを実行** （必ずコミットメッセージ案を提案し、ユーザーに実行の可否を確認）

---

### モードB: 統合版（実験的）

**このモードはスキル内で統合ロジックを直接実行します。Agents を呼び出しません。**

#### 4-B-1. タスク分解（統合）

```
Read('.claude/skills/implement-feature/supporting-files/integrated-decomposer.md')
```

**手順:**
1. requirements.md, design.md, tasklist.md を読み込み
2. タスクを CT 単位に分解
3. feature-docs/task-decomposed/ct-*.md を生成
4. CTテンプレート: `.claude/skills/implement-feature/templates/ct-template.md`

**CT粒度の判断基準:**
- 変更ファイル数: 1〜5ファイル
- 実装手順: 3〜7項目
- 推定作業時間: 30分〜2時間
- 1コミット単位で完結

#### 4-B-2. CT実装ループ（統合）

```
Read('.claude/skills/implement-feature/supporting-files/integrated-executor.md')
```

**ループ開始:**

**4-B-2-1. 未完了CTの確認**
```
Glob('feature-docs/task-decomposed/ct-*.md')
```
- ステータスが「未着手」または「差し戻し」のCTを特定
- **全CTが「完了」の場合**: ループ終了、ステップ5へ

**4-B-2-2. CT実装**
1. CTファイルを読み込み、ステータスを「作業中」に更新
2. 設計資料・コンテキストを確認
3. CTに記載された内容を実装（Write, Edit, Bash ツールを使用）
4. 自己チェック（構文エラー、型エラーの確認）
5. ステータスを「レビュー待ち」に更新
6. 実装サマリーをCTファイルに追記

**4-B-2-3. 品質レビュー**
```
Read('.claude/skills/implement-feature/supporting-files/integrated-reviewer.md')
```

1. CTファイルと設計資料を読み込み
2. `git diff` で変更内容を確認
3. レビュー観点チェック:
   - CT適合性
   - 設計適合性
   - 品質（構文エラー、型エラー）
   - セキュリティ（APIキー漏洩など）
4. 品質チェックコマンド実行:
   ```
   Bash('ruff check .')
   Bash('pytest')  # 該当する場合
   ```
5. 判定（OK / NG）と根拠を記録

**4-B-2-4. レビュー結果に基づく分岐**

- **OK の場合**:
  1. CTステータスを「完了」に更新
  2. CTファイルに実装サマリーを記入
  3. **tasklist.md の該当タスクに `[x]` を付ける（CT完了時）**
  4. **ユーザーに /create-commit スキルでのコミット実行を依頼し、実行完了を待つ**
  5. **ユーザーのコミット実行を確認してから**次の未完了CTへ（4-B-2-1に戻る）

- **NG の場合（1〜2回目）**:
  1. CTファイルのNG回数を更新（+1）
  2. ステータスを「作業中」に変更
  3. 指摘された問題を修正
  4. ステータスを「レビュー待ち」に更新
  5. 再度レビュー（4-B-2-3に戻る）

- **NG の場合（3回目以降）**:
  1. CTステータスを「差し戻し」に変更
  2. 差し戻し理由をCTファイルに記録
  3. CT再分解処理（4-B-1に戻る）

#### スコープ制約の厳守（モードB）

以下の場合は実装を停止し、差し戻す:
- CTに記載されていない機能追加が必要
- 新しい依存パッケージの追加が必要
- データ構造の変更が必要
- 影響範囲がCTの想定を超えた

**差し戻し時の対応:**
1. 実装を停止
2. CTファイルに問題を記録
3. ステータスを「差し戻し」に更新
4. CT再分解処理へ

---

**このステップ（モードAまたはモードB）が完了したら、ただちにステップ5に進む。**

---

## ステップ5: 最終確認と振り返り

### 目的
全CTの完了を確認し、実装の振り返りを記録する。

### 手順

1. **全CTの完了確認**
   ```
   Glob('feature-docs/task-decomposed/ct-*.md')
   各CTファイルのステータスが「完了」になっていることを確認
   ```

2. **tasklist.md の全タスク完了を確認**
   ```
   Read('feature-docs/tasklist.md')
   全てのタスクが [x] になっていることを確認
   ```

   **注**: 各CTで既に品質チェック（lint, typecheck, test）は実行済み

3. **振り返りの記録**

   tasklist.md の「実装後の振り返り」セクションを更新:

   ```
   Edit('feature-docs/tasklist.md')

   記入内容:
   - 実装完了日: [YYYY-MM-DD]
   - 計画と実績の差分:
     * 計画時には想定していなかった技術的な変更点
     * 実装方針の変更とその理由
     * 新たに必要になったタスクとその理由
     * 技術的理由でスキップしたタスク（該当する場合）
   - 学んだこと:
     * 実装を通じて学んだ技術的な知見
     * 既存コードから学んだパターン
   - プロセス上の改善点:
     * タスク管理で良かった点
     * コンテキスト収集の有効性
   - 次回への改善提案:
     * 次回の機能追加で気をつけること
     * より効率的な実装方法
   ```

4. **完了報告**
   ```
   「機能実装が完了しました。

   ✅ 完了状態:
   - feature-docs/task-decomposed/: 全CT完了（各CTで品質チェック済み）
   - feature-docs/tasklist.md: 全タスク完了
   - 振り返り: 記録済み

   実装の振り返りは feature-docs/tasklist.md の最終セクションをご確認ください。」
   ```

---

## 完了条件

このワークフローは、以下の全ての条件を満たした時点で完了:

- ステップ2: requirements.md がユーザー承認済み
- ステップ3: design.md がユーザー承認済み
- ステップ4: 全CTが完了状態（各CTで lint, typecheck, test が成功済み）
- ステップ5: feature-docs/task-decomposed/ の全CTファイルがステータス「完了」
- ステップ5: tasklist.md の全タスクが完了状態（`[x]` または技術的理由でスキップ）
- ステップ5: tasklist.md に振り返りが記載されている
