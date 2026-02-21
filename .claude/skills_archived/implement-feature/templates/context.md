# コンテキスト分析

## 参照パス

--ref で指定されたファイル:
{参照ファイルのリストを列挙}

## idea.md からの要求理解

{idea.md が存在する場合、要求の要約を記述}
{存在しない場合は「idea.md なし」と記述}

---

## 参照コードから検出したパターン

### {ファイル名1} から

**ファイルの役割**:
- {このファイルが担う責務（例: RAG検索チェーン、エージェント定義、プロンプトテンプレート）}

**実装パターン**:
- {Chain/Runnable の構成（例: LLMChain, RetrievalQA, AgentExecutor）}
- {プロンプトテンプレートのスタイル（例: ChatPromptTemplate, PromptTemplate）}
- {LLM モデルの指定方法（例: ChatOpenAI, ChatAnthropic）}

**命名規則**:
- {変数/関数の命名（例: snake_case）}
- {ファイルの命名（例: snake_case.py）}
- {クラスの命名（例: PascalCase）}

**型定義**:
- {型ヒントの使用状況（例: TypedDict, Pydantic BaseModel）}
- {型定義のスタイル（例: 引数・戻り値の型ヒント）}

### {ファイル名2} から

**ファイルの役割**:
- {このファイルが担う責務}

**LLM API呼び出しパターン**:
- {LLM プロバイダー（例: OpenAI, Anthropic, Google Generative AI）}
- {エラーハンドリング（例: try-except, リトライロジック）}
- {ストリーミング処理（例: .stream(), astream()）}
- {トークン制限・コスト管理}

**ベクトルDB・Retriever パターン**:
- {ベクトルDB（例: Chroma, FAISS, Pinecone）}
- {Embedding モデル（例: OpenAIEmbeddings, HuggingFaceEmbeddings）}
- {検索戦略（例: similarity_search, MMR）}
- {チャンク分割（例: RecursiveCharacterTextSplitter）}

**メモリ管理パターン**:
- {メモリタイプ（例: ConversationBufferMemory, ConversationSummaryMemory）}
- {メモリの永続化（例: RedisChatMessageHistory, FileChatMessageHistory）}

---

## 検出したプロジェクト共通パターン

### ディレクトリ構造

```
{参照パスから推測されるディレクトリ構造}
例:
src/
├── chains/         # LangChainチェーン定義
├── agents/         # エージェント定義
├── prompts/        # プロンプトテンプレート
├── retrievers/     # Retriever・ベクトルDB設定
├── tools/          # カスタムツール定義
└── utils/          # ユーティリティ関数
```

### Chain 作成パターン

```python
# 検出したChainの典型的な構造
{コードサンプル}

# 例:
from langchain.chains import LLMChain
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate

llm = ChatOpenAI(model="gpt-4", temperature=0)
prompt = ChatPromptTemplate.from_messages([...])
chain = LLMChain(llm=llm, prompt=prompt)
```

### LLM API 呼び出しパターン

```python
# 検出した LLM API 呼び出しの典型的な構造
{コードサンプル}

# 例:
try:
    response = llm.invoke(messages)
except Exception as e:
    logger.error(f"LLM呼び出しエラー: {e}")
    # リトライ・フォールバックロジック
```

### RAG パターン

```python
# 検出した RAG の典型的な構造
{コードサンプル}

# 例:
from langchain_community.vectorstores import Chroma
from langchain.chains import RetrievalQA

vectorstore = Chroma(...)
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})
qa_chain = RetrievalQA.from_chain_type(llm=llm, retriever=retriever)
```

### テストパターン

{テストファイルが参照に含まれる場合}
```python
# 検出したテストの典型的な構造
{コードサンプル}

# 例:
import pytest
from unittest.mock import Mock

def test_chain_invoke():
    mock_llm = Mock()
    chain = create_chain(mock_llm)
    result = chain.invoke({"input": "test"})
    assert result is not None
```

{テストファイルが参照に含まれない場合}
- テストファイルは参照パスに含まれていません
- テスト作成時は既存のテストファイルを参照してください

---

## 従うべきパターン（実装時の指針）

### 必須
- {必ず従うべきパターン1（例: プロンプトは prompts/ ディレクトリに配置）}
- {必ず従うべきパターン2（例: LLM呼び出しは必ずエラーハンドリングを実装）}
- {必ず従うべきパターン3（例: API キーは環境変数から読み込み）}

### 推奨
- {従うことが望ましいパターン1（例: Chain は再利用可能な関数として定義）}
- {従うことが望ましいパターン2（例: プロンプトテンプレートは変数化）}

### 注意事項
- {既存コードで気になった点、避けるべきパターン（例: トークン制限を超える可能性）}

---

## 技術スタック（検出）

| カテゴリ | 技術 |
|---------|------|
| 言語 | Python 3.x |
| フレームワーク | LangChain / LangGraph |
| LLM プロバイダー | OpenAI / Anthropic / Google Generative AI |
| ベクトルDB | Chroma / FAISS / Pinecone |
| Embedding | OpenAIEmbeddings / HuggingFaceEmbeddings |
| テスト | pytest / unittest |
| パッケージ管理 | uv / pip |
| Linter/Formatter | ruff |
