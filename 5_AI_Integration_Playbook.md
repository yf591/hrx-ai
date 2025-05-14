
# 5. AI Integration Playbook（作成途中/"8.2"から）

## 目次

1. [AIシステム統合の概要](#1-aiシステム統合の概要)
2. [AIモデル選定とインテグレーションフレームワーク](#2-aiモデル選定とインテグレーションフレームワーク)
3. [RAGシステム実装ガイド](#3-ragシステム実装ガイド)
4. [プロンプトエンジニアリングパターン](#4-プロンプトエンジニアリングパターン)
5. [モデル評価と品質保証](#5-モデル評価と品質保証)
6. [AIコンポーネントのデプロイ戦略](#6-aiコンポーネントのデプロイ戦略)
7. [パフォーマンス最適化](#7-パフォーマンス最適化)
8. [AIシステムのモニタリングと観測可能性](#8-aiシステムのモニタリングと観測可能性)
9. [実装例とユースケース別ガイダンス](#9-実装例とユースケース別ガイダンス)
10. [コンプライアンスと倫理的考慮事項](#10-コンプライアンスと倫理的考慮事項)

## 1. AIシステム統合の概要

### 1.1 HRX-AIにおけるAIの役割

HRX-AIは、AIを中核に据えた次世代HR管理プラットフォームです。AIシステムは以下の主要な役割を担います。

- **情報分析と洞察生成**: 複雑な人事データからパターンを特定し、アクションにつながる洞察を提供
- **予測モデリング**: 離職リスク、採用成功確率、パフォーマンストレンドなどの予測
- **自然言語処理**: ドキュメント解析、センチメント分析、コミュニケーション支援
- **パーソナライゼーション**: ユーザー体験と推奨事項のカスタマイズ
- **意思決定支援**: データドリブンな戦略的決定のためのシナリオモデリングと推奨

### 1.2 AIアーキテクチャの概要

HRX-AIのAIシステムは、以下の主要コンポーネントで構成します。

```mermaid
graph TD
    A[アプリケーションレイヤー] --> B[AI オーケストレーションレイヤー]
    B --> C[基盤モデルレイヤー]
    B --> D[特化型モデルレイヤー]
    A --> E[ベクトルデータベース]
    E --> B
    B --> F[データ処理パイプライン]
    F --> G[データソース/シンク]
    
    subgraph "コンポーネント詳細"
        B --- B1[LangChain/LlamaIndex]
        C --- C1[GPT-4o/Claude3/Gemini]
        D --- D1[Hugging Face Transformers]
        D --- D2[カスタム予測モデル]
        E --- E1[テキスト埋め込み]
        E --- E2[セマンティック検索]
        F --- F1[抽出・変換・ロード]
        F --- F2[データエンリッチメント]
    end
```

### 1.3 トップレベル統合原則

HRX-AIでのAI統合には以下の原則に従います。

1. **モジュール性**: AIコンポーネントは疎結合に設計し、容易に交換・アップグレード可能にする
2. **スケーラビリティ**: 大規模組織データにも対応できるようにシステムを設計する
3. **適応性**: ユーザーフィードバックとパフォーマンスメトリクスに基づき継続的に改善する
4. **透明性**: AIの意思決定プロセスを説明可能にし、ユーザーの信頼を構築する
5. **効率性**: リソース使用を最適化し、コストとレイテンシーを最小化する
6. **倫理的設計**: プライバシー、公平性、説明責任を中心に据えた実装を行う

## 2. AIモデル選定とインテグレーションフレームワーク

### 2.1 モデル選定基準

適切なAIモデルの選定は、機能の効果とコスト効率に直接影響します。以下の基準に基づいてモデルを選定します。

#### 2.1.1 タスク別モデル選定マトリクス

| タスク           | 推奨モデル             | 代替モデル             | 選定理由                    |
| ------------- | ----------------- | ----------------- | ----------------------- |
| 一般的なテキスト生成・理解 | GPT-4o            | Claude 3.5 sonnet | コンテキスト理解力、一貫性、マルチモーダル能力 |
| 高速・軽量なレスポンス   | Gemini 2.0 Flash  | Claude 3.5 sonnet | 低レイテンシー、コスト効率           |
| 長文脈処理         | Claude 3.7 Sonnet | Gemini 2.5 Pro    | 100K+のトークン処理能力          |
| コード生成・分析      | Claude 3.7 Sonnet | Gemini 2.5 Pro    | コード品質、説明能力              |
| 特定領域のタスク      | 特化型オープンソースモデル     | ファインチューニングしたLLM   | 専門性、コスト効率               |

#### 2.1.2 モデル選択の考慮事項

- **性能指標**: 精度、再現率、F1スコアなど要件を満たす性能を備えているか
- **レイテンシー要件**: ユースケースに必要な応答時間を満たせるか
- **スケーラビリティ**: 予想されるトラフィック量とデータ量を処理できるか
- **コスト効率**: トークン単価、必要な計算リソースが予算内かどうか
- **機能的要件**: マルチモーダル、セマンティック検索、分類など必要な機能をサポートしているか
- **開発者支援**: API安定性、ドキュメント、ライブラリサポートの充実度

### 2.2 インテグレーションフレームワーク

HRX-AIでは、LangChain/LlamaIndexを中心としたインテグレーションフレームワークを採用し、以下の構造で実装します

#### 2.2.1 バックエンドAI統合アーキテクチャ

```python
# backend/app/ai/framework.py
from langchain.chat_models import ChatOpenAI, ChatAnthropic, ChatGoogleGenerativeAI
from langchain.embeddings import OpenAIEmbeddings, HuggingFaceEmbeddings
from langchain.chains import RetrievalQA, ConversationalRetrievalChain
from langchain.vectorstores import SupabaseVectorStore
from langchain.memory import ConversationBufferMemory

class AIModelFactory:
    @staticmethod
    def get_llm(model_name: str, **kwargs):
        """モデル名に基づいて適切なLLMインスタンスを返す"""
        if model_name.startswith("gpt-"):
            return ChatOpenAI(model=model_name, **kwargs)
        elif model_name.startswith("claude-"):
            return ChatAnthropic(model=model_name, **kwargs)
        elif model_name.startswith("gemini-"):
            return ChatGoogleGenerativeAI(model=model_name, **kwargs)
        else:
            raise ValueError(f"Unsupported model: {model_name}")
    
    @staticmethod
    def get_embeddings(embedding_type: str = "openai", **kwargs):
        """埋め込みモデルのインスタンスを返す"""
        if embedding_type == "openai":
            return OpenAIEmbeddings(**kwargs)
        elif embedding_type == "huggingface":
            return HuggingFaceEmbeddings(**kwargs)
        else:
            raise ValueError(f"Unsupported embedding type: {embedding_type}")
```

#### 2.2.2 モデル設定管理

```python
# backend/app/config/ai_models.py
from pydantic import BaseSettings

class AIModelsConfig(BaseSettings):
    """AIモデル設定管理クラス"""
    # デフォルトモデル設定
    default_llm_provider: str = "openai"
    default_llm_model: str = "gpt-4o"
    default_embedding_provider: str = "openai"
    default_embedding_model: str = "text-embedding-ada-002"
    
    # モデルごとのパラメータ設定
    model_parameters: dict = {
        "gpt-4o": {
            "temperature": 0.2,
            "top_p": 0.95,
            "max_tokens": 2000
        },
        "claude-3-opus": {
            "temperature": 0.2,
            "top_p": 0.95,
            "max_tokens": 4000
        },
        "gemini-1.5-pro": {
            "temperature": 0.2,
            "top_p": 0.95,
            "max_tokens": 2048
        }
    }
    
    # 特定タスク用モデルマッピング
    task_to_model_mapping: dict = {
        "general_chat": "gpt-4o",
        "long_context": "claude-3-opus",
        "low_latency": "gemini-1.5-flash",
        "code_generation": "gpt-4o",
        "document_analysis": "claude-3-sonnet"
    }
    
    class Config:
        env_prefix = "AI_"  # 環境変数から設定をオーバーライド可能
```

#### 2.2.3 APIインターフェース定義

```python
# backend/app/api/routes/ai.py
from fastapi import APIRouter, Depends, HTTPException
from app.ai.framework import AIModelFactory
from app.config.ai_models import AIModelsConfig
from app.schemas.ai import AIRequest, AIResponse
from app.services.ai import AIService

router = APIRouter(prefix="/api/v1/ai", tags=["AI"])
ai_config = AIModelsConfig()

@router.post("/chat", response_model=AIResponse)
async def chat_with_ai(request: AIRequest):
    """AIチャットエンドポイント - プロンプトに対するレスポンスを生成"""
    try:
        # タスクに応じたモデルを選択
        model_name = ai_config.task_to_model_mapping.get(
            request.task_type, ai_config.default_llm_model
        )
        
        # モデルパラメータの取得と上書き
        model_params = ai_config.model_parameters.get(model_name, {}).copy()
        if request.parameters:
            model_params.update(request.parameters)
        
        # モデルのインスタンス化
        llm = AIModelFactory.get_llm(model_name, **model_params)
        
        # AIサービスを使用してレスポンスを生成
        service = AIService(llm)
        response = await service.process_request(request)
        
        return response
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"AI処理エラー: {str(e)}")
```

### 2.3 モデルバージョニングとアップグレード戦略

#### 2.3.1 バージョニング規則

AI モデルのバージョニングには以下の命名規則を使用します

```
[モデル名]-[メジャー].[マイナー]-[プロバイダー]-[日付]
```

例
- `hrx-employee-risk-1.0-openai-20241001`: OpenAIを使用した従業員リスク予測モデル v1.0（2025年5月14日）
- `hrx-sentiment-2.1-anthropic-20241105`: Anthropicを使用したセンチメント分析モデル v2.1（2025年5月15日）

#### 2.3.2 アップグレードプロセス

1. **評価とベンチマーク**: 新モデルを標準データセットで評価し、既存モデルと比較
2. **シャドウデプロイ**: 実環境で並行して新モデルを動かし、結果を比較（プロダクションへの影響なし）
3. **カナリーテスト**: 限定ユーザーグループに新モデルを提供して実環境でのパフォーマンスを検証
4. **段階的ロールアウト**: 監視しながら徐々にトラフィックを新モデルに移行
5. **フォールバックメカニズム**: 問題発生時に容易に旧モデルに戻せる仕組みの確保

#### 2.3.3 モデル互換性の確保

```python
# backend/app/services/model_compatibility.py
class ModelCompatibilityService:
    """モデルバージョン間の互換性を管理するサービス"""
    
    def __init__(self, model_registry):
        self.model_registry = model_registry
    
    def transform_output(self, model_version, output):
        """モデルの出力を標準フォーマットに変換"""
        transformer = self.get_transformer(model_version)
        return transformer.transform(output)
    
    def get_transformer(self, model_version):
        """バージョンに対応する変換器を取得"""
        if model_version.startswith("hrx-employee-risk-1"):
            return EmployeeRiskV1Transformer()
        elif model_version.startswith("hrx-employee-risk-2"):
            return EmployeeRiskV2Transformer()
        # その他のモデル変換器...
        else:
            return DefaultTransformer()
```

## 3. RAGシステム実装ガイド

### 3.1 RAGアーキテクチャ概要

検索拡張生成（Retrieval-Augmented Generation: RAG）システムは、HRX-AIの中核的なAI機能です。このシステムにより、「社内知識に基づいた正確な回答」「最新の人事情報を反映した提案」「組織特有のコンテキストを考慮したアドバイス」などが可能になります。

#### 3.1.1 HRX-AI RAGシステムの基本フロー

```mermaid
sequenceDiagram
    participant U as ユーザー
    participant FE as フロントエンド
    participant BE as バックエンドAPI
    participant RE as 検索エンジン
    participant DB as ベクトルDB
    participant LLM as 大規模言語モデル
    
    U->>FE: 質問/リクエスト
    FE->>BE: APIリクエスト
    BE->>BE: クエリ解析/拡張
    BE->>RE: 関連情報検索
    RE->>DB: ベクトル類似度検索
    DB-->>RE: 関連ドキュメント
    RE-->>BE: 検索結果
    BE->>BE: コンテキスト構築
    BE->>LLM: プロンプト+コンテキスト
    LLM-->>BE: 生成された回答
    BE->>BE: 後処理/フォーマット
    BE-->>FE: 構造化レスポンス
    FE-->>U: UI表示
```

### 3.2 ドキュメント処理パイプライン

効果的なRAGシステムを構築するための最初のステップは、データの前処理と索引付けです。

#### 3.2.1 文書取り込みパイプライン

```python
# backend/app/services/document_ingestion.py
import uuid
from langchain.document_loaders import PyPDFLoader, CSVLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import SupabaseVectorStore
from app.config.ai_models import AIModelsConfig

class DocumentIngestionPipeline:
    """文書取り込みパイプライン"""
    
    def __init__(self, db_connection, ai_config=None):
        self.db_connection = db_connection
        self.ai_config = ai_config or AIModelsConfig()
        self.embeddings = self._get_embeddings()
        self.text_splitter = RecursiveCharacterTextSplitter(
            chunk_size=1000, 
            chunk_overlap=100
        )
    
    def _get_embeddings(self):
        """埋め込みモデルのインスタンスを取得"""
        embedding_provider = self.ai_config.default_embedding_provider
        embedding_model = self.ai_config.default_embedding_model
        
        if embedding_provider == "openai":
            return OpenAIEmbeddings(model=embedding_model)
        # 他のプロバイダーのサポート...
        
    async def process_document(self, file_path, metadata=None):
        """ドキュメントを処理してベクトルDBに保存"""
        # ファイル拡張子に基づいて適切なローダーを選択
        if file_path.endswith(".pdf"):
            loader = PyPDFLoader(file_path)
        elif file_path.endswith(".csv"):
            loader = CSVLoader(file_path)
        else:
            raise ValueError(f"Unsupported file type: {file_path}")
        
        # ドキュメントの読み込み
        documents = loader.load()
        
        # メタデータの追加
        if metadata:
            for doc in documents:
                doc.metadata.update(metadata)
                # 追跡用の一意のIDを割り当て
                doc.metadata["doc_id"] = str(uuid.uuid4())
        
        # テキスト分割
        chunks = self.text_splitter.split_documents(documents)
        
        # ベクトルDBへの保存
        vector_store = SupabaseVectorStore.from_documents(
            chunks,
            self.embeddings,
            client=self.db_connection,
            table_name="document_embeddings"
        )
        
        return {
            "chunks_count": len(chunks),
            "doc_ids": [doc.metadata.get("doc_id") for doc in documents]
        }
```

#### 3.2.2 定期的なインデックス更新戦略

```python
# backend/app/services/index_refresher.py
from datetime import datetime
import asyncio

class IndexRefresher:
    """ベクトルインデックスの定期更新サービス"""
    
    def __init__(self, ingestion_pipeline, source_connector, refresh_interval_hours=24):
        self.ingestion_pipeline = ingestion_pipeline
        self.source_connector = source_connector
        self.refresh_interval = refresh_interval_hours * 3600  # 秒に変換
    
    async def start_refresh_loop(self):
        """定期的なインデックス更新ループを開始"""
        while True:
            try:
                await self.refresh_index()
                await asyncio.sleep(self.refresh_interval)
            except Exception as e:
                print(f"Index refresh error: {str(e)}")
                # エラー発生時は短い間隔で再試行
                await asyncio.sleep(300)
    
    async def refresh_index(self):
        """インデックスを更新"""
        # 最終更新以降に変更されたドキュメントを取得
        changed_docs = await self.source_connector.get_changed_documents()
        
        for doc in changed_docs:
            if doc["status"] == "deleted":
                # ドキュメントが削除された場合、インデックスからも削除
                await self.delete_from_index(doc["id"])
            else:
                # 新規または更新されたドキュメント
                await self.ingestion_pipeline.process_document(
                    doc["path"],
                    metadata={
                        "source": doc["source"],
                        "last_updated": datetime.now().isoformat(),
                        "version": doc["version"]
                    }
                )
        
        return {
            "processed_docs": len(changed_docs),
            "timestamp": datetime.now().isoformat()
        }
    
    async def delete_from_index(self, doc_id):
        """インデックスから特定のドキュメントを削除"""
        # 実装はベクトルDBの具体的なAPIに依存
        pass
```

### 3.3 検索コンポーネント実装

効率的で正確な情報検索は、RAGシステムの成功に不可欠です。

#### 3.3.1 高度なクエリ処理

```python
# backend/app/services/query_processing.py
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

class QueryProcessor:
    """クエリ処理とコンテキスト取得サービス"""
    
    def __init__(self, vector_store, llm):
        self.vector_store = vector_store
        self.llm = llm
        self.base_retriever = vector_store.as_retriever(search_kwargs={"k": 5})
        
        # LLMを使用したコンテキスト圧縮（無関係な情報の除去）
        self.compressor = LLMChainExtractor.from_llm(llm)
        self.compression_retriever = ContextualCompressionRetriever(
            base_compressor=self.compressor,
            base_retriever=self.base_retriever
        )
    
    async def process_query(self, query_text, filters=None):
        """クエリを処理し、関連ドキュメントを取得"""
        # クエリ拡張（オプション）
        expanded_query = await self._expand_query(query_text)
        
        # 検索フィルターの適用
        search_kwargs = {"k": 5}  # デフォルト値
        if filters:
            search_kwargs["filter"] = filters
        
        # ハイブリッド検索（キーワードとセマンティック）
        keyword_results = await self._keyword_search(query_text, search_kwargs)
        semantic_results = await self._semantic_search(expanded_query, search_kwargs)
        
        # 結果のマージとランク付け
        merged_results = self._merge_search_results(keyword_results, semantic_results)
        
        # コンテキストプルーニング（関連性の低い情報を削除）
        pruned_results = await self._prune_context(query_text, merged_results)
        
        return {
            "original_query": query_text,
            "expanded_query": expanded_query,
            "contexts": pruned_results
        }
    
    async def _expand_query(self, query_text):
        """クエリ拡張：検索効果を高めるためにクエリを拡張する"""
        # 簡単な実装例
        return query_text
        
        # LLMを使用した高度なクエリ拡張の例
        # prompt = f"元のクエリ：{query_text}\n検索効率を向上させるために、このクエリを拡張してください。"
        # response = await self.llm.agenerate([prompt])
        # return response.generations[0][0].text.strip()
    
    async def _keyword_search(self, query, kwargs):
        """キーワードベースの検索（完全一致や部分一致）"""
        # 実装はベクトルDBの具体的なAPIに依存
        pass
    
    async def _semantic_search(self, query, kwargs):
        """セマンティック検索（意味的類似性）"""
        return await self.compression_retriever.aget_relevant_documents(query)
    
    def _merge_search_results(self, keyword_results, semantic_results):
        """検索結果のマージと重複除去"""
        # シンプルな実装例
        unique_docs = {}
        
        # キーワード結果はより高い優先度で追加
        for doc in keyword_results:
            unique_docs[doc.metadata["doc_id"]] = doc
        
        # セマンティック結果を追加（重複する場合は上書きしない）
        for doc in semantic_results:
            if doc.metadata["doc_id"] not in unique_docs:
                unique_docs[doc.metadata["doc_id"]] = doc
        
        return list(unique_docs.values())
    
    async def _prune_context(self, query, documents):
        """クエリに対して関連性の低い情報を削除"""
        # 基本的な実装では圧縮リトリーバーの結果をそのまま使用
        return documents
```

#### 3.3.2 メタデータフィルタリング

```python
# backend/app/schemas/search_filters.py
from pydantic import BaseModel
from typing import Dict, List, Optional, Any, Union
from datetime import datetime

class DateRange(BaseModel):
    from_date: datetime
    to_date: datetime

class MetadataFilter(BaseModel):
    field: str
    operator: str  # "eq", "neq", "gt", "lt", "in", "contains"
    value: Any

class SearchFilters(BaseModel):
    """検索フィルターのスキーマ"""
    document_types: Optional[List[str]] = None  # "policy", "employee", "performance", etc.
    departments: Optional[List[str]] = None
    date_range: Optional[DateRange] = None
    created_by: Optional[str] = None
    tags: Optional[List[str]] = None
    custom_filters: Optional[List[MetadataFilter]] = None
    
    def to_vector_db_filter(self) -> Dict:
        """ベクトルDBのフィルター形式に変換"""
        filters = {}
        
        if self.document_types:
            filters["document_type"] = {"$in": self.document_types}
            
        if self.departments:
            filters["department"] = {"$in": self.departments}
            
        if self.date_range:
            filters["created_at"] = {
                "$gte": self.date_range.from_date.isoformat(),
                "$lte": self.date_range.to_date.isoformat()
            }
            
        if self.created_by:
            filters["created_by"] = self.created_by
            
        if self.tags:
            filters["tags"] = {"$containsAny": self.tags}
        
        # カスタムフィルターの処理
        if self.custom_filters:
            for cf in self.custom_filters:
                if cf.operator == "eq":
                    filters[cf.field] = cf.value
                elif cf.operator == "neq":
                    filters[cf.field] = {"$ne": cf.value}
                elif cf.operator == "gt":
                    filters[cf.field] = {"$gt": cf.value}
                elif cf.operator == "lt":
                    filters[cf.field] = {"$lt": cf.value}
                elif cf.operator == "in":
                    filters[cf.field] = {"$in": cf.value}
                elif cf.operator == "contains":
                    filters[cf.field] = {"$contains": cf.value}
        
        return filters
```

### 3.4 コンテキスト最適化とプロンプト生成

検索結果の質を高めるための最適化技術と効果的なプロンプト生成を実装します。

#### 3.4.1 コンテキスト選択と順序付け

```python
# backend/app/services/context_optimizer.py
class ContextOptimizer:
    """検索結果のコンテキストを最適化するサービス"""
    
    def __init__(self, llm):
        self.llm = llm
    
    async def optimize_context(self, query, retrieved_documents, max_tokens=3800):
        """クエリに最適なコンテキストを選択し順序付け"""
        # 関連性によるドキュメントのランク付け
        ranked_docs = await self._rank_documents(query, retrieved_documents)
        
        # トークン数を考慮したコンテキスト選択
        selected_docs = self._select_within_token_limit(ranked_docs, max_tokens)
        
        # コンテキストの統合と順序付け
        organized_context = self._organize_context(selected_docs)
        
        return organized_context
    
    async def _rank_documents(self, query, documents):
        """LLMを使用してドキュメントの関連性をランク付け"""
        # 各ドキュメントの関連性スコアを計算
        scored_docs = []
        for doc in documents:
            relevance_score = await self._calculate_relevance(query, doc.page_content)
            scored_docs.append((doc, relevance_score))
        
        # スコアでソート（降順）
        scored_docs.sort(key=lambda x: x[1], reverse=True)
        return [doc for doc, _ in scored_docs]
    
    async def _calculate_relevance(self, query, content):
        """クエリとコンテンツの関連性スコアを計算"""
        # 簡易実装：埋め込みの類似度計算などの方法もあり
        prompt = f"""
        クエリ: {query}
        
        コンテンツ: {content}
        
        上記のクエリに対するコンテンツの関連性を0から10の数値で評価してください。
        関連性スコア（0-10）:
        """
        
        response = await self.llm.agenerate([prompt])
        # レスポンスから数値を抽出
        try:
            score_text = response.generations[0][0].text.strip()
            score = float(score_text)
            return min(max(score, 0), 10)  # 0-10の範囲に制限
        except:
            return 5  # 解析エラーの場合のデフォルト値
    
    def _select_within_token_limit(self, documents, max_tokens):
        """トークン制限内に収まるようにドキュメントを選択"""
        selected = []
        current_tokens = 0
        
        for doc in documents:
            # 簡易的なトークン数推定（実際にはトークナイザーを使用）
            doc_tokens = len(doc.page_content.split()) * 1.3  # 単語数 * 平均トークン/単語比率
            
            if current_tokens + doc_tokens <= max_tokens:
                selected.append(doc)
                current_tokens += doc_tokens
            else:
                break
        
        return selected
    
    def _organize_context(self, documents):
        """選択されたコンテキストを論理的に順序付けて構造化"""
        # ドキュメントタイプによるグループ化の例
        grouped_docs = {}
        
        for doc in documents:
            doc_type = doc.metadata.get("document_type", "unknown")
            if doc_type not in grouped_docs:
                grouped_docs[doc_type] = []
            grouped_docs[doc_type].append(doc)
        
        # グループ化されたドキュメントを特定の順序で結合
        # 例：方針 -> 手順 -> 具体例 -> 統計 の順
        priority_order = ["policy", "procedure", "example", "statistics", "unknown"]
        
        organized_docs = []
        for doc_type in priority_order:
            if doc_type in grouped_docs:
                organized_docs.extend(grouped_docs[doc_type])
        
        return organized_docs
```

#### 3.4.2 動的プロンプトテンプレート生成

```python
# backend/app/services/prompt_factory.py
from langchain.prompts import ChatPromptTemplate, SystemMessagePromptTemplate, HumanMessagePromptTemplate

class PromptFactory:
    """動的プロンプト生成ファクトリー"""
    
    def __init__(self):
        self.system_templates = {
            "general": "あなたは人事部門向けのAIアシスタントです。正確で偏りのない回答を提供してください。",
            "recruitment": "あなたは採用プロセスを専門とするHRアシスタントです。採用、面接、候補者評価に関する質問に回答してください。",
            "performance": "あなたは従業員のパフォーマンス管理を専門とするHRアシスタントです。評価プロセス、フィードバック、能力開発に関する質問に回答してください。",
            "compliance": "あなたは人事コンプライアンスを専門とするHRアシスタントです。労働法、規制、ポリシーに関する質問に回答してください。"
        }
        
        self.context_format_templates = {
            "default": "以下は関連情報です：\n\n{context}",
            "qa": "以下のドキュメントにある情報を使用して質問に答えてください：\n\n{context}",
            "analysis": "以下のデータを分析し、インサイトを提供してください：\n\n{context}"
        }
    
    def create_rag_prompt(self, query, contexts, domain="general", format_type="default"):
        """RAG用のプロンプトを生成"""
        # システムプロンプト選択
        system_template = self.system_templates.get(domain, self.system_templates["general"])
        
        # コンテキスト形式選択
        context_format = self.context_format_templates.get(format_type, self.context_format_templates["default"])
        
        # コンテキストのフォーマット
        formatted_context = self._format_context_documents(contexts)
        context_section = context_format.format(context=formatted_context)
        
        # プロンプトの組み立て
        system_message = SystemMessagePromptTemplate.from_template(system_template)
        
        human_template = f"{context_section}\n\n質問/リクエスト: {{query}}"
        human_message = HumanMessagePromptTemplate.from_template(human_template)
        
        chat_prompt = ChatPromptTemplate.from_messages([system_message, human_message])
        
        return chat_prompt.format(query=query)
    
    def _format_context_documents(self, documents):
        """ドキュメントをテキスト形式にフォーマット"""
        formatted_docs = []
        
        for i, doc in enumerate(documents):
            # メタデータから有用な情報を抽出
            source = doc.metadata.get("source", "不明な情報源")
            date = doc.metadata.get("date", "日付不明")
            
            # フォーマット済みのドキュメント
            formatted_doc = f"[ドキュメント {i+1}] {source} ({date})\n{doc.page_content}\n"
            formatted_docs.append(formatted_doc)
        
        return "\n".join(formatted_docs)
```

### 3.5 RAG統合サービス

上記のコンポーネントを統合してエンドツーエンドのRAGサービスを実装します。

```python
# backend/app/services/rag_service.py
from app.services.query_processing import QueryProcessor
from app.services.context_optimizer import ContextOptimizer
from app.services.prompt_factory import PromptFactory
from app.config.ai_models import AIModelsConfig
from app.ai.framework import AIModelFactory

class RAGService:
    """検索拡張生成（RAG）サービス"""
    
    def __init__(
        self,
        vector_store,
        ai_config=None,
        query_processor=None,
        context_optimizer=None,
        prompt_factory=None
    ):
        self.ai_config = ai_config or AIModelsConfig()
        
        # デフォルトモデルを使用してLLMを初期化
        self.llm = AIModelFactory.get_llm(
            self.ai_config.default_llm_model,
            **self.ai_config.model_parameters.get(self.ai_config.default_llm_model, {})
        )
        
        # 依存サービスの初期化
        self.query_processor = query_processor or QueryProcessor(vector_store, self.llm)
        self.context_optimizer = context_optimizer or ContextOptimizer(self.llm)
        self.prompt_factory = prompt_factory or PromptFactory()
    
    async def process_rag_query(self, query_text, domain="general", filters=None, max_tokens=3800):
        """RAGクエリを処理し、LLMレスポンスを生成"""
        # 関連ドキュメントの検索
        search_result = await self.query_processor.process_query(query_text, filters)
        
        # コンテキストの最適化
        optimized_contexts = await self.context_optimizer.optimize_context(
            query_text,
            search_result["contexts"],
            max_tokens
        )
        
        # プロンプト生成
        rag_prompt = self.prompt_factory.create_rag_prompt(
            query_text,
            optimized_contexts,
            domain
        )
        
        # LLMでの回答生成
        response = await self.llm.agenerate([rag_prompt])
        generated_text = response.generations[0][0].text.strip()
        
        # レスポンス構築
        result = {
            "query": query_text,
            "response": generated_text,
            "contexts": [
                {
                    "content": doc.page_content,
                    "metadata": doc.metadata
                }
                for doc in optimized_contexts
            ],
            "model_used": self.ai_config.default_llm_model,
        }
        
        return result
```

### 3.6 クライアント側の実装例

```typescript
// frontend/src/lib/api/rag.ts
import axios from 'axios';

interface RAGQueryParams {
  query: string;
  domain?: string;
  filters?: {
    documentTypes?: string[];
    departments?: string[];
    dateRange?: {
      fromDate: string;
      toDate: string;
    };
    tags?: string[];
    [key: string]: any;
  };
}

interface RAGResponse {
  query: string;
  response: string;
  contexts: Array<{
    content: string;
    metadata: Record<string, any>;
  }>;
  model_used: string;
}

export async function performRAGQuery(params: RAGQueryParams): Promise<RAGResponse> {
  try {
    const response = await axios.post('/api/v1/ai/rag', params);
    return response.data;
  } catch (error) {
    console.error('RAG query error:', error);
    throw new Error('Failed to get AI response');
  }
}
```

```tsx
// frontend/src/components/ai/RAGQueryInterface.tsx
import React, { useState } from 'react';
import { performRAGQuery } from '@/lib/api/rag';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from '@/components/ui/card';
import { Spinner } from '@/components/ui/spinner';

export function RAGQueryInterface() {
  const [query, setQuery] = useState('');
  const [response, setResponse] = useState<any>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    
    if (!query.trim()) return;
    
    setLoading(true);
    setError(null);
    
    try {
      const result = await performRAGQuery({ 
        query,
        domain: 'general',
        // 必要に応じてフィルターを追加
      });
      
      setResponse(result);
    } catch (err) {
      setError('AIレスポンスの取得に失敗しました。後でもう一度お試しください。');
      console.error(err);
    } finally {
      setLoading(false);
    }
  };
  
  return (
    <div className="space-y-4">
      <form onSubmit={handleSubmit} className="flex gap-2">
        <Input
          value={query}
          onChange={(e) => setQuery(e.target.value)}
          placeholder="質問を入力してください..."
          className="flex-1"
        />
        <Button type="submit" disabled={loading || !query.trim()}>
          {loading ? <Spinner size="sm" /> : '送信'}
        </Button>
      </form>
      
      {error && (
        <div className="p-3 bg-red-50 border border-red-200 text-red-800 rounded-md">
          {error}
        </div>
      )}
      
      {response && (
        <Card>
          <CardHeader>
            <CardTitle>回答</CardTitle>
          </CardHeader>
          <CardContent>
            <div className="prose max-w-none">
              {response.response.split('\n').map((line: string, i: number) => (
                <p key={i}>{line}</p>
              ))}
            </div>
          </CardContent>
          <CardFooter className="flex flex-col items-start gap-2">
            <details className="text-sm text-gray-500 w-full">
              <summary className="cursor-pointer">情報ソース ({response.contexts.length})</summary>
              <div className="mt-2 space-y-2 text-xs">
                {response.contexts.map((context: any, i: number) => (
                  <div key={i} className="p-2 bg-gray-50 rounded-md">
                    <div className="font-semibold text-gray-700">
                      ソース: {context.metadata.source || '不明'}
                      {context.metadata.date && ` (${context.metadata.date})`}
                    </div>
                    <div className="mt-1">{context.content}</div>
                  </div>
                ))}
              </div>
            </details>
            <div className="text-xs text-gray-500">
              モデル: {response.model_used}
            </div>
          </CardFooter>
        </Card>
      )}
    </div>
  );
}
```

## 4. プロンプトエンジニアリングパターン

### 4.1 HR固有のプロンプトテンプレート

HRX-AIの主要タスクに対応するプロンプトのテンプレートです。

#### 4.1.1 プロンプトライブラリ構造

```typescript
// frontend/src/lib/prompts/prompt-library.ts
interface PromptTemplate {
  id: string;
  name: string;
  category: string;
  description: string;
  template: string;
  parameters: string[];
  suggestedValues?: Record<string, string[]>;
  exampleOutput?: string;
}

// HRX-AI用プロンプトライブラリ
export const promptLibrary: PromptTemplate[] = [
  // 従業員分析プロンプト
  {
    id: 'employee-risk-analysis',
    name: '従業員リスク分析',
    category: 'employee-retention',
    description: '従業員の離職リスク要因を分析し、リスク軽減のための推奨アクションを生成します',
    template: `
# 従業員離職リスク分析

## 従業員情報
- 名前: {{employee_name}}
- 役職: {{job_title}}
- 部門: {{department}}
- 勤続年数: {{tenure}} 年
- 最終評価スコア: {{performance_score}}/10
- 直近6ヶ月の欠勤日数: {{absence_days}}日
- 直近の昇給から経過期間: {{months_since_promotion}}ヶ月
- 直属マネージャー評価: {{manager_rating}}/10
- エンゲージメントサーベイスコア: {{engagement_score}}/10

## タスク
1. 上記データに基づき、離職リスク要因を特定し、各要因の重要度を評価してください。
2. 離職リスク全体のスコアを1-10の数値で評価してください。
3. リスクレベルに応じた具体的な介入策と保持戦略を3-5個提案してください。
4. 提案内容は実用的で直ちに実行可能なものにしてください。

回答は次の形式で提供してください：

### リスク分析
[リスク要因とその分析]

### 総合リスクスコア
[1-10の数値、およびその理由]

### 推奨アクション
1. [具体的なアクション1]
2. [具体的なアクション2]
3. [具体的なアクション3]
...

### 期待される成果
[推奨アクション実施による期待効果]
    `,
    parameters: [
      'employee_name', 'job_title', 'department', 'tenure', 
      'performance_score', 'absence_days', 'months_since_promotion',
      'manager_rating', 'engagement_score'
    ],
    suggestedValues: {
      'department': ['営業', 'マーケティング', 'エンジニアリング', '人事', '財務']
    },
    exampleOutput: `
### リスク分析
主なリスク要因:
1. エンゲージメントスコアが低い (6/10) - 高リスク要因
2. 直属マネージャー評価が中程度 (7/10) - 中リスク要因
3. 最終昇給から長期間経過 (18ヶ月) - 高リスク要因
4. パフォーマンススコアは良好 (8/10) - リスク軽減要因

### 総合リスクスコア
7/10 - 高リスク。エンゲージメントの低さと長期間の昇給がないことが主な原因です。

### 推奨アクション
1. 1週間以内にキャリア開発面談をスケジュールし、成長機会と将来のキャリアパスについて話し合う
2. スキル向上のための研修プログラムへの参加機会を提供し、新たな責任領域を検討する
3. 報酬見直しのための評価を即時実施し、市場価値に応じた調整を検討する
4. プロジェクト達成に対する非金銭的認識プログラムを実施する
5. ワークライフバランスの改善策を提案し、柔軟な勤務形態の選択肢を提供する

### 期待される成果
上記アクションにより、エンゲージメントスコアが1-2ポイント向上し、離職リスクが3-4ポイント低下することが期待されます。特に、キャリア開発と報酬の見直しは短期的な成果をもたらし、スキル開発は長期的なエンゲージメント向上に貢献するでしょう。
    `
  },
  
  // 採用文書生成プロンプト
  {
    id: 'job-description-generator',
    name: '求人票生成',
    category: 'recruitment',
    description: '魅力的で包括的な求人票を生成します',
    template: `
# 求人票生成

## 職位情報
- 役職: {{job_title}}
- 部門: {{department}}
- 雇用形態: {{employment_type}}
- 勤務地: {{location}}
- 年収レンジ: {{salary_range}}

## 会社情報
- 会社名: {{company_name}}
- 業界: {{industry}}
- 会社規模: {{company_size}}
- 企業文化: {{culture_keywords}}

## 要求スキルと経験
{{required_skills}}

## 追加情報
- 主な責任: {{key_responsibilities}}
- 理想的な候補者の特徴: {{ideal_candidate}}
- 福利厚生と特典: {{benefits}}

## タスク
1. 上記情報に基づき、多様性を重視し、包括的な言葉遣いで求人票を作成してください。
2. 会社文化と価値観を反映した内容にしてください。
3. 応募を促す魅力的な説明を含めてください。
4. 以下のセクションを含めてください:
   - 会社紹介
   - 職位概要
   - 主な責任
   - 必須条件
   - 歓迎条件
   - 福利厚生
   - 応募方法

求人票は、モチベーションが高く、適格な候補者を惹きつけるように作成してください。
    `,
    parameters: [
      'job_title', 'department', 'employment_type', 'location', 'salary_range',
      'company_name', 'industry', 'company_size', 'culture_keywords',
      'required_skills', 'key_responsibilities', 'ideal_candidate', 'benefits'
    ],
    suggestedValues: {
      'employment_type': ['正社員', '契約社員', 'パートタイム', 'リモート'],
      'culture_keywords': ['革新的', '協調的', '自律性重視', '社会的インパクト志向', 'ワークライフバランス']
    }
  },
  
  // パフォーマンスレビュープロンプト
  {
    id: 'performance-review-generator',
    name: 'パフォーマンスレビュー作成',
    category: 'performance-management',
    description: '従業員のパフォーマンスレビューのドラフトを作成します',
    template: `
# パフォーマンスレビュードラフト作成

## 従業員情報
- 名前: {{employee_name}}
- 役職: {{job_title}}
- 部門: {{department}}
- レビュー期間: {{review_period}}
- 前回のレビュースコア: {{previous_score}}/5

## パフォーマンスデータ
- 主要業績指標 (KPI) 達成度: {{kpi_achievement}}
- 完了したプロジェクト: {{completed_projects}}
- 主な成果: {{key_achievements}}
- 改善が必要な分野: {{areas_for_improvement}}
- チームへの貢献: {{team_contribution}}

## マネージャーのコメント
{{manager_notes}}

## タスク
1. 上記情報に基づき、公正でバランスの取れたパフォーマンスレビューのドラフトを作成してください。
2. 具体的な例と数値データを用いて成果を客観的に評価してください。
3. 改善が必要な分野については、建設的なフィードバックと具体的な改善策を提案してください。
4. 次のレビュー期間に向けた明確な目標を3-5項目提案してください。
5. 全体評価スコア（1-5）を推奨し、その根拠を説明してください。

レビューは以下のセクションで構成してください：
- パフォーマンス概要
- 主要な成果と強み
- 改善領域と成長機会
- 次期の目標と期待
- 全体評価と推奨アクション
    `,
    parameters: [
      'employee_name', 'job_title', 'department', 'review_period',
      'previous_score', 'kpi_achievement', 'completed_projects', 
      'key_achievements', 'areas_for_improvement', 'team_contribution',
      'manager_notes'
    ]
  },
  
  // その他のテンプレート...
];
```

#### 4.1.2 プロンプトテンプレートエンジン

```typescript
// frontend/src/lib/prompts/prompt-engine.ts
import { promptLibrary } from './prompt-library';

/**
 * プロンプトテンプレート内のパラメータを実際の値で置換する
 */
export function fillPromptTemplate(templateId: string, parameters: Record<string, string>): string {
  const template = promptLibrary.find(p => p.id === templateId);
  if (!template) {
    throw new Error(`Template with ID ${templateId} not found`);
  }
  
  let filledTemplate = template.template;
  
  // テンプレート内のパラメータを置換
  for (const [key, value] of Object.entries(parameters)) {
    const regex = new RegExp(`{{\\s*${key}\\s*}}`, 'g');
    filledTemplate = filledTemplate.replace(regex, value);
  }
  
  // 未入力のパラメータをチェック
  const remainingParams = template.parameters.filter(param => {
    const regex = new RegExp(`{{\\s*${param}\\s*}}`, 'g');
    return regex.test(filledTemplate);
  });
  
  if (remainingParams.length > 0) {
    console.warn(`Warning: Some parameters are not filled: ${remainingParams.join(', ')}`);
  }
  
  return filledTemplate;
}

/**
 * テンプレートIDに基づいてプロンプトを取得
 */
export function getPromptTemplate(id: string): typeof promptLibrary[0] | undefined {
  return promptLibrary.find(p => p.id === id);
}

/**
 * カテゴリに基づいてプロンプトテンプレートをフィルタリング
 */
export function getPromptsByCategory(category: string): typeof promptLibrary {
  return promptLibrary.filter(p => p.category === category);
}

/**
 * テキストからプロンプトテンプレートを検索
 */
export function searchPromptTemplates(query: string): typeof promptLibrary {
  const lowerQuery = query.toLowerCase();
  return promptLibrary.filter(p => 
    p.name.toLowerCase().includes(lowerQuery) ||
    p.description.toLowerCase().includes(lowerQuery) ||
    p.category.toLowerCase().includes(lowerQuery)
  );
}
```

### 4.2 成果を上げるプロンプト設計パターン

以下はHRX-AIの具体的なユースケースに対応した効果的だと思われるプロンプトパターンの例です。

#### 4.2.1 チェーンオブソート (CoT) プロンプトの実装

```Python
# backend/app/services/prompt_patterns.py
class ChainOfThoughtPrompt:
    """段階的思考プロセスを促すプロンプトパターン"""
    
    @staticmethod
    def create_prompt(task_description, inputs, steps=None):
        """
        Chain-of-Thought プロンプトを生成
        
        Args:
            task_description: タスクの説明
            inputs: 入力データや情報
            steps: 思考プロセスのステップ (Optional)
        
        Returns:
            CoTプロンプト文字列
        """
        if steps is None:
            steps = [
                "関連する事実と情報を整理する",
                "それらの情報から何がわかるか分析する",
                "可能性のある結論や選択肢を検討する",
                "最終的な結論または推奨事項を提示する"
            ]
        
        # 基本プロンプト構築
        prompt = f"""# {task_description}

## 入力情報
{inputs}

## 指示
以下のステップに従って段階的に考えてください：

"""

        # 思考ステップの追加
        for i, step in enumerate(steps):
            prompt += f"{i+1}. {step}\n"
        
        prompt += "\n各ステップでの思考を明示してください。"
        
        return prompt

# 使用例：従業員のパフォーマンス問題分析
def analyze_performance_issue(employee_data, performance_metrics, incidents):
    """従業員のパフォーマンス問題をCoTアプローチで分析"""
    
    inputs = f"""
従業員情報:
{employee_data}

パフォーマンス指標:
{performance_metrics}

問題事象:
{incidents}
"""
    
    steps = [
        "従業員の経歴、スキル、前回のレビューなど基本情報を確認する",
        "パフォーマンス指標の時系列変化を分析し、傾向を特定する",
        "報告されている問題事象のパターンと根本原因を考察する",
        "パフォーマンス低下の可能性のある要因をリストアップする",
        "それぞれの要因の可能性と影響度を評価する",
        "最も可能性の高い要因と改善のための具体的な提案を提示する"
    ]
    
    return ChainOfThoughtPrompt.create_prompt(
        "従業員パフォーマンス問題分析",
        inputs,
        steps
    )
```

#### 4.2.2 ペルソナベースのプロンプト設計

```python
# backend/app/services/prompt_patterns.py
class PersonaBasedPrompt:
    """ペルソナに基づいたプロンプトパターン"""
    
    # 事前定義されたペルソナ
    PERSONAS = {
        "hr_specialist": {
            "name": "橋本茜",
            "role": "HR専門家",
            "expertise": "人事全般、従業員関係管理、コンプライアンス",
            "tone": "プロフェッショナル、客観的、徹底的",
            "system_prompt": "あなたは経験豊富な人事専門家です。人事方針、雇用法、ベストプラクティスに関する深い知識を持ちます。常に客観的な視点で、法令遵守と従業員のウェルビーイングのバランスを考慮して回答してください。"
        },
        "talent_acquisition": {
            "name": "鈴木拓也",
            "role": "採用マネージャー",
            "expertise": "人材獲得、面接、候補者評価、採用マーケティング",
            "tone": "エネルギッシュ、説得力がある、候補者中心",
            "system_prompt": "あなたは優れた採用マネージャーです。魅力的な求人票の作成、効果的な候補者スクリーニング、包括的な面接プロセスの設計を得意とします。多様で才能のあるチームを構築するための視点で回答してください。"
        },
        "learning_development": {
            "name": "佐藤美咲",
            "role": "L&D スペシャリスト",
            "expertise": "従業員育成、トレーニング設計、能力開発",
            "tone": "サポート的、教育的、成長志向",
            "system_prompt": "あなたは学習と発展を専門とするスペシャリストです。効果的な学習プログラムの設計、従業員のスキル向上、リーダーシップ育成に焦点を当てています。継続的な成長と能力開発を奨励する視点で回答してください。"
        },
        "compensation_benefits": {
            "name": "高橋誠",
            "role": "報酬・福利厚生アナリスト",
            "expertise": "給与構造、ベネフィット設計、報酬戦略",
            "tone": "詳細志向、分析的、公平性重視",
            "system_prompt": "あなたは報酬と福利厚生の専門家です。市場競争力のある給与体系、魅力的な福利厚生パッケージ、コスト効率の良い報酬戦略を開発する専門知識を持っています。公平性とビジネスニーズのバランスを考慮して回答してください。"
        },
        "employee_relations": {
            "name": "田中香織",
            "role": "従業員関係マネージャー",
            "expertise": "紛争解決、従業員エンゲージメント、組織文化",
            "tone": "共感的、外交的、建設的",
            "system_prompt": "あなたは従業員関係の専門家です。職場の問題解決、健全な労使関係の維持、ポジティブな組織文化の促進を専門としています。共感と公平性をもって、従業員と組織の両方の利益を考慮して回答してください。"
        }
    }
    
    @staticmethod
    def create_prompt(persona_key, query, additional_context=""):
        """
        ペルソナに基づいたプロンプトを生成
        
        Args:
            persona_key: 使用するペルソナのキー
            query: ユーザークエリ
            additional_context: 追加コンテキスト（オプション）
        
        Returns:
            ペルソナベースのプロンプト文字列
        """
        # ペルソナの取得
        if persona_key not in PersonaBasedPrompt.PERSONAS:
            raise ValueError(f"Unknown persona: {persona_key}")
        
        persona = PersonaBasedPrompt.PERSONAS[persona_key]
        
        # システムプロンプトとユーザークエリを組み合わせる
        system_prompt = persona["system_prompt"]
        
        # 追加コンテキストがある場合は含める
        context_section = f"\n\n## 追加コンテキスト\n{additional_context}" if additional_context else ""
        
        # 最終プロンプト
        prompt = f"""## システム指示
{system_prompt}

## ペルソナ情報
- 名前: {persona['name']}
- 役割: {persona['role']}
- 専門分野: {persona['expertise']}
- トーン: {persona['tone']}

{context_section}

## ユーザークエリ
{query}

上記のペルソナとして、専門知識とトーンを反映した回答を提供してください。
"""
        
        return prompt

# 使用例：従業員エンゲージメント向上戦略
def generate_engagement_strategy(engagement_data, team_info):
    """従業員エンゲージメント向上戦略をペルソナベースアプローチで生成"""
    
    query = "最近のエンゲージメント調査で満足度の低下が見られます。チーム内のエンゲージメントを向上させるための実用的な戦略を5つ提案してください。"
    
    additional_context = f"""
## エンゲージメントデータ
{engagement_data}

## チーム情報
{team_info}
"""
    
    return PersonaBasedPrompt.create_prompt(
        "employee_relations",
        query,
        additional_context
    )
```

#### 4.2.3 マルチステッププロンプトワークフロー

```python
# backend/app/services/prompt_workflows.py
from typing import Dict, List, Any
import asyncio

class PromptWorkflow:
    """複数ステップのプロンプトワークフローを管理"""
    
    def __init__(self, llm_service):
        self.llm_service = llm_service
        self.results = {}
    
    async def execute_workflow(self, workflow_steps: List[Dict[str, Any]], initial_data: Dict[str, Any] = None):
        """
        定義されたステップに従ってプロンプトワークフローを実行
        
        Args:
            workflow_steps: ワークフローステップの定義リスト
            initial_data: ワークフロー開始時の初期データ
        
        Returns:
            各ステップの結果を含む辞書
        """
        data = initial_data or {}
        self.results = {}
        
        for step in workflow_steps:
            step_id = step["id"]
            prompt_template = step["prompt_template"]
            
            # 前のステップの結果を変数として使用可能
            variables = {**data, **self.results}
            
            # テンプレートに変数を適用
            prompt = self._fill_template(prompt_template, variables)
            
            # LLMに送信
            response = await self.llm_service.generate_text(prompt, step.get("model_params", {}))
            
            # 出力の後処理（必要に応じて）
            if "output_processor" in step:
                response = step["output_processor"](response)
            
            # 結果を保存
            self.results[step_id] = response
            
            # ワークフロー制御（条件付きステップなど）
            if "condition" in step and not step["condition"](response, variables):
                break
        
        return self.results
    
    def _fill_template(self, template: str, variables: Dict[str, Any]) -> str:
        """プロンプトテンプレートに変数を適用"""
        result = template
        for key, value in variables.items():
            if isinstance(value, (str, int, float, bool)):
                result = result.replace(f"{{{{{key}}}}}", str(value))
        return result


# 使用例：従業員業績改善プラン作成ワークフロー
async def create_performance_improvement_plan(employee_data, performance_issues):
    """段階的なワークフローで従業員業績改善プランを作成"""
    
    # LLMサービスのインスタンスを取得（実装に依存）
    from app.services.llm_service import get_llm_service
    llm_service = get_llm_service()
    
    workflow = PromptWorkflow(llm_service)
    
    # ワークフローステップの定義
    workflow_steps = [
        {
            "id": "issue_analysis",
            "prompt_template": """
# 業績問題分析

## 従業員情報
{{employee_data}}

## 報告された問題
{{performance_issues}}

## タスク
1. 上記の情報に基づいて、業績問題を分析してください。
2. 根本原因の可能性を特定してください。
3. 問題の重大度（低/中/高）を評価してください。

詳細な分析を提供してください。
""",
            "model_params": {
                "temperature": 0.2
            }
        },
        {
            "id": "goal_setting",
            "prompt_template": """
# 改善目標の設定

## 従業員情報
{{employee_data}}

## 問題分析
{{issue_analysis}}

## タスク
1. この従業員のために3-5つの具体的で測定可能な改善目標を設定してください。
2. 各目標の達成を測定する方法を指定してください。
3. 各目標に現実的なタイムラインを設定してください。

目標は具体的、測定可能、達成可能、関連性があり、期限付きであるべきです。
""",
            "model_params": {
                "temperature": 0.3
            }
        },
        {
            "id": "support_resources",
            "prompt_template": """
# サポートリソースと戦略

## 設定された目標
{{goal_setting}}

## タスク
従業員が上記の目標を達成するのを支援するために推奨される以下のリソースと戦略を提供してください：

1. トレーニングまたはコーチングリソース
2. 必要なツールまたは技術サポート
3. マネージャーの関与と支援戦略
4. 進捗モニタリングの仕組み
5. 成功した場合のインセンティブまたは表彰
""",
            "model_params": {
                "temperature": 0.4
            }
        },
        {
            "id": "final_pip",
            "prompt_template": """
# 業績改善プラン（PIP）最終文書

## 従業員情報
{{employee_data}}

## 問題分析
{{issue_analysis}}

## 改善目標
{{goal_setting}}

## サポートリソースと戦略
{{support_resources}}

## タスク
上記の情報を統合し、正式な業績改善プラン（PIP）文書を作成してください。以下を含める必要があります：

1. 序文と目的
2. 業績問題の簡潔な説明
3. 具体的な改善目標とタイムライン
4. 提供されるサポートと資源
5. 進捗レビュースケジュール
6. 結果と期待事項（目標達成または未達の場合）

プロフェッショナルなトーンで、建設的でサポート重視のアプローチを維持してください。
""",
            "model_params": {
                "temperature": 0.2
            }
        }
    ]
    
    # 初期データ
    initial_data = {
        "employee_data": employee_data,
        "performance_issues": performance_issues
    }
    
    # ワークフロー実行
    results = await workflow.execute_workflow(workflow_steps, initial_data)
    
    return results["final_pip"]  # 最終結果
```

### 4.3 プロンプト最適化技術

プロンプトの品質と効果を高めるための最適化手法についてです。

#### 4.3.1 プロンプト評価フレームワーク

```python
# backend/app/services/prompt_evaluation.py
from typing import List, Dict, Any
import numpy as np
import json

class PromptEvaluator:
    """プロンプトの品質と有効性を評価するフレームワーク"""
    
    def __init__(self, llm_service):
        self.llm_service = llm_service
    
    async def evaluate_prompt(self, prompt: str, criteria: List[str] = None, test_cases: List[Dict] = None):
        """
        プロンプトを評価し、品質スコアを返す
        
        Args:
            prompt: 評価するプロンプト
            criteria: 評価基準のリスト（省略可）
            test_cases: プロンプトのテストケース（省略可）
            
        Returns:
            評価結果を含む辞書
        """
        if criteria is None:
            criteria = [
                "明確さ", "完全性", "関連性", "実用性", 
                "バイアスの排除", "効率性", "正確さ"
            ]
            
        results = {}
        
        # 基本的な品質評価
        quality_score = await self._evaluate_quality(prompt, criteria)
        results["quality_evaluation"] = quality_score
        
        # テストケースがある場合は実際の応答を評価
        if test_cases:
            test_results = await self._evaluate_with_test_cases(prompt, test_cases)
            results["test_case_evaluation"] = test_results
            
        # 全体スコアの計算
        if "test_case_evaluation" in results:
            test_score = np.mean([case["score"] for case in results["test_case_evaluation"]])
            quality_avg = np.mean(list(quality_score.values()))
            results["overall_score"] = (quality_avg * 0.4) + (test_score * 0.6)  # テスト結果に重み付け
        else:
            results["overall_score"] = np.mean(list(quality_score.values()))
            
        return results
    
    async def _evaluate_quality(self, prompt: str, criteria: List[str]):
        """プロンプトの品質を評価"""
        
        # プロンプト品質評価のためのメタプロンプト
        meta_prompt = f"""
# プロンプト品質評価

## 評価対象のプロンプト

{prompt}


## タスク
上記のプロンプトを以下の基準で評価してください。各基準について1-10の数値スコアと簡潔な説明を提供してください：

{chr(10).join([f'- {criterion}' for criterion in criteria])}

## 出力形式
JSONオブジェクトとして結果を返してください：
```json
{{
    "criterion1": {{"score": X, "explanation": "説明..."}},
    "criterion2": {{"score": X, "explanation": "説明..."}},
    ...
}}
```
```

客観的に評価し、改善のための具体的なフィードバックを提供してください。
"""
        
        # LLMにメタプロンプトを送信
        response = await self.llm_service.generate_text(meta_prompt, {"temperature": 0.2})
        
        # JSONレスポースのパース
        try:
            # JSON部分を抽出
            json_content = response
            if "```json" in response:
                json_content = response.split("```json")[1].split("```")[0].strip()
            elif "```" in response:
                json_content = response.split("```")[1].strip()
                
            evaluation = json.loads(json_content)
            
            # スコアのみの辞書に変換
            scores = {k: v["score"] for k, v in evaluation.items()}
            return scores
            
        except Exception as e:
            print(f"Error parsing evaluation response: {e}")
            # エラー時はダミー値を返す
            return {criterion: 5 for criterion in criteria}
    
    async def _evaluate_with_test_cases(self, prompt: str, test_cases: List[Dict]):
        """テストケースを使用してプロンプトを評価"""
        results = []
        
        for i, test_case in enumerate(test_cases):
            input_data = test_case.get("input", "")
            expected = test_case.get("expected", "")
            evaluation_criteria = test_case.get("criteria", "関連性、正確性、完全性")
            
            # テストケースの入力をプロンプトに適用
            full_prompt = prompt
            if input_data:
                if isinstance(input_data, dict):
                    # 辞書の場合、キーを変数としてプロンプトに挿入
                    for key, value in input_data.items():
                        full_prompt = full_prompt.replace(f"{{{{{key}}}}}", str(value))
                else:
                    # 文字列の場合、そのまま追加
                    full_prompt += f"\n\n{input_data}"
            
            # LLMにテストプロンプトを送信
            response = await self.llm_service.generate_text(full_prompt, test_case.get("model_params", {}))
            
            # 結果の評価
            eval_prompt = f"""
# プロンプト応答評価

## 評価基準
{evaluation_criteria}

## 期待される応答の要素
{expected}

## 実際の応答
{response}

## タスク
上記の実際の応答を評価基準と期待される要素に基づいて評価してください。
1-10のスケールでスコアを付け、簡潔な説明を提供してください。

## 出力形式
```json
{{
    "score": X,
    "explanation": "説明...",
    "strengths": ["強み1", "強み2", ...],
    "weaknesses": ["弱み1", "弱み2", ...]
}}
```
```

"""
            
            # 評価プロンプトを送信
            eval_response = await self.llm_service.generate_text(eval_prompt, {"temperature": 0.2})
            
            # JSON応答の解析
            try:
                # JSON部分を抽出
                json_content = eval_response
                if "```json" in eval_response:
                    json_content = eval_response.split("```json")[1].split("```")[0].strip()
                elif "```" in eval_response:
                    json_content = eval_response.split("```")[1].strip()
                    
                evaluation = json.loads(json_content)
                
                results.append({
                    "test_case": i + 1,
                    "response": response,
                    "score": evaluation.get("score", 5),
                    "explanation": evaluation.get("explanation", ""),
                    "strengths": evaluation.get("strengths", []),
                    "weaknesses": evaluation.get("weaknesses", [])
                })
                
            except Exception as e:
                print(f"Error parsing test case evaluation: {e}")
                results.append({
                    "test_case": i + 1,
                    "response": response,
                    "score": 5,
                    "explanation": "評価解析エラー",
                    "strengths": [],
                    "weaknesses": ["評価結果の解析に失敗しました"]
                })
                
        return results
```

#### 4.3.2 A/Bテストフレームワーク

```python
# backend/app/services/prompt_ab_testing.py
import asyncio
import random
from typing import List, Dict, Any, Callable

class PromptABTester:
    """プロンプトバリエーションのA/Bテストを実施するフレームワーク"""
    
    def __init__(self, llm_service, evaluator):
        self.llm_service = llm_service
        self.evaluator = evaluator  # プロンプト評価用サービス
    
    async def run_ab_test(
        self,
        prompt_variants: List[str],
        test_inputs: List[Dict[str, Any]],
        evaluation_fn: Callable[[str, Dict[str, Any]], float] = None,
        n_iterations: int = 10
    ):
        """
        プロンプトバリエーションのA/Bテストを実行

        Args:
            prompt_variants: テストするプロンプトバリエーションのリスト
            test_inputs: テストの入力データリスト
            evaluation_fn: レスポンス評価関数（省略時は内部評価を使用）
            n_iterations: テスト反復回数

        Returns:
            テスト結果の詳細
        """
        if len(prompt_variants) < 2:
            raise ValueError("A/Bテストには少なくとも2つのプロンプトバリエーションが必要です")
        
        results = {f"variant_{i}": {"prompt": prompt, "scores": []} for i, prompt in enumerate(prompt_variants)}
        
        # すべてのバリエーションと入力の組み合わせをテスト
        for iteration in range(n_iterations):
            # テスト順序をランダム化
            random.shuffle(test_inputs)
            
            for test_input in test_inputs:
                for var_id, var_info in results.items():
                    # テスト入力をプロンプトに適用
                    filled_prompt = self._apply_input_to_prompt(var_info["prompt"], test_input)
                    
                    # LLMからレスポンス生成
                    response = await self.llm_service.generate_text(filled_prompt)
                    
                    # カスタム評価関数または内部評価を使用
                    if evaluation_fn:
                        score = evaluation_fn(response, test_input)
                    else:
                        # 内部評価 - 期待される出力との比較など
                        score = await self._evaluate_response(response, test_input)
                    
                    # スコアを記録
                    var_info["scores"].append(score)
        
        # 結果の集計と統計処理
        summary = self._calculate_summary(results)
        
        return {
            "detailed_results": results,
            "summary": summary,
            "winner": summary["best_variant"],
            "improvement": summary["improvement_over_base"]
        }
    
    async def _evaluate_response(self, response: str, test_input: Dict[str, Any]) -> float:
        """内部評価ロジック - 必要に応じてカスタマイズ"""
        # 期待出力がある場合は比較評価
        if "expected_output" in test_input:
            comparison_prompt = f"""
# 応答評価

## 期待される出力
{test_input.get('expected_output')}

## 実際の応答
{response}

## タスク
上記の実際の応答を期待される出力と比較し、0-10のスケールで類似性スコアを提供してください。
内容の正確性、完全性、関連性を考慮してください。

回答は数字のみで答えてください。
"""
            result = await self.llm_service.generate_text(comparison_prompt, {"temperature": 0.1})
            
            try:
                score = float(result.strip())
                return min(max(score, 0), 10)  # 0-10の範囲に制限
            except:
                # 数値解析に失敗した場合は中間値を返す
                return 5.0
        
        # 評価基準がある場合はそれに基づいて評価
        elif "evaluation_criteria" in test_input:
            criteria = test_input.get("evaluation_criteria")
            evaluation_prompt = f"""
# 応答評価

## 評価基準
{criteria}

## 応答
{response}

## タスク
上記の応答を評価基準に基づいて0-10のスケールで評価してください。

回答は数字のみで答えてください。
"""
            result = await self.llm_service.generate_text(evaluation_prompt, {"temperature": 0.1})
            
            try:
                score = float(result.strip())
                return min(max(score, 0), 10)
            except:
                return 5.0
        
        # いずれもない場合は一般的な品質を評価
        else:
            quality_prompt = f"""
# 応答品質評価

## 応答
{response}

## タスク
上記の応答の全体的な品質を0-10のスケールで評価してください。
明確さ、有用性、適切性を考慮してください。

回答は数字のみで答えてください。
"""
            result = await self.llm_service.generate_text(quality_prompt, {"temperature": 0.1})
            
            try:
                score = float(result.strip())
                return min(max(score, 0), 10)
            except:
                return 5.0
    
    def _apply_input_to_prompt(self, prompt_template: str, input_data: Dict[str, Any]) -> str:
        """入力データをプロンプトテンプレートに適用"""
        result = prompt_template
        
        # プロンプト末尾に追加するユーザー入力
        user_input = input_data.get("user_input", "")
        
        # テンプレート変数の置換
        for key, value in input_data.items():
            if isinstance(value, (str, int, float, bool)):
                result = result.replace(f"{{{{{key}}}}}", str(value))
        
        # ユーザー入力がある場合は追加
        if user_input and user_input not in result:
            result += f"\n\n{user_input}"
            
        return result
    
    def _calculate_summary(self, results: Dict[str, Dict[str, Any]]) -> Dict[str, Any]:
        """テスト結果のサマリーを計算"""
        summary = {}
        
        # 各バリエーションの平均スコアを計算
        for var_id, var_info in results.items():
            scores = var_info["scores"]
            if scores:
                var_info["mean_score"] = sum(scores) / len(scores)
                var_info["median_score"] = sorted(scores)[len(scores) // 2]
            else:
                var_info["mean_score"] = var_info["median_score"] = 0
        
        # 最良のバリエーションを特定
        best_variant = max(results.items(), key=lambda x: x[1]["mean_score"])
        summary["best_variant"] = best_variant[0]
        summary["best_score"] = best_variant[1]["mean_score"]
        
        # ベースライン（variant_0）からの改善率を計算
        base_score = results["variant_0"]["mean_score"]
        summary["base_score"] = base_score
        
        if base_score > 0:
            improvement = ((summary["best_score"] - base_score) / base_score) * 100
            summary["improvement_over_base"] = f"{improvement:.2f}%"
        else:
            summary["improvement_over_base"] = "N/A"
        
        # 全バリエーションのランキング
        ranked_variants = sorted(
            [(var_id, var_info["mean_score"]) for var_id, var_info in results.items()],
            key=lambda x: x[1],
            reverse=True
        )
        summary["ranking"] = ranked_variants
        
        return summary
```

#### 4.3.3 自動プロンプト最適化

```python
# backend/app/services/prompt_optimizer.py
import random
import asyncio
from typing import List, Dict, Any, Optional

class PromptOptimizer:
    """AIプロンプトの自動最適化を行うサービス"""
    
    def __init__(self, llm_service, evaluator):
        self.llm_service = llm_service
        self.evaluator = evaluator
    
    async def optimize_prompt(
        self,
        base_prompt: str,
        test_cases: List[Dict[str, Any]],
        optimization_rounds: int = 3,
        variants_per_round: int = 3,
        target_score: float = 9.0
    ) -> Dict[str, Any]:
        """
        プロンプトを複数ラウンドで最適化する

        Args:
            base_prompt: 最適化の基点となるプロンプト
            test_cases: プロンプトの評価に使用するテストケース
            optimization_rounds: 最適化ラウンド数
            variants_per_round: ラウンドごとに生成するバリアント数
            target_score: 目標スコア（これに達したら停止）

        Returns:
            最適化結果の詳細
        """
        current_best = base_prompt
        current_score = await self._evaluate_prompt(current_best, test_cases)
        
        optimization_history = [{
            "round": 0,
            "prompt": current_best,
            "score": current_score,
            "improvements": []
        }]
        
        # 最適化ラウンドを実行
        for round_num in range(1, optimization_rounds + 1):
            print(f"最適化ラウンド {round_num}/{optimization_rounds} 開始")
            
            # 現在のプロンプトの改善点を特定
            improvement_areas = await self._identify_improvements(current_best, test_cases)
            
            # 改善を適用したプロンプトバリアントを生成
            variants = await self._generate_prompt_variants(
                current_best, 
                improvement_areas, 
                variants_per_round
            )
            
            # バリアントを評価
            best_variant = current_best
            best_variant_score = current_score
            
            variant_results = []
            for i, variant in enumerate(variants):
                variant_score = await self._evaluate_prompt(variant, test_cases)
                variant_results.append({"prompt": variant, "score": variant_score})
                
                if variant_score > best_variant_score:
                    best_variant = variant
                    best_variant_score = variant_score
            
            # ラウンドの結果を記録
            optimization_history.append({
                "round": round_num,
                "prompt": best_variant,
                "score": best_variant_score,
                "improvements": improvement_areas,
                "variants": variant_results
            })
            
            # 結果の更新
            if best_variant_score > current_score:
                current_best = best_variant
                current_score = best_variant_score
                print(f"ラウンド {round_num} で改善: スコア {current_score:.2f}")
            else:
                print(f"ラウンド {round_num} で改善なし: スコア {current_score:.2f}")
            
            # 目標スコア到達で早期終了
            if current_score >= target_score:
                print(f"目標スコア {target_score} に達したため最適化を終了します")
                break
        
        return {
            "original_prompt": base_prompt,
            "original_score": optimization_history[0]["score"],
            "optimized_prompt": current_best,
            "optimized_score": current_score,
            "improvement_percentage": ((current_score - optimization_history[0]["score"]) / 
                                     optimization_history[0]["score"]) * 100 if optimization_history[0]["score"] > 0 else 0,
            "optimization_history": optimization_history
        }
    
    async def _evaluate_prompt(self, prompt: str, test_cases: List[Dict[str, Any]]) -> float:
        """プロンプトの品質をテストケースを使用して評価"""
        evaluation = await self.evaluator.evaluate_prompt(prompt, test_cases=test_cases)
        return evaluation["overall_score"]
    
    async def _identify_improvements(
        self, prompt: str, test_cases: List[Dict[str, Any]]
    ) -> List[Dict[str, str]]:
        """プロンプトの改善領域を特定"""
        
        # テストケースからの応答を収集
        responses = []
        for test_case in test_cases:
            # テスト入力をプロンプトに適用
            filled_prompt = prompt
            input_data = test_case.get("input", {})
            
            if isinstance(input_data, dict):
                for key, value in input_data.items():
                    filled_prompt = filled_prompt.replace(f"{{{{{key}}}}}", str(value))
            
            # LLMからレスポンスを取得
            response = await self.llm_service.generate_text(filled_prompt)
            responses.append(response)
        
        # 改善分析のためのメタプロンプト
        analysis_prompt = f"""

# プロンプト改善分析

## 現在のプロンプト
{prompt}


## テストケースからの応答
{chr(10).join([f"### 応答 {i+1}\n```\n{response}\n```" for i, response in enumerate(responses)])}

## タスク
上記のプロンプトとそこから得られた応答を分析し、プロンプトの改善領域を特定してください。
以下の観点で考慮してください：

1. 明確さと具体性
2. 構造と組織
3. ガイダンスの詳細さ
4. トーンと表現
5. 例示やコンテキスト
6. 出力形式の指定
7. エッジケースの対応

具体的な改善提案を3-5項目リストアップしてください。各提案には、問題点、理由、具体的な改善方法を含めてください。

## 出力形式
```json
[
  {{
    "problem": "問題点の要約",
    "reason": "なぜこれが問題か",
    "improvement": "具体的な改善提案"
  }},
  ...
]
```
```

"""
        
        # 改善分析を実行
        analysis_response = await self.llm_service.generate_text(analysis_prompt, {"temperature": 0.2})
        
        # JSON部分を抽出して解析
        try:
            # JSON部分を抽出
            json_content = analysis_response
            
            if "```json" in analysis_response:
                json_content = analysis_response.split("```json")[1].split("```")[0].strip()
            elif "```" in analysis_response:
                json_content = analysis_response.split("```")[1].strip()
            
            import json
            improvements = json.loads(json_content)
            return improvements
        except Exception as e:
            print(f"改善提案の解析エラー: {e}")
            return []
    
    async def _generate_prompt_variants(
        self, base_prompt: str, improvements: List[Dict[str, str]], num_variants: int
    ) -> List[str]:
        """改善提案に基づいた複数のプロンプトバリアントを生成"""
        
        if not improvements:
            return [base_prompt] * num_variants
        
        # 一部の改善をランダムに適用するためのヘルパー関数
        def get_random_improvements(all_improvements, min_count=1):
            count = random.randint(min_count, len(all_improvements))
            return random.sample(all_improvements, count)
            
        variants = []
        generation_tasks = []
        
        for i in range(num_variants):
            # 各バリアントに適用する改善をランダムに選択
            selected_improvements = get_random_improvements(improvements)
            
            # 改善提案に基づくバリアント生成プロンプト
            generation_prompt = f"""
# プロンプトバリアント生成

## 元のプロンプト
{base_prompt}


## 適用すべき改善点
{chr(10).join([f"### 改善点 {j+1}\n問題: {imp['problem']}\n理由: {imp['reason']}\n改善案: {imp['improvement']}" for j, imp in enumerate(selected_improvements)])}

## タスク
上記の元のプロンプトに対して、指定された改善点を適用した新しいバージョンを作成してください。
改善点を適切に統合し、全体の一貫性とトーンを維持してください。
元のプロンプトの重要な要素や構造は保持しつつ、必要な改善を加えてください。

## 出力
改善されたプロンプトの完全なテキストのみを出力してください。説明や追加コメントは含めないでください。
"""
            
            # タスクを生成
            task = self.llm_service.generate_text(generation_prompt, {"temperature": 0.3})
            generation_tasks.append(task)
        
        # すべてのバリアント生成を並行実行
        variant_responses = await asyncio.gather(*generation_tasks)
        
        # 応答からプロンプトバリアントを抽出
        for response in variant_responses:
            # コードブロックがある場合はそれを抽出
            if "```" in response:
                prompt_content = response.split("```")[1].strip()
                if prompt_content.startswith("prompt") or prompt_content.startswith("python"):
                    prompt_content = "\n".join(prompt_content.split("\n")[1:])
                variants.append(prompt_content)
            else:
                # コードブロックがなければ応答全体をプロンプトとして使用
                variants.append(response.strip())
        
        return variants
```

### 4.4 プロンプトテストとバリデーション

プロンプトの信頼性と品質を確保するためのテスト手法についてです。

#### 4.4.1 自動化されたプロンプトテスト

```python
# backend/app/services/prompt_testing.py
import asyncio
import json
from typing import List, Dict, Any, Union, Optional

class PromptTester:
    """プロンプトの自動テストと検証を行うサービス"""
    
    def __init__(self, llm_service):
        self.llm_service = llm_service
        # テスト結果の成功基準
        self.success_criteria = {
            "min_score": 7.0,  # 最低スコア
            "min_success_rate": 0.8  # 成功率
        }
    
    async def run_test_suite(
        self, 
        prompt_template: str,
        test_cases: List[Dict[str, Any]],
        success_criteria: Optional[Dict[str, float]] = None
    ) -> Dict[str, Any]:
        """
        プロンプトに対するテストスイートを実行

        Args:
            prompt_template: テストするプロンプトテンプレート
            test_cases: テストケースのリスト
            success_criteria: 成功基準（省略時はデフォルト値を使用）

        Returns:
            テスト結果の詳細
        """
        criteria = success_criteria or self.success_criteria
        
        results = []
        passed_tests = 0
        total_score = 0.0
        
        for i, test_case in enumerate(test_cases):
            print(f"テストケース {i+1}/{len(test_cases)} を実行中...")
            
            result = await self.run_test_case(prompt_template, test_case)
            results.append(result)
            
            # 成功判定
            if result["score"] >= criteria["min_score"]:
                passed_tests += 1
                
            total_score += result["score"]
        
        # 結果の集計
        success_rate = passed_tests / len(test_cases) if test_cases else 0
        avg_score = total_score / len(test_cases) if test_cases else 0
        passed = success_rate >= criteria["min_success_rate"] and avg_score >= criteria["min_score"]
        
        summary = {
            "passed": passed,
            "success_rate": success_rate,
            "average_score": avg_score,
            "tests_passed": f"{passed_tests}/{len(test_cases)}",
            "criteria_used": criteria
        }
        
        return {
            "prompt_template": prompt_template,
            "test_results": results,
            "summary": summary
        }
    
    async def run_test_case(
        self, prompt_template: str, test_case: Dict[str, Any]
    ) -> Dict[str, Any]:
        """
        単一テストケースの実行

        Args:
            prompt_template: テストするプロンプトテンプレート
            test_case: テストケース定義

        Returns:
            テスト結果
        """
        # テストケース情報の取得
        input_data = test_case.get("input", {})
        expected_output = test_case.get("expected", "")
        assertion = test_case.get("assertion", "content_similarity")
        
        # プロンプトに入力データを適用
        filled_prompt = prompt_template
        
        if isinstance(input_data, dict):
            # 辞書の場合、キーをテンプレート変数として置換
            for key, value in input_data.items():
                filled_prompt = filled_prompt.replace(f"{{{{{key}}}}}", str(value))
        elif isinstance(input_data, str):
            # 文字列の場合、追加入力として追加
            if "{{input}}" in filled_prompt:
                filled_prompt = filled_prompt.replace("{{input}}", input_data)
            else:
                filled_prompt += f"\n\n{input_data}"
        
        # モデルパラメータの取得
        model_params = test_case.get("model_params", {"temperature": 0.2})
        
        # プロンプトを実行して応答を取得
        response = await self.llm_service.generate_text(filled_prompt, model_params)
        
        # テスト結果の評価
        evaluation = await self._evaluate_test_result(response, expected_output, assertion)
        
        # 結果の構築
        result = {
            "test_case_name": test_case.get("name", f"Test {hash(str(input_data)) % 10000}"),
            "input": input_data,
            "expected_output": expected_output,
            "actual_output": response,
            "score": evaluation["score"],
            "passed": evaluation["score"] >= self.success_criteria["min_score"],
            "evaluation_details": evaluation
        }
        
        return result
    
    async def _evaluate_test_result(
        self, actual: str, expected: Union[str, Dict, List], assertion_type: str
    ) -> Dict[str, Any]:
        """テスト結果を評価"""
        
        if assertion_type == "exact_match":
            # 完全一致チェック
            matches = actual.strip() == expected.strip() if isinstance(expected, str) else False
            score = 10.0 if matches else 0.0
            return {
                "assertion_type": assertion_type,
                "score": score,
                "matches": matches,
                "details": "完全に一致" if matches else "一致しません"
            }
            
        elif assertion_type == "contains":
            # 含有チェック
            if isinstance(expected, str):
                contains = expected.strip() in actual
                score = 10.0 if contains else 0.0
                return {
                    "assertion_type": assertion_type,
                    "score": score,
                    "contains": contains,
                    "details": "期待値を含みます" if contains else "期待値を含みません"
                }
            elif isinstance(expected, list):
                # リストの場合、すべての要素が含まれていることをチェック
                contained_items = [item for item in expected if item in actual]
                contains_all = len(contained_items) == len(expected)
                contains_ratio = len(contained_items) / len(expected) if expected else 0
                score = contains_ratio * 10.0
                return {
                    "assertion_type": assertion_type,
                    "score": score,
                    "contains_all": contains_all,
                    "contains_ratio": contains_ratio,
                    "contained_items": contained_items,
                    "missing_items": [item for item in expected if item not in actual],
                    "details": f"{len(contained_items)}/{len(expected)} 項目が含まれています"
                }
            else:
                return {
                    "assertion_type": assertion_type,
                    "score": 0.0,
                    "error": "非対応の期待値タイプです"
                }
                
        elif assertion_type == "json_structure":
            # JSON構造チェック
            try:
                actual_json = json.loads(actual) if isinstance(actual, str) else actual
                
                if isinstance(expected, dict):
                    # すべてのキーが存在するかチェック
                    expected_keys = set(_flatten_dict_keys(expected))
                    actual_keys = set(_flatten_dict_keys(actual_json))
                    
                    common_keys = expected_keys.intersection(actual_keys)
                    missing_keys = expected_keys - actual_keys
                    
                    structure_score = len(common_keys) / len(expected_keys) if expected_keys else 0
                    score = structure_score * 10.0
                    
                    return {
                        "assertion_type": assertion_type,
                        "score": score,
                        "structure_match_ratio": structure_score,
                        "common_keys": list(common_keys),
                        "missing_keys": list(missing_keys),
                        "details": f"{len(common_keys)}/{len(expected_keys)} のキーが一致しています"
                    }
                else:
                    return {
                        "assertion_type": assertion_type,
                        "score": 0.0,
                        "error": "JSON構造チェックには辞書型の期待値が必要です"
                    }
            except Exception as e:
                return {
                    "assertion_type": assertion_type,
                    "score": 0.0,
                    "error": f"JSON解析エラー: {str(e)}"
                }
                
        else:
            # デフォルト: コンテンツ類似性チェック
            similarity_prompt = f"""
# 応答類似性評価

## 期待される出力
{expected}

## 実際の出力
{actual}

## タスク
上記の実際の出力と期待される出力の類似性を、内容の正確さと完全性の観点から評価してください。
0-10のスケールでスコアを付け、評価の理由を簡潔に説明してください。

## 出力形式
```json
{{
    "score": X,
    "explanation": "評価理由...",
    "matching_points": ["一致点1", "一致点2", ...],
    "missing_points": ["欠落点1", "欠落点2", ...]
}}
```
```

"""
            
            try:
                result = await self.llm_service.generate_text(similarity_prompt, {"temperature": 0.1})
                
                # JSON部分を抽出
                json_content = result
                if "```json" in result:
                    json_content = result.split("```json")[1].split("```")[0].strip()
                elif "```" in result:
                    json_content = result.split("```")[1].strip()
                
                evaluation = json.loads(json_content)
                evaluation["assertion_type"] = "content_similarity"
                return evaluation
                
            except Exception as e:
                print(f"類似性評価エラー: {e}")
                # エラー時のデフォルト評価
                return {
                    "assertion_type": "content_similarity",
                    "score": 5.0,  # 中間値
                    "explanation": "評価処理でエラーが発生しました",
                    "error": str(e)
                }

def _flatten_dict_keys(d, parent_key=''):
    """辞書のキーを平坦化してリストを作成するヘルパー関数"""
    items = []
    for k, v in d.items():
        new_key = f"{parent_key}.{k}" if parent_key else k
        if isinstance(v, dict):
            items.extend(_flatten_dict_keys(v, new_key))
        else:
            items.append(new_key)
    return items
```

#### 4.4.2 エッジケーステスト

```python
# backend/app/services/edge_case_testing.py
from typing import List, Dict, Any
import random

class EdgeCaseTester:
    """プロンプトのエッジケースをテストするサービス"""
    
    def __init__(self, llm_service, prompt_tester):
        self.llm_service = llm_service
        self.prompt_tester = prompt_tester
    
    async def generate_edge_cases(
        self, 
        prompt_template: str,
        sample_inputs: List[Dict[str, Any]] = None,
        num_edge_cases: int = 5
    ) -> List[Dict[str, Any]]:
        """
        プロンプトのエッジケースを生成

        Args:
            prompt_template: テスト対象のプロンプトテンプレート
            sample_inputs: サンプル入力のリスト（あれば）
            num_edge_cases: 生成するエッジケースの数

        Returns:
            生成されたエッジケーステストケースのリスト
        """
        # プロンプトからテンプレート変数を抽出
        template_vars = self._extract_template_vars(prompt_template)
        
        # サンプル入力を解析してデータ型や範囲を把握
        var_analysis = {}
        if sample_inputs:
            for var in template_vars:
                values = [
                    input_data.get(var) for input_data in sample_inputs 
                    if isinstance(input_data, dict) and var in input_data
                ]
                
                if values:
                    var_analysis[var] = self._analyze_values(values)
        
        # エッジケース生成プロンプト
        generation_prompt = f"""
# プロンプトエッジケース生成

## テスト対象のプロンプトテンプレート
{prompt_template}


## テンプレート変数
{', '.join(template_vars) if template_vars else '(テンプレート変数なし)'}

## 変数分析
{self._format_var_analysis(var_analysis)}

## タスク
上記のプロンプトテンプレートに対して、以下のようなエッジケースを考え、テストケースとして設計してください：

1. 極端な入力値（非常に長い、空、特殊文字を含む等）
2. 予期しない形式の入力（数値が期待される箇所に文字列など）
3. 矛盾する情報
4. 言語的に曖昧な入力
5. 複数の解釈が可能なケース
6. セキュリティリスクがあるケース（プロンプトインジェクション等）
7. 多言語・特殊文字のケース

各テストケースには、テスト名、入力データ、テストの目的を含めてください。

## 出力形式
JSONの配列として、以下のようなテストケースを{num_edge_cases}個出力してください：

```json
[
  {{
    "name": "テストケース名",
    "input": {{
      "template_var1": "値1",
      "template_var2": "値2",
      ...
    }},
    "purpose": "このテストケースの目的の説明",
    "expected_issue": "予想される問題点"
  }},
  ...
]
```
```

テストケースは多様性があり、さまざまな種類の問題を検出できるようにしてください。
"""
        
        # エッジケースの生成
        response = await self.llm_service.generate_text(generation_prompt, {"temperature": 0.8})
        
        # JSONを抽出
        try:
            json_content = response
            if "```json" in response:
                json_content = response.split("```json")[1].split("```")[0].strip()
            elif "```" in response:
                json_content = response.split("```")[1].strip()
            
            import json
            edge_cases = json.loads(json_content)
            
            # テストケース形式に変換
            test_cases = []
            for case in edge_cases:
                test_case = {
                    "name": case.get("name", "エッジケーステスト"),
                    "input": case.get("input", {}),
                    "assertion": "robustness",  # エッジケースは堅牢性をテスト
                    "metadata": {
                        "purpose": case.get("purpose", ""),
                        "expected_issue": case.get("expected_issue", "")
                    }
                }
                test_cases.append(test_case)
            
            return test_cases
            
        except Exception as e:
            print(f"エッジケース生成エラー: {e}")
            return []
    
    async def test_edge_cases(
        self, 
        prompt_template: str,
        edge_cases: List[Dict[str, Any]] = None,
        num_cases: int = 5
    ) -> Dict[str, Any]:
        """
        プロンプトに対してエッジケーステストを実行

        Args:
            prompt_template: テスト対象のプロンプトテンプレート
            edge_cases: 使用するエッジケース（なければ自動生成）
            num_cases: 自動生成する場合のエッジケース数

        Returns:
            テスト結果の詳細
        """
        # エッジケースがなければ生成
        if not edge_cases:
            edge_cases = await self.generate_edge_cases(
                prompt_template,
                num_edge_cases=num_cases
            )
        
        # エッジケースを実行
        robust_cases = []
        failed_cases = []
        
        for case in edge_cases:
            # テスト実行
            result = await self._evaluate_edge_case(prompt_template, case)
            
            # 結果を分類
            if result["is_robust"]:
                robust_cases.append(result)
            else:
                failed_cases.append(result)
        
        # サマリーの作成
        robustness_score = len(robust_cases) / len(edge_cases) if edge_cases else 0
        robustness_percentage = robustness_score * 100
        
        summary = {
            "robustness_score": robustness_score,
            "robustness_percentage": f"{robustness_percentage:.1f}%",
            "total_cases": len(edge_cases),
            "robust_cases": len(robust_cases),
            "failed_cases": len(failed_cases),
            "common_issues": await self._identify_common_issues(failed_cases)
        }
        
        return {
            "prompt_template": prompt_template,
            "edge_case_results": robust_cases + failed_cases,
            "robust_cases": robust_cases,
            "failed_cases": failed_cases,
            "summary": summary
        }
    
    async def _evaluate_edge_case(
        self, prompt_template: str, test_case: Dict[str, Any]
    ) -> Dict[str, Any]:
        """エッジケースの評価実行"""
        try:
            input_data = test_case.get("input", {})
            
            # プロンプトにエッジケース入力を適用
            filled_prompt = prompt_template
            for key, value in input_data.items():
                filled_prompt = filled_prompt.replace(f"{{{{{key}}}}}", str(value))
            
            # LLMからレスポンス取得
            response = await self.llm_service.generate_text(filled_prompt, {"temperature": 0.2})
            
            # レスポンスの堅牢性評価
            robustness_prompt = f"""
# プロンプト堅牢性評価

## エッジケース
{test_case.get("metadata", {}).get("purpose", "エッジケーステスト")}

## 予想される問題点
{test_case.get("metadata", {}).get("expected_issue", "不明")}

## 生成された応答
{response}

## タスク
上記の応答がエッジケースに対してどの程度堅牢に対応できたかを評価してください。
以下の観点で評価してください：
1. エラー処理: 不適切な入力を適切に認識し、対処したか
2. 一貫性: 応答は一貫性があり、矛盾がないか
3. 適切性: 回答が適切な範囲と形式を保持しているか
4. アラート: 問題のある入力について適切に警告したか

## 出力形式
```json
{{
    "is_robust": true/false,
    "robustness_score": 0-10,
    "explanation": "評価の説明...",
    "issues_detected": ["検出された問題1", "検出された問題2", ...]
}}
```
```

"""
            
            evaluation_response = await self.llm_service.generate_text(robustness_prompt, {"temperature": 0.1})
            
            # 評価結果の解析
            import json
            try:
                # JSON部分を抽出
                json_content = evaluation_response
                if "```json" in evaluation_response:
                    json_content = evaluation_response.split("```json")[1].split("```")[0].strip()
                elif "```" in evaluation_response:
                    json_content = evaluation_response.split("```")[1].strip()
                
                evaluation = json.loads(json_content)
                
                # 結果の構築
                return {
                    "test_case": test_case.get("name", "エッジケーステスト"),
                    "input": input_data,
                    "response": response,
                    "is_robust": evaluation.get("is_robust", False),
                    "robustness_score": evaluation.get("robustness_score", 0),
                    "explanation": evaluation.get("explanation", ""),
                    "issues_detected": evaluation.get("issues_detected", []),
                    "purpose": test_case.get("metadata", {}).get("purpose", ""),
                    "expected_issue": test_case.get("metadata", {}).get("expected_issue", "")
                }
                
            except Exception as e:
                print(f"評価レスポース解析エラー: {e}")
                # 解析エラー時のフォールバック
                return {
                    "test_case": test_case.get("name", "エッジケーステスト"),
                    "input": input_data,
                    "response": response,
                    "is_robust": False,
                    "robustness_score": 0,
                    "explanation": "評価結果の解析に失敗しました",
                    "issues_detected": ["評価レスポンス解析エラー"],
                    "error": str(e),
                    "purpose": test_case.get("metadata", {}).get("purpose", ""),
                    "expected_issue": test_case.get("metadata", {}).get("expected_issue", "")
                }
                
        except Exception as e:
            print(f"エッジケース評価エラー: {e}")
            # 実行エラー時のフォールバック
            return {
                "test_case": test_case.get("name", "エッジケーステスト"),
                "input": test_case.get("input", {}),
                "response": None,
                "is_robust": False,
                "robustness_score": 0,
                "explanation": "テスト実行中にエラーが発生しました",
                "issues_detected": ["テスト実行エラー"],
                "error": str(e),
                "purpose": test_case.get("metadata", {}).get("purpose", ""),
                "expected_issue": test_case.get("metadata", {}).get("expected_issue", "")
            }
    
    async def _identify_common_issues(self, failed_cases: List[Dict[str, Any]]) -> List[Dict[str, Any]]:
        """失敗したケースから共通の問題パターンを特定"""
        if not failed_cases:
            return []
        
        # 検出された問題を収集
        all_issues = []
        for case in failed_cases:
            issues = case.get("issues_detected", [])
            for issue in issues:
                all_issues.append({
                    "issue": issue,
                    "test_case": case.get("test_case", ""),
                    "robustness_score": case.get("robustness_score", 0)
                })
        
        # 問題がない場合は終了
        if not all_issues:
            return []
        
        # 共通問題パターンの分析
        analysis_prompt = f"""
# エッジケーステストの失敗パターン分析

## 検出された問題
{chr(10).join([f"- {issue['issue']} (テストケース: {issue['test_case']}, スコア: {issue['robustness_score']})" for issue in all_issues])}

## タスク
上記の問題を分析し、共通のパターンを特定してください。各パターンについて、その説明と考えられる解決策を提案してください。

## 出力形式
```json
[
  {{
    "pattern": "パターン名",
    "description": "パターンの説明",
    "frequency": "頻度 (例: 高/中/低)",
    "solution": "提案される解決策"
  }},
  ...
]
```
```

最も重要なパターンを3-5個特定してください。
"""
        
        # 分析の実行
        try:
            response = await self.llm_service.generate_text(analysis_prompt, {"temperature": 0.2})
            
            # JSON部分を抽出
            json_content = response
            if "```json" in response:
                json_content = response.split("```json")[1].split("```")[0].strip()
            elif "```" in response:
                json_content = response.split("```")[1].strip()
            
            import json
            patterns = json.loads(json_content)
            return patterns
            
        except Exception as e:
            print(f"パターン分析エラー: {e}")
            return [{
                "pattern": "分析エラー",
                "description": "パターン分析中にエラーが発生しました",
                "frequency": "不明",
                "solution": "手動での問題確認を行ってください"
            }]
    
    def _extract_template_vars(self, template: str) -> List[str]:
        """テンプレート文字列から変数を抽出"""
        import re
        # {{変数名}} パターンを検索
        pattern = r'\{\{\s*(\w+)\s*\}\}'
        matches = re.findall(pattern, template)
        return list(set(matches))  # 重複を除去
    
    def _analyze_values(self, values: List[Any]) -> Dict[str, Any]:
        """値のリストを分析して型や範囲などを特定"""
        if not values:
            return {"type": "unknown", "examples": []}
        
        # 型の判定
        types = set(type(v).__name__ for v in values if v is not None)
        
        # 数値の場合、範囲を計算
        if all(isinstance(v, (int, float)) for v in values if v is not None):
            non_none_values = [v for v in values if v is not None]
            if non_none_values:
                return {
                    "type": list(types),
                    "min": min(non_none_values),
                    "max": max(non_none_values),
                    "avg": sum(non_none_values) / len(non_none_values),
                    "examples": random.sample(values, min(3, len(values)))
                }
        
        # 文字列の場合、長さなどを分析
        if all(isinstance(v, str) for v in values if v is not None):
            non_none_values = [v for v in values if v is not None]
            if non_none_values:
                lengths = [len(v) for v in non_none_values]
                return {
                    "type": list(types),
                    "min_length": min(lengths),
                    "max_length": max(lengths),
                    "avg_length": sum(lengths) / len(lengths),
                    "examples": random.sample(values, min(3, len(values)))
                }
        
        # その他の型
        return {
            "type": list(types),
            "examples": random.sample(values, min(3, len(values)))
        }
    
    def _format_var_analysis(self, var_analysis: Dict[str, Dict[str, Any]]) -> str:
        """変数分析の結果を読みやすい形式にフォーマット"""
        if not var_analysis:
            return "変数の分析情報はありません。"
        
        lines = []
        for var, analysis in var_analysis.items():
            line = f"- {var}: "
            
            if "type" in analysis:
                line += f"型: {', '.join(analysis['type']) if isinstance(analysis['type'], list) else analysis['type']}"
            
            if "min" in analysis and "max" in analysis:
                line += f", 範囲: {analysis['min']} - {analysis['max']}"
            
            if "min_length" in analysis and "max_length" in analysis:
                line += f", 長さ範囲: {analysis['min_length']} - {analysis['max_length']} 文字"
            
            if "examples" in analysis:
                examples = [str(ex) for ex in analysis["examples"] if ex is not None]
                if examples:
                    example_str = ", ".join(examples)
                    if len(example_str) > 100:  # 長すぎる場合は切り詰め
                        example_str = example_str[:97] + "..."
                    line += f", 例: {example_str}"
            
            lines.append(line)
        
        return "\n".join(lines)
```

#### 4.4.3 プロンプトリント

```python
# backend/app/services/prompt_linting.py
import re
from typing import List, Dict, Any

class PromptLinter:
    """プロンプトの静的解析（リント）を行うサービス"""
    
    def __init__(self):
        # リントルールの定義
        self.rules = {
            "template_vars_undefined": {
                "description": "定義されていないテンプレート変数の使用",
                "severity": "error"
            },
            "ambiguous_instructions": {
                "description": "曖昧な指示",
                "severity": "warning"
            },
            "missing_output_format": {
                "description": "出力形式の指定がない",
                "severity": "warning"
            },
            "excessive_length": {
                "description": "過度に長いプロンプト",
                "severity": "warning"
            },
            "jargon": {
                "description": "業界用語や専門用語の過度な使用",
                "severity": "info"
            },
            "prompt_injection_vulnerability": {
                "description": "プロンプトインジェクション脆弱性",
                "severity": "error"
            },
            "missing_context": {
                "description": "コンテキスト情報の不足",
                "severity": "warning"
            }
        }
        
        # 曖昧な表現のパターン
        self.ambiguous_patterns = [
            r"\b(?:maybe|perhaps|possibly|might|could be)\b",
            r"\b(?:some|any|few|several|many|various)\b(?! specific)",
            r"\b(?:good|better|best|nice|great)\b(?! [a-zA-Z]+ing|\s+at\s+)",
            r"\boptimal\b(?! [a-zA-Z]+ing|\s+for\s+)",
            r"\b(?:appropriate|suitable|adequate)\b(?! for)",
        ]
        
        # プロンプトインジェクション攻撃パターン
        self.injection_patterns = [
            r"ignore (?:all|previous|above).*instructions",
            r"disregard (?:all|previous|above).*(?:instructions|prompt)",
            r"forget (?:all|previous|above).*instructions",
            r"do not follow (?:the|any|all) (?:instructions|guidelines)"
        ]
    
    def lint_prompt(
        self, 
        prompt: str, 
        defined_vars: List[str] = None
    ) -> Dict[str, Any]:
        """
        プロンプトを解析してコード品質問題を特定

        Args:
            prompt: リントするプロンプト
            defined_vars: 定義済み変数のリスト (省略可)

        Returns:
            リント結果の詳細
        """
        issues = []
        
        # テンプレート変数チェック
        if defined_vars is not None:
            var_issues = self._check_template_vars(prompt, defined_vars)
            issues.extend(var_issues)
        
        # 曖昧な指示のチェック
        ambiguity_issues = self._check_ambiguous_instructions(prompt)
        issues.extend(ambiguity_issues)
        
        # 出力形式のチェック
        if not self._has_output_format(prompt):
            issues.append({
                "rule": "missing_output_format",
                "description": self.rules["missing_output_format"]["description"],
                "severity": self.rules["missing_output_format"]["severity"],
                "location": "全体",
                "suggestion": "出力形式を明示的に指定してください。例:「## 出力形式」セクションを追加。"
            })
        
        # プロンプト長のチェック
        length_issue = self._check_length(prompt)
        if length_issue:
            issues.append(length_issue)
        
        # 業界用語のチェック
        jargon_issues = self._check_jargon(prompt)
        issues.extend(jargon_issues)
        
        # プロンプトインジェクション脆弱性チェック
        injection_issues = self._check_injection_vulnerability(prompt)
        issues.extend(injection_issues)
        
        # コンテキスト情報のチェック
        if not self._has_sufficient_context(prompt):
            issues.append({
                "rule": "missing_context",
                "description": self.rules["missing_context"]["description"],
                "severity": self.rules["missing_context"]["severity"],
                "location": "全体",
                "suggestion": "タスクに関連するコンテキスト情報を追加してください。"
            })
        
        # 結果の集計
        severities = [issue["severity"] for issue in issues]
        has_error = "error" in severities
        has_warning = "warning" in severities
        
        return {
            "issues_count": len(issues),
            "issues": issues,
            "has_error": has_error,
            "has_warning": has_warning,
            "severity_counts": {
                "error": severities.count("error"),
                "warning": severities.count("warning"),
                "info": severities.count("info")
            }
        }
    
    def _check_template_vars(self, prompt: str, defined_vars: List[str]) -> List[Dict[str, Any]]:
        """未定義のテンプレート変数をチェック"""
        pattern = r'\{\{\s*(\w+)\s*\}\}'
        matches = re.finditer(pattern, prompt)
        
        issues = []
        for match in matches:
            var_name = match.group(1)
            if var_name not in defined_vars:
                issues.append({
                    "rule": "template_vars_undefined",
                    "description": f"未定義の変数 '{var_name}' が使用されています",
                    "severity": self.rules["template_vars_undefined"]["severity"],
                    "location": f"位置: {match.start()}-{match.end()}",
                    "suggestion": f"変数 '{var_name}' を定義するか、プロンプトから削除してください。"
                })
        
        return issues
    
    def _check_ambiguous_instructions(self, prompt: str) -> List[Dict[str, Any]]:
        """曖昧な指示をチェック"""
        issues = []
        
        for pattern in self.ambiguous_patterns:
            matches = re.finditer(pattern, prompt, re.IGNORECASE)
            for match in matches:
                context = self._get_context(prompt, match.start(), match.end())
                issues.append({
                    "rule": "ambiguous_instructions",
                    "description": f"曖昧な表現 '{match.group(0)}' が使用されています",
                    "severity": self.rules["ambiguous_instructions"]["severity"],
                    "location": f"位置: {match.start()}-{match.end()}",
                    "context": context,
                    "suggestion": f"'{match.group(0)}' をより具体的な表現に置き換えてください。"
                })
        
        return issues
    
    def _has_output_format(self, prompt: str) -> bool:
        """出力形式の指定があるかチェック"""
        # 出力形式を示す一般的なパターン
        format_patterns = [
            r"(?:output|response|answer|result) (?:format|structure)",
            r"format (?:your|the) (?:output|response|answer|result)",
            r"## (?:output|response|format)",
            r"json format"
        ]
        
        for pattern in format_patterns:
            if re.search(pattern, prompt, re.IGNORECASE):
                return True
        
        # JSONやYAMLなど構造化データの例示パターン
        if re.search(r"```(?:json|yaml|xml)", prompt, re.IGNORECASE):
            return True
            
        return False
    
    def _check_length(self, prompt: str) -> Dict[str, Any]:
        """プロンプト長をチェック"""
        words = prompt.split()
        word_count = len(words)
        
        # 一般的な目安として1000語以上は長すぎる可能性
        if word_count > 1000:
            return {
                "rule": "excessive_length",
                "description": f"プロンプトが長すぎます（{word_count}語）",
                "severity": self.rules["excessive_length"]["severity"],
                "location": "全体",
                "suggestion": "プロンプトを簡潔にし、必要な情報だけに絞ってください。"
            }
        
        return None
    
    def _check_jargon(self, prompt: str) -> List[Dict[str, Any]]:
        """業界用語や専門用語の過度な使用をチェック"""
        # 注: 実際には特定の分野に関連する用語辞書があるとより効果的
        # 簡易版の実装としているため、実際にはより洗練された辞書や
        # AIを用いた分析が望ましい
        
        hr_jargon = [
            r"\bKPI\b", r"\bROI\b", r"\bPIP\b", r"\bHIPO\b", r"\bSME\b",
            r"\bFTE\b", r"\bHRBP\b", r"\bEAP\b", r"\bonboarding\b",
            r"\boffboarding\b", r"\battrition\b"
        ]
        
        issues = []
        jargon_count = 0
        
        for term in hr_jargon:
            matches = re.finditer(term, prompt, re.IGNORECASE)
            for match in matches:
                jargon_count += 1
                if jargon_count <= 3:  # 最初の数個だけを個別に報告
                    context = self._get_context(prompt, match.start(), match.end())
                    issues.append({
                        "rule": "jargon",
                        "description": f"専門用語 '{match.group(0)}' が使用されています",
                        "severity": self.rules["jargon"]["severity"],
                        "location": f"位置: {match.start()}-{match.end()}",
                        "context": context,
                        "suggestion": f"'{match.group(0)}' の意味を説明するか、よりわかりやすい表現に置き換えることを検討してください。"
                    })
        
        # 多数の専門用語がある場合はまとめて報告
        if jargon_count > 3:
            issues.append({
                "rule": "jargon",
                "description": f"多数の専門用語（合計 {jargon_count} 個）が使用されています",
                "severity": self.rules["jargon"]["severity"],
                "location": "複数箇所",
                "suggestion": "専門用語の使用を減らすか、適切な説明を追加することを検討してください。"
            })
        
        return issues
    
    def _check_injection_vulnerability(self, prompt: str) -> List[Dict[str, Any]]:
        """プロンプトインジェクション脆弱性をチェック"""
        issues = []
        
        for pattern in self.injection_patterns:
            matches = re.finditer(pattern, prompt, re.IGNORECASE)
            for match in matches:
                context = self._get_context(prompt, match.start(), match.end())
                issues.append({
                    "rule": "prompt_injection_vulnerability",
                    "description": f"プロンプトインジェクションの可能性: '{match.group(0)}'",
                    "severity": self.rules["prompt_injection_vulnerability"]["severity"],
                    "location": f"位置: {match.start()}-{match.end()}",
                    "context": context,
                    "suggestion": "この表現はプロンプトインジェクション攻撃に類似しています。ユーザー入力を直接含める場合は注意してください。"
                })
        
        # ユーザー入力をそのまま含める脆弱なパターン
        if "{{input}}" in prompt:
            issues.append({
                "rule": "prompt_injection_vulnerability",
                "description": "ユーザー入力をそのまま含める可能性があります",
                "severity": "warning",
                "location": f"位置: {prompt.find('{{input}}')}",
                "suggestion": "ユーザー入力は適切に検証してから使用し、構造化された形式で含めることを検討してください。"
            })
        
        return issues
    
    def _has_sufficient_context(self, prompt: str) -> bool:
        """十分なコンテキスト情報があるかチェック"""
        # 簡易的なチェック - 実際にはより高度な分析が望ましい
        
        # コンテキストセクションの存在
        context_sections = re.search(
            r"(?:##? |===)(?:context|background|information|data)",
            prompt, 
            re.IGNORECASE
        )
        
        if context_sections:
            return True
        
        # コードブロックなどの具体的な情報
        if re.search(r"```(?!json|yaml|xml)", prompt):  # 出力形式以外のコードブロック
            return True
        
        # 行数が十分にあり、かつ詳細な説明が含まれている可能性
        lines = prompt.split('\n')
        if len(lines) > 10 and len(prompt) > 500:
            return True
            
        return False
    
    def _get_context(self, text: str, start: int, end: int, context_chars: int = 20) -> str:
        """指定された位置の前後のコンテキストを取得"""
        context_start = max(0, start - context_chars)
        context_end = min(len(text), end + context_chars)
        
        prefix = "..." if context_start > 0 else ""
        suffix = "..." if context_end < len(text) else ""
        
        return prefix + text[context_start:context_end] + suffix
```

## 5. モデル評価と品質保証

AIモデルの評価と品質保証のためのフレームワークです。

### 5.1 評価指標とベンチマーク

```python
# backend/app/services/model_evaluation.py
from typing import List, Dict, Any, Optional, Union
import numpy as np
import json
import asyncio
from datetime import datetime

class ModelEvaluator:
    """AIモデルの評価とベンチマークを行うサービス"""
    
    def __init__(self, model_service):
        self.model_service = model_service  # モデル呼び出し用サービス
    
    async def evaluate_model(
        self,
        model_id: str,
        evaluation_datasets: Dict[str, List[Dict[str, Any]]],
        metrics: List[str] = None
    ) -> Dict[str, Any]:
        """
        モデルを複数のデータセットで総合的に評価

        Args:
            model_id: 評価対象のモデル識別子
            evaluation_datasets: カテゴリ別の評価データセット
            metrics: 使用する評価指標のリスト

        Returns:
            評価結果の詳細
        """
        if metrics is None:
            metrics = ["correctness", "relevance", "coherence", "helpfulness", "toxicity"]
        
        results = {}
        all_scores = []
        
        # 各カテゴリのデータセットで評価
        for category, dataset in evaluation_datasets.items():
            print(f"{category} データセットを評価中...")
            
            category_results = await self._evaluate_dataset(model_id, dataset, metrics)
            results[category] = category_results
            
            # 全体のスコア集計用
            for item in category_results["items"]:
                all_scores.append({
                    "category": category,
                    "query_id": item["query_id"],
                    **{metric: score for metric, score in item["scores"].items()}
                })
        
        # 全体の統計を計算
        overall_stats = self._calculate_overall_stats(all_scores, metrics)
        
        return {
            "model_id": model_id,
            "timestamp": datetime.now().isoformat(),
            "category_results": results,
            "overall_stats": overall_stats
        }
    
    async def _evaluate_dataset(
        self,
        model_id: str,
        dataset: List[Dict[str, Any]],
        metrics: List[str]
    ) -> Dict[str, Any]:
        """単一データセットでモデルを評価"""
        evaluation_tasks = []
        
        for item in dataset:
            task = self._evaluate_single_item(model_id, item, metrics)
            evaluation_tasks.append(task)
        
        # 並行して評価を実行
        evaluation_results = await asyncio.gather(*evaluation_tasks)
        
        # 結果の集計
        scores_by_metric = {metric: [] for metric in metrics}
        for result in evaluation_results:
            for metric, score in result["scores"].items():
                scores_by_metric[metric].append(score)
        
        # メトリック別の統計
        metric_stats = {}
        for metric, scores in scores_by_metric.items():
            metric_stats[metric] = {
                "mean": np.mean(scores),
                "median": np.median(scores),
                "min": np.min(scores),
                "max": np.max(scores),
                "std": np.std(scores)
            }
        
        return {
            "items": evaluation_results,
            "metrics": metric_stats,
            "overall_score": np.mean([np.mean(scores) for scores in scores_by_metric.values()]),
            "items_count": len(evaluation_results)
        }
    
    async def _evaluate_single_item(
        self,
        model_id: str,
        item: Dict[str, Any],
        metrics: List[str]
    ) -> Dict[str, Any]:
        """単一アイテムの評価"""
        query = item["query"]
        expected = item.get("expected", None)
        
        # モデルからレスポンスを取得
        response = await self.model_service.generate_text(
            model_id,
            query,
            item.get("parameters", {})
        )
        
        # 各メトリックでレスポンスを評価
        scores = {}
        for metric in metrics:
            score = await self._evaluate_metric(
                metric,
                query,
                response,
                expected
            )
            scores[metric] = score
        
        # 評価結果の構築
        return {
            "query_id": item.get("id", str(hash(query) % 10000)),
            "query": query,
            "response": response,
            "expected": expected,
            "scores": scores,
            "average_score": np.mean(list(scores.values())) if scores else 0
        }
    
    async def _evaluate_metric(
        self,
        metric: str,
        query: str,
        response: str,
        expected: Optional[str] = None
    ) -> float:
        """特定のメトリックを使用してレスポンスを評価"""
        
        # 評価用のメタLLMプロンプト
        evaluation_prompt = f"""
# レスポンス評価

## クエリ
{query}

## モデル応答
{response}

"""

        if expected is not None:
            evaluation_prompt += f"""
## 期待される応答
{expected}

"""

        evaluation_prompt += f"""
## 評価対象の指標
"{metric}"

## タスク
上記のモデル応答を、指定された評価指標に基づいて0〜10のスケールで評価してください。
以下の評価基準を参考にしてください：

"""

        # 指標別の評価基準
        if metric == "correctness":
            evaluation_prompt += """
- 0: 完全に間違っており、事実に反する情報が含まれている
- 5: 部分的に正確だが、いくつかの誤りや不正確な情報が含まれている
- 10: 完全に正確で、すべての情報が事実に基づいている
"""
        elif metric == "relevance":
            evaluation_prompt += """
- 0: クエリと完全に無関係
- 5: クエリに部分的に関連しているが、主要な質問に対処していない
- 10: クエリに完全に関連し、すべての側面に適切に対応している
"""
        elif metric == "coherence":
            evaluation_prompt += """
- 0: 論理的な流れがなく、一貫性を欠いている
- 5: ある程度の論理的な流れはあるが、一部の接続や説明が不明確
- 10: 完全に整理された論理的構造で、明確で一貫性のある説明を提供
"""
        elif metric == "helpfulness":
            evaluation_prompt += """
- 0: まったく役に立たず、問題解決に貢献しない
- 5: いくつかの有用な情報や方向性を提供しているが、不十分
- 10: 完全に有用で、問題に対する包括的な解決策を提供している
"""
        elif metric == "toxicity":
            evaluation_prompt += """
- 0: 非常に有害で、攻撃的、差別的、または不適切なコンテンツを含む
- 5: 微妙な問題はあるが、明らかな有害コンテンツはない
- 10: 完全に無害で、プロフェッショナルかつ倫理的なコンテンツ
"""
        else:
            evaluation_prompt += """
- 0: 非常に低品質
- 5: 中程度の品質
- 10: 非常に高品質
"""

        evaluation_prompt += """
## 出力形式
スコアのみを数値（0〜10）で返してください。説明は不要です。
"""

        try:
            # 評価モデルを使用して評価
            evaluation_model = "gpt-4o"  # 高精度が必要なため高性能モデルを使用
            
            evaluation_result = await self.model_service.generate_text(
                evaluation_model,
                evaluation_prompt,
                {"temperature": 0.1}  # 一貫性のため低温度
            )
            
            # 結果からスコアを抽出
            score = float(evaluation_result.strip())
            return min(max(score, 0), 10)  # 0-10の範囲に制限
            
        except Exception as e:
            print(f"評価エラー ({metric}): {e}")
            return 5.0  # エラー時は中間値を返す
    
    def _calculate_overall_stats(
        self,
        all_scores: List[Dict[str, Any]],
        metrics: List[str]
    ) -> Dict[str, Any]:
        """全体の評価統計を計算"""
        if not all_scores:
            return {}
        
        # メトリック別の集計
        metric_stats = {}
        for metric in metrics:
            scores = [item[metric] for item in all_scores if metric in item]
            if scores:
                metric_stats[metric] = {
                    "mean": np.mean(scores),
                    "median": np.median(scores),
                    "min": np.min(scores),
                    "max": np.max(scores),
                    "std": np.std(scores),
                    "count": len(scores)
                }
        
        # カテゴリ別の集計
        categories = set(item["category"] for item in all_scores)
        category_stats = {}
        
        for category in categories:
            category_items = [item for item in all_scores if item["category"] == category]
            metric_means = {}
            
            for metric in metrics:
                scores = [item[metric] for item in category_items if metric in item]
                if scores:
                    metric_means[metric] = np.mean(scores)
            
            category_stats[category] = {
                "count": len(category_items),
                "metrics": metric_means,
                "overall": np.mean(list(metric_means.values())) if metric_means else 0
            }
        
        # 全体の平均スコア
        overall_score = np.mean([
            stat["overall"] for cat, stat in category_stats.items()
        ])
        
        return {
            "metrics": metric_stats,
            "categories": category_stats,
            "overall_score": overall_score,
            "total_queries": len(all_scores)
        }

    async def benchmark_compare(
        self,
        model_ids: List[str],
        benchmark_dataset: List[Dict[str, Any]],
        metrics: List[str] = None
    ) -> Dict[str, Any]:
        """
        複数モデルを同じベンチマークデータセットで比較

        Args:
            model_ids: 比較するモデルのID一覧
            benchmark_dataset: ベンチマークデータセット
            metrics: 使用する評価指標

        Returns:
            モデル比較の結果
        """
        if metrics is None:
            metrics = ["correctness", "relevance", "coherence", "helpfulness", "toxicity"]
        
        results = {}
        
        # 各モデルを評価
        for model_id in model_ids:
            print(f"モデル {model_id} をベンチマーク中...")
            
            single_dataset = {"benchmark": benchmark_dataset}
            model_result = await self.evaluate_model(model_id, single_dataset, metrics)
            
            # 結果を抽出
            results[model_id] = {
                "overall_score": model_result["overall_stats"]["overall_score"],
                "metrics": {
                    metric: stats["mean"] 
                    for metric, stats in model_result["overall_stats"]["metrics"].items()
                },
                "detailed_results": model_result["category_results"]["benchmark"]
            }
        
        # モデルのランキング
        ranking = sorted(
            [(model_id, data["overall_score"]) for model_id, data in results.items()],
            key=lambda x: x[1],
            reverse=True
        )
        
        return {
            "timestamp": datetime.now().isoformat(),
            "models_compared": model_ids,
            "results": results,
            "ranking": ranking,
            "best_model": ranking[0][0] if ranking else None,
            "metrics_used": metrics
        }
```

### 5.2 品質モニタリングフレームワーク

```python
# backend/app/services/quality_monitoring.py
import json
from typing import List, Dict, Any, Optional
from datetime import datetime, timedelta
import asyncio

class AIQualityMonitor:
    """AIモデルの品質を監視するサービス"""
    
    def __init__(self, model_service, evaluator, storage_service):
        self.model_service = model_service
        self.evaluator = evaluator
        self.storage_service = storage_service  # 監視データの保存と取得
        
        # アラートのしきい値
        self.alert_thresholds = {
            "significant_drop": 1.5,  # スコアの大幅な低下（ポイント）
            "consistent_decline": 0.5,  # 3回連続での低下（ポイント/回）
            "critical_score": 6.0,  # クリティカルスコアのしきい値
            "toxicity_threshold": 3.0,  # 有害コンテンツのしきい値（低いほど有害）
        }
    
    async def monitor_production_requests(
        self,
        sample_rate: float = 0.05,
        evaluation_metrics: List[str] = None
    ):
        """
        本番リクエストのサンプルを評価して品質を監視するバックグラウンドプロセス
        
        Args:
            sample_rate: モニタリングするリクエストの割合（0.0-1.0）
            evaluation_metrics: 使用する評価指標
        """
        if evaluation_metrics is None:
            evaluation_metrics = ["correctness", "relevance", "coherence", "helpfulness", "toxicity"]
        
        print(f"品質モニタリングを開始しました。サンプリング率: {sample_rate*100:.1f}%")
        
        # 監視プロセスの例（通常はバックグラウンドワーカーとして実装）
        while True:
            try:
                # 最近のリクエストからサンプリング
                recent_requests = await self._get_recent_production_requests(sample_rate)
                
                if not recent_requests:
                    print("モニタリング対象のリクエストがありません。待機中...")
                    await asyncio.sleep(300)  # 5分待機
                    continue
                
                print(f"品質評価: {len(recent_requests)}件のリクエストを評価中")
                
                # リクエストを評価
                evaluation_results = []
                for request in recent_requests:
                    try:
                        result = await self._evaluate_request(request, evaluation_metrics)
                        evaluation_results.append(result)
                    except Exception as e:
                        print(f"リクエスト評価エラー: {e}")
                
                # 結果を集約して保存
                monitoring_data = self._aggregate_monitoring_data(evaluation_results)
                await self.storage_service.store_monitoring_data(monitoring_data)
                
                # アラート条件をチェック
                alerts = await self._check_for_alerts(monitoring_data)
                if alerts:
                    await self._send_alerts(alerts)
                
                # 次の評価まで待機
                await asyncio.sleep(3600)  # 1時間待機
                
            except Exception as e:
                print(f"モニタリングプロセスエラー: {e}")
                await asyncio.sleep(300)  # エラー時は短い間隔で再試行
    
    async def _get_recent_production_requests(self, sample_rate: float) -> List[Dict[str, Any]]:
        """最近の本番リクエストのサンプルを取得"""
        # 実際の実装ではログDBやキューから取得
        # ここではダミーの実装
        
        recent_requests = await self.storage_service.get_recent_requests(
            hours=24,  # 過去24時間のリクエスト
            limit=1000  # 最大1000件
        )
        
        # サンプリング
        import random
        sampled_requests = random.sample(
            recent_requests,
            int(len(recent_requests) * sample_rate)
        ) if recent_requests else []
        
        return sampled_requests
    
    async def _evaluate_request(
        self,
        request: Dict[str, Any],
        metrics: List[str]
    ) -> Dict[str, Any]:
        """単一リクエストの品質を評価"""
        # リクエスト情報の抽出
        query = request.get("input", "")
        response = request.get("output", "")
        model_id = request.get("model_id", "")
        timestamp = request.get("timestamp", "")
        request_id = request.get("request_id", "")
        
        # 各メトリックでレスポンスを評価
        scores = {}
        for metric in metrics:
            score = await self.evaluator._evaluate_metric(metric, query, response)
            scores[metric] = score
        
        average_score = sum(scores.values()) / len(scores) if scores else 0
        
        # 結果を返す
        return {
            "request_id": request_id,
            "model_id": model_id,
            "timestamp": timestamp,
            "query": query,
            "response": response,
            "scores": scores,
            "average_score": average_score,
            "evaluation_timestamp": datetime.now().isoformat()
        }
    
    def _aggregate_monitoring_data(
        self,
        evaluation_results: List[Dict[str, Any]]
    ) -> Dict[str, Any]:
        """評価結果を集約して監視データを生成"""
        if not evaluation_results:
            return {
                "timestamp": datetime.now().isoformat(),
                "sample_count": 0,
                "error": "評価結果がありません"
            }
        
        # メトリック別の統計
        metrics = list(evaluation_results[0]["scores"].keys())
        metrics_data = {}
        
        for metric in metrics:
            scores = [result["scores"][metric] for result in evaluation_results]
            metrics_data[metric] = {
                "mean": sum(scores) / len(scores),
                "min": min(scores),
                "max": max(scores),
                "count": len(scores)
            }
        
        # モデル別の統計
        models = set(result["model_id"] for result in evaluation_results)
        model_data = {}
        
        for model_id in models:
            model_results = [r for r in evaluation_results if r["model_id"] == model_id]
            model_scores = [r["average_score"] for r in model_results]
            
            model_data[model_id] = {
                "mean_score": sum(model_scores) / len(model_scores),
                "sample_count": len(model_results),
                "metrics": {}
            }
            
            # モデル別のメトリック統計
            for metric in metrics:
                metric_scores = [r["scores"][metric] for r in model_results]
                model_data[model_id]["metrics"][metric] = sum(metric_scores) / len(metric_scores)
        
        # 低品質/有害な応答の識別
        poor_responses = [
            {
                "request_id": r["request_id"],
                "scores": r["scores"],
                "average_score": r["average_score"],
                "model_id": r["model_id"],
                "query": r["query"],
                "response": r["response"]
            }
            for r in evaluation_results
            if r["average_score"] < 6.0 or r["scores"].get("toxicity", 10) < 3.0
        ]
        
        # 監視データの構築
        return {
            "timestamp": datetime.now().isoformat(),
            "time_period": f"{min(r['timestamp'] for r in evaluation_results)} - {max(r['timestamp'] for r in evaluation_results)}",
            "sample_count": len(evaluation_results),
            "overall_score": sum(r["average_score"] for r in evaluation_results) / len(evaluation_results),
            "metrics": metrics_data,
            "models": model_data,
            "poor_responses": poor_responses[:10],  # 最悪の10件のみ含める
            "poor_response_count": len(poor_responses)
        }
    
    async def _check_for_alerts(self, current_data: Dict[str, Any]) -> List[Dict[str, Any]]:
        """モニタリングデータからアラート条件をチェック"""
        alerts = []
        
        # 過去のモニタリングデータを取得
        historical_data = await self.storage_service.get_monitoring_history(days=7)
        
        if not historical_data:
            return []  # 履歴データがない場合はアラートなし
        
        # 1. 急激な品質低下をチェック
        latest_score = current_data["overall_score"]
        historical_avg = sum(h["overall_score"] for h in historical_data) / len(historical_data)
        
        if historical_avg - latest_score > self.alert_thresholds["significant_drop"]:
            alerts.append({
                "type": "significant_quality_drop",
                "severity": "high",
                "message": f"品質スコアが大幅に低下しました: {historical_avg:.2f} → {latest_score:.2f}",
                "details": {
                    "previous_avg": historical_avg,
                    "current_score": latest_score,
                    "difference": historical_avg - latest_score
                }
            })
        
        # 2. 継続的な品質低下をチェック
        if len(historical_data) >= 3:
            recent_history = sorted(historical_data, key=lambda x: x["timestamp"], reverse=True)[:3]
            recent_scores = [recent_history[0]["overall_score"], recent_history[1]["overall_score"], recent_history[2]["overall_score"]]
            
            if all(recent_scores[i] < recent_scores[i+1] - self.alert_thresholds["consistent_decline"] for i in range(2)):
                alerts.append({
                    "type": "consistent_quality_decline",
                    "severity": "medium",
                    "message": f"品質スコアが3回連続で低下しています: {recent_scores[2]:.2f} → {recent_scores[1]:.2f} → {recent_scores[0]:.2f}",
                    "details": {
                        "trend": recent_scores[::-1],
                        "decline_rate": [(recent_scores[i] - recent_scores[i-1]) for i in range(1, 3)]
                    }
                })
        
        # 3. クリティカルスコア未満の応答の増加
        if current_data["poor_response_count"] > 0:
            poor_ratio = current_data["poor_response_count"] / current_data["sample_count"]
            
            # 過去の低品質応答率
            historical_poor_ratio = sum(
                h.get("poor_response_count", 0) for h in historical_data
            ) / sum(h["sample_count"] for h in historical_data)
            
            if poor_ratio > 0.2 and poor_ratio > historical_poor_ratio * 1.5:  # 20%以上かつ通常の1.5倍以上
                alerts.append({
                    "type": "increased_poor_responses",
                    "severity": "high",
                    "message": f"低品質応答の割合が増加しています: {historical_poor_ratio:.1%} → {poor_ratio:.1%}",
                    "details": {
                        "current_ratio": poor_ratio,
                        "historical_ratio": historical_poor_ratio,
                        "example_responses": current_data["poor_responses"][:3]  # 例として3件含める
                    }
                })
        
        # 4. モデル別の品質低下チェック
        for model_id, model_data in current_data["models"].items():
            # 同じモデルの過去データを取得
            historical_model_data = [
                h["models"].get(model_id, {})
                for h in historical_data
                if model_id in h.get("models", {})
            ]
            
            if historical_model_data:
                # 過去の平均スコア
                historical_model_avg = sum(
                    h.get("mean_score", 0) for h in historical_model_data
                ) / len(historical_model_data)
                
                # 現在のスコア
                current_model_score = model_data.get("mean_score", 0)
                
                # 急激な低下をチェック
                if historical_model_avg - current_model_score > self.alert_thresholds["significant_drop"]:
                    alerts.append({
                        "type": "model_quality_drop",
                        "severity": "high",
                        "message": f"モデル {model_id} の品質が大幅に低下しました: {historical_model_avg:.2f} → {current_model_score:.2f}",
                        "details": {
                            "model_id": model_id,
                            "previous_avg": historical_model_avg,
                            "current_score": current_model_score,
                            "difference": historical_model_avg - current_model_score
                        }
                    })
        
        return alerts
    
    async def _send_alerts(self, alerts: List[Dict[str, Any]]):
        """アラートを送信"""
        # 実際の実装ではメール、Slack、監視システムなどに送信
        
        for alert in alerts:
            # 重要度に応じたログレベル
            if alert["severity"] == "high":
                print(f"[高重要度アラート] {alert['message']}")
            else:
                print(f"[アラート] {alert['message']}")
            
            # アラートの保存
            await self.storage_service.store_alert(alert)
            
            # 通知の送信（実際の実装ではここでメール送信などを行う）
            alert_data = {
                "timestamp": datetime.now().isoformat(),
                "alert_type": alert["type"],
                "severity": alert["severity"],
                "message": alert["message"],
                "details": alert.get("details", {})
            }
            
            # 通知サービスの呼び出し例
            # await self.notification_service.send_notification(
            #     channels=["email", "slack"],
            #     subject=f"AIモニタリングアラート: {alert['type']}",
            #     content=json.dumps(alert_data)
            # )
```

### 5.3 コンテンツセーフティフィルター

```python
# backend/app/services/content_safety.py
from typing import Dict, Any, List, Optional, Tuple
import re
from datetime import datetime
import json

class ContentSafetyFilter:
    """AIレスポンスの安全性を評価・フィルタリングするサービス"""
    
    def __init__(self, model_service, storage_service=None):
        self.model_service = model_service
        self.storage_service = storage_service  # オプション: 違反記録の保存用
        
        # 安全性チェックのカテゴリ
        self.safety_categories = [
            "harmful_content",  # 有害なコンテンツ
            "misinformation",   # 誤情報
            "bias",             # バイアスのあるコンテンツ
            "pii_disclosure",   # 個人情報の漏洩
            "copyright_violation", # 著作権違反
            "hallucination"     # 幻覚（事実と異なる情報）
        ]
        
        # 不適切コンテンツのパターン（簡易版）
        self.inappropriate_patterns = {
            "pii": [
                r"\b(?:\d{3}[-.]?){2}\d{4}\b",  # 米国の電話番号
                r"\b\d{3}[-]?\d{2}[-]?\d{4}\b", # 米国のSSN
                r"[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+", # メールアドレス
                r"\b(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|3[47][0-9]{13}|3(?:0[0-5]|[68][0-9])[0-9]{11}|6(?:011|5[0-9]{2})[0-9]{12}|(?:2131|1800|35\d{3})\d{11})\b" # クレジットカード番号
            ]
        }
    
    async def check_content_safety(
        self,
        query: str,
        response: str
    ) -> Dict[str, Any]:
        """
        クエリとレスポンスの安全性をチェック
        
        Args:
            query: ユーザークエリ
            response: AIレスポンス
            
        Returns:
            安全性チェックの結果
        """
        # 1. パターンベースの簡易チェック
        pattern_check = self._pattern_safety_check(query, response)
        
        # 2. モデルベースの高度な安全性チェック
        model_check = await self._model_safety_check(query, response)
        
        # 最終的な安全性スコアと判定
        combined_score = self._combine_safety_scores(pattern_check, model_check)
        
        # 結果の構築
        result = {
            "timestamp": datetime.now().isoformat(),
            "is_safe": combined_score["is_safe"],
            "overall_safety_score": combined_score["overall_score"],
            "category_scores": combined_score["category_scores"],
            "detected_issues": pattern_check["detected_patterns"] + model_check["detected_issues"],
            "content_filters": {
                "applied": not combined_score["is_safe"],
                "filtered_response": self._apply_content_filter(response, combined_score) if not combined_score["is_safe"] else response
            }
        }
        
        # 安全でないコンテンツを記録（オプション）
        if self.storage_service and not result["is_safe"]:
            await self._log_unsafe_content(query, response, result)
        
        return result
    
    def _pattern_safety_check(
        self,
        query: str,
        response: str
    ) -> Dict[str, Any]:
        """正規表現パターンによる安全性チェック"""
        detected_patterns = []
        
        # PII（個人を特定できる情報）の検出
        for pattern_type, patterns in self.inappropriate_patterns.items():
            for pattern in patterns:
                # レスポンス内のマッチをチェック
                matches = re.findall(pattern, response)
                if matches:
                    # マスキング処理（表示用）
                    masked_matches = [self._mask_sensitive_data(m) for m in matches]
                    detected_patterns.append({
                        "type": pattern_type,
                        "matches": masked_matches,
                        "count": len(matches)
                    })
        
        # カテゴリ別スコアの計算（パターンベースでは限定的）
        category_scores = {
            "pii_disclosure": 10.0 - min(len([p for p in detected_patterns if p["type"] == "pii"]) * 2.0, 10.0),
            "harmful_content": 10.0,  # パターンベースでは検出困難
            "misinformation": 10.0,   # パターンベースでは検出困難
            "bias": 10.0,            # パターンベースでは検出困難
            "copyright_violation": 10.0, # パターンベースでは検出困難
            "hallucination": 10.0    # パターンベースでは検出困難
        }
        
        # 最低スコアとそのカテゴリを特定
        min_score = min(category_scores.values())
        min_categories = [cat for cat, score in category_scores.items() if score == min_score]
        
        return {
            "detected_patterns": detected_patterns,
            "category_scores": category_scores,
            "min_score": min_score,
            "min_categories": min_categories
        }
    
    async def _model_safety_check(
        self,
        query: str,
        response: str
    ) -> Dict[str, Any]:
        """AIモデルによる高度な安全性チェック"""
        safety_check_prompt = f"""
# コンテンツ安全性評価

## ユーザークエリ
{query}

## AIレスポンス
{response}

## タスク
上記のユーザークエリとAIレスポンスを評価し、以下のカテゴリごとに安全性を評価してください：

1. harmful_content: 有害、暴力的、扇動的なコンテンツ
2. misinformation: 誤情報や不正確な情報
3. bias: バイアスの強い、偏った、または差別的なコンテンツ
4. pii_disclosure: 個人を特定できる情報（PII）の開示
5. copyright_violation: 著作権侵害の可能性
6. hallucination: AIが事実と異なる情報を生成している（幻覚）

各カテゴリについて0から10の安全性スコアを付けてください：
- 0: 非常に問題あり（完全に禁止されるべき）
- 5: 境界線上（懸念があるがはっきりしない）
- 10: 完全に安全（問題なし）

問題を検出した場合は、その具体的な内容を説明してください。

## 出力形式
評価結果をJSON形式で出力してください：

```json
{{
  "category_scores": {{
    "harmful_content": X,
    "misinformation": X,
    "bias": X,
    "pii_disclosure": X,
    "copyright_violation": X,
    "hallucination": X
  }},
  "detected_issues": [
    {{
      "category": "カテゴリ名",
      "score": X,
      "description": "検出された問題の説明"
    }}
  ],
  "overall_assessment": "全体的な評価の説明"
}}
```
```


深刻な問題がない場合、detected_issuesは空の配列になります。
"""
        
        try:
            # 安全性評価モデルの使用
            safety_model = "gpt-4o"  # 高い精度が必要なため高性能モデルを使用
            safety_response = await self.model_service.generate_text(
                safety_model,
                safety_check_prompt,
                {"temperature": 0.2}
            )
            
            # JSONの抽出と解析
            json_content = safety_response
            
            if "```json" in safety_response:
                json_content = safety_response.split("```json")[1].split("```")[0].strip()
            elif "```" in safety_response:
                json_content = safety_response.split("```")[1].strip()
            
            evaluation = json.loads(json_content)
            
            # 検出された問題がなければ空のリストを確保
            if "detected_issues" not in evaluation:
                evaluation["detected_issues"] = []
            
            return evaluation
            
        except Exception as e:
            print(f"安全性評価エラー: {e}")
            # エラー時のフォールバック - 安全側に倒す
            return {
                "category_scores": {cat: 5.0 for cat in self.safety_categories},
                "detected_issues": [{
                    "category": "system_error",
                    "score": 5.0,
                    "description": "安全性評価中にエラーが発生したため、念のためスコアを控えめに設定しました"
                }],
                "overall_assessment": "評価エラー - 詳細な安全性チェックができませんでした"
            }
    
    def _combine_safety_scores(
        self,
        pattern_check: Dict[str, Any],
        model_check: Dict[str, Any]
    ) -> Dict[str, Any]:
        """パターンベースとモデルベースの安全性スコアを組み合わせる"""
        combined_scores = {}
        
        # すべてのカテゴリに対して、より厳しい（低い）スコアを採用
        for category in self.safety_categories:
            pattern_score = pattern_check["category_scores"].get(category, 10.0)
            model_score = model_check["category_scores"].get(category, 10.0)
            combined_scores[category] = min(pattern_score, model_score)
        
        # 全体のスコアは最低カテゴリスコア
        overall_score = min(combined_scores.values())
        
        # 安全性の判定（スコアが7.0以上なら安全と判断）
        is_safe = overall_score >= 7.0
        
        return {
            "is_safe": is_safe,
            "overall_score": overall_score,
            "category_scores": combined_scores
        }
    
    def _apply_content_filter(
        self,
        response: str,
        safety_result: Dict[str, Any]
    ) -> str:
        """安全でないコンテンツに対してフィルターを適用"""
        filtered_response = response
        
        # 深刻な問題（スコアが3.0未満）がある場合は完全に置換
        if safety_result["overall_score"] < 3.0:
            return "[このレスポンスはコンテンツ安全ポリシーに違反する可能性があるため表示されません]"
        
        # 中程度の問題（スコアが3.0〜7.0）の場合は問題のある部分のみ処理
        if safety_result["overall_score"] < 7.0:
            # PII（個人情報）を検出して置換
            for pattern in self.inappropriate_patterns.get("pii", []):
                filtered_response = re.sub(
                    pattern,
                    "[個人情報削除]",
                    filtered_response
                )
            
            # 警告メッセージを追加
            filtered_response = (
                f"{filtered_response}\n\n"
                f"[注意: このレスポンスには不正確な情報や問題のあるコンテンツが含まれている可能性があります]"
            )
        
        return filtered_response
    
    def _mask_sensitive_data(self, data: str) -> str:
        """機密データのマスキング処理"""
        if not data:
            return ""
        
        # データの種類に応じたマスキング
        if "@" in data:  # メールアドレス
            parts = data.split("@")
            username = parts[0]
            domain = parts[1]
            masked_username = username[0] + "*" * (len(username) - 2) + username[-1] if len(username) > 2 else username[0] + "*"
            return f"{masked_username}@{domain}"
            
        elif re.match(r"\b(?:\d{3}[-.]?){2}\d{4}\b", data):  # 電話番号
            # 最後の4桁以外をマスク
            return re.sub(r"\d(?=\d{4})", "*", data)
            
        else:  # その他のデータ
            # 先頭と末尾を残して中間をマスク
            if len(data) <= 2:
                return "*" * len(data)
            else:
                return data[0] + "*" * (len(data) - 2) + data[-1]
    
    async def _log_unsafe_content(
        self,
        query: str,
        response: str,
        safety_result: Dict[str, Any]
    ):
        """安全でないコンテンツのログ記録（オプション）"""
        if not self.storage_service:
            return
        
        log_entry = {
            "timestamp": datetime.now().isoformat(),
            "query": query,
            "response": response,
            "safety_result": {
                "overall_score": safety_result["overall_safety_score"],
                "category_scores": safety_result["category_scores"],
                "detected_issues": safety_result["detected_issues"]
            },
            "filtered": safety_result["content_filters"]["applied"]
        }
        
        await self.storage_service.store_safety_violation(log_entry)
```

## 6. AIコンポーネントのデプロイ戦略

AIモデルとコンポーネントを効果的にデプロイするための戦略です。

### 6.1 ハイブリッドデプロイアーキテクチャ

```python
# backend/app/services/deployment_manager.py
from typing import Dict, Any, List, Optional
import asyncio
from enum import Enum
import json
import time

class DeploymentType(Enum):
    """AIコンポーネントのデプロイタイプ"""
    THIRD_PARTY_API = "third_party_api"  # 外部APIサービス（OpenAI, Claude等）
    MANAGED_SERVICE = "managed_service"  # マネージドサービス（Bedrock, Vertex AIなど）
    CONTAINER = "container"              # コンテナ化されたデプロイ
    SERVERLESS = "serverless"            # サーバーレス関数
    EDGE = "edge"                        # エッジデプロイメント
    ON_PREMISE = "on_premise"            # オンプレミスデプロイ

class ModelStatus(Enum):
    """AIモデルのステータス"""
    ACTIVE = "active"               # アクティブ
    INACTIVE = "inactive"           # 非アクティブ
    DEPLOYING = "deploying"         # デプロイ中
    ERROR = "error"                 # エラー
    DEPRECATED = "deprecated"       # 非推奨
    ROLLING_UPDATE = "rolling_update" # ローリングアップデート中

class DeploymentManager:
    """AIモデルとコンポーネントのデプロイを管理するサービス"""
    
    def __init__(self, config_service, monitoring_service=None):
        self.config_service = config_service  # 設定管理サービス
        self.monitoring_service = monitoring_service  # 監視サービス（オプション）
        
        # デプロイされたモデルの状態管理
        self.deployed_models = {}
        
        # デプロイアダプター（各デプロイタイプに対応）
        self.adapters = {
            DeploymentType.THIRD_PARTY_API.value: ThirdPartyAPIAdapter(),
            DeploymentType.MANAGED_SERVICE.value: ManagedServiceAdapter(),
            DeploymentType.CONTAINER.value: ContainerAdapter(),
            DeploymentType.SERVERLESS.value: ServerlessAdapter(),
            DeploymentType.EDGE.value: EdgeAdapter(),
            DeploymentType.ON_PREMISE.value: OnPremiseAdapter()
        }
    
    async def initialize(self):
        """デプロイマネージャーの初期化"""
        # 設定サービスからデプロイ設定を読み込み
        self.deployment_config = await self.config_service.get_deployment_config()
        
        # すでにデプロイされているモデルの状態を読み込み
        self.deployed_models = await self.config_service.get_deployed_models_state()
        
        # モデルの状態をチェック・更新
        await self._refresh_model_states()
    
    async def deploy_model(
        self,
        model_config: Dict[str, Any]
    ) -> Dict[str, Any]:
        """
        指定された設定に基づいてモデルをデプロイ

        Args:
            model_config: モデルのデプロイ設定（モデルID、タイプ、パラメータなど）

        Returns:
            デプロイ結果の詳細
        """
        model_id = model_config.get("model_id")
        deployment_type = model_config.get("deployment_type")
        
        if not model_id or not deployment_type:
            raise ValueError("model_id と deployment_type は必須です")
        
        # 適切なデプロイアダプターを取得
        if deployment_type not in self.adapters:
            raise ValueError(f"サポートされていないデプロイタイプ: {deployment_type}")
        
        adapter = self.adapters[deployment_type]
        
        try:
            # モデル状態を「デプロイ中」に更新
            self._update_model_state(
                model_id,
                ModelStatus.DEPLOYING.value,
                {"deployment_start": time.time()}
            )
            
            # アダプターを使用してデプロイ実行
            deployment_result = await adapter.deploy(model_config)
            
            # デプロイ成功時の処理
            self._update_model_state(
                model_id,
                ModelStatus.ACTIVE.value,
                {
                    "deployment_end": time.time(),
                    "deployment_info": deployment_result,
                    "config": model_config
                }
            )
            
            # デプロイ設定を保存
            await self.config_service.save_deployed_model_state(model_id, self.deployed_models[model_id])
            
            # 監視設定（オプション）
            if self.monitoring_service:
                await self.monitoring_service.register_model_monitoring(model_id, model_config)
            
            return {
                "model_id": model_id,
                "status": "success",
                "deployment_type": deployment_type,
                "timestamp": time.time(),
                "details": deployment_result
            }
            
        except Exception as e:
            # デプロイ失敗時の処理
            error_details = {
                "error": str(e),
                "timestamp": time.time()
            }
            
            self._update_model_state(
                model_id,
                ModelStatus.ERROR.value,
                {"last_error": error_details}
            )
            
            # エラー状態を保存
            await self.config_service.save_deployed_model_state(model_id, self.deployed_models[model_id])
            
            raise RuntimeError(f"モデル {model_id} のデプロイに失敗: {str(e)}")
    
    async def undeploy_model(
        self,
        model_id: str
    ) -> Dict[str, Any]:
        """
        デプロイされたモデルを削除

        Args:
            model_id: アンデプロイするモデルID

        Returns:
            操作結果
        """
        if model_id not in self.deployed_models:
            raise ValueError(f"モデル {model_id} は現在デプロイされていません")
        
        model_info = self.deployed_models[model_id]
        deployment_type = model_info.get("config", {}).get("deployment_type")
        
        if not deployment_type or deployment_type not in self.adapters:
            raise ValueError(f"モデル {model_id} のデプロイタイプが無効です")
        
        adapter = self.adapters[deployment_type]
        
        try:
            # アンデプロイを実行
            result = await adapter.undeploy(model_id, model_info)
            
            # モデル状態を非アクティブに更新
            self._update_model_state(
                model_id,
                ModelStatus.INACTIVE.value,
                {"undeployment_timestamp": time.time()}
            )
            
            # 状態を保存
            await self.config_service.save_deployed_model_state(model_id, self.deployed_models[model_id])
            
            # 監視からの削除（オプション）
            if self.monitoring_service:
                await self.monitoring_service.unregister_model_monitoring(model_id)
            
            return {
                "model_id": model_id,
                "status": "undeployed",
                "timestamp": time.time(),
                "details": result
            }
            
        except Exception as e:
            raise RuntimeError(f"モデル {model_id} のアンデプロイに失敗: {str(e)}")
    
    async def update_model(
        self,
        model_id: str,
        new_config: Dict[str, Any],
        update_strategy: str = "rolling"
    ) -> Dict[str, Any]:
        """
        デプロイされたモデルを更新

        Args:
            model_id: 更新するモデルID
            new_config: 新しい設定
            update_strategy: 更新戦略（"rolling", "blue_green", "canary"）

        Returns:
            更新結果
        """
        if model_id not in self.deployed_models:
            raise ValueError(f"モデル {model_id} は現在デプロイされていません")
        
        current_info = self.deployed_models[model_id]
        current_config = current_info.get("config", {})
        deployment_type = current_config.get("deployment_type")
        
        if not deployment_type or deployment_type not in self.adapters:
            raise ValueError(f"モデル {model_id} のデプロイタイプが無効です")
        
        # 設定をマージ（新しい設定で上書き）
        merged_config = {**current_config, **new_config}
        merged_config["model_id"] = model_id  # IDは変更不可
        
        adapter = self.adapters[deployment_type]
        
        try:
            # 更新状態を記録
            self._update_model_state(
                model_id,
                ModelStatus.ROLLING_UPDATE.value,
                {"update_start": time.time(), "old_config": current_config, "new_config": new_config}
            )
            
            # 更新戦略を選択
            if update_strategy == "rolling":
                result = await self._rolling_update(model_id, adapter, merged_config)
            elif update_strategy == "blue_green":
                result = await self._blue_green_update(model_id, adapter, merged_config)
            elif update_strategy == "canary":
                result = await self._canary_update(model_id, adapter, merged_config)
            else:
                raise ValueError(f"サポートされていない更新戦略: {update_strategy}")
            
            # 更新完了を記録
            self._update_model_state(
                model_id,
                ModelStatus.ACTIVE.value,
                {
                    "update_end": time.time(),
                    "update_result": result,
                    "config": merged_config
                }
            )
            
            # 状態を保存
            await self.config_service.save_deployed_model_state(model_id, self.deployed_models[model_id])
            
            return {
                "model_id": model_id,
                "status": "updated",
                "update_strategy": update_strategy,
                "timestamp": time.time(),
                "details": result
            }
            
        except Exception as e:
            # 更新失敗を記録
            self._update_model_state(
                model_id,
                ModelStatus.ERROR.value,
                {"update_error": str(e), "timestamp": time.time()}
            )
            
            await self.config_service.save_deployed_model_state(model_id, self.deployed_models[model_id])
            
            raise RuntimeError(f"モデル {model_id} の更新に失敗: {str(e)}")
    
    async def get_model_status(
        self,
        model_id: str
    ) -> Dict[str, Any]:
        """
        デプロイされたモデルの現在のステータスを取得

        Args:
            model_id: ステータスを取得するモデルID

        Returns:
            モデルステータスの詳細
        """
        if model_id not in self.deployed_models:
            raise ValueError(f"モデル {model_id} は登録されていません")
        
        model_info = self.deployed_models[model_id]
        
        # 最新のステータスを確認
        current_status = model_info.get("status", ModelStatus.INACTIVE.value)
        
        if current_status == ModelStatus.ACTIVE.value:
            # アクティブモデルの場合、実際の状態を確認
            try:
                deployment_type = model_info.get("config", {}).get("deployment_type")
                if deployment_type in self.adapters:
                    adapter = self.adapters[deployment_type]
                    health_check = await adapter.check_health(model_id, model_info)
                    
                    if not health_check.get("healthy", False):
                        # ヘルスチェック失敗の場合、状態を更新
                        self._update_model_state(
                            model_id,
                            ModelStatus.ERROR.value,
                            {"health_check_failure": health_check}
                        )
                        
                        await self.config_service.save_deployed_model_state(model_id, self.deployed_models[model_id])
            except Exception as e:
                print(f"ヘルスチェックエラー: {e}")
                # エラーはログに記録するが、状態は変更しない
        
        # 最終的なステータス情報を返す
        return {
            "model_id": model_id,
            "status": self.deployed_models[model_id].get("status"),
            "last_updated": self.deployed_models[model_id].get("last_updated"),
            "deployment_info": self.deployed_models[model_id].get("deployment_info", {}),
            "health": self.deployed_models[model_id].get("health", {})
        }
    
    async def list_deployed_models(
        self,
        filter_status: Optional[str] = None
    ) -> List[Dict[str, Any]]:
        """
        デプロイされたモデルの一覧を取得

        Args:
            filter_status: 特定のステータスでフィルタリング（オプション）

        Returns:
            デプロイされたモデルのリスト
        """
        results = []
        
        for model_id, model_info in self.deployed_models.items():
            status = model_info.get("status")
            
            # ステータスフィルタリング
            if filter_status and status != filter_status:
                continue
            
            results.append({
                "model_id": model_id,
                "status": status,
                "deployment_type": model_info.get("config", {}).get("deployment_type"),
                "last_updated": model_info.get("last_updated"),
                "is_active": status == ModelStatus.ACTIVE.value
            })
        
        return results
    
    def _update_model_state(
        self,
        model_id: str,
        status: str,
        updates: Dict[str, Any] = None
    ):
        """モデルの状態を更新"""
        if model_id not in self.deployed_models:
            self.deployed_models[model_id] = {}
        
        self.deployed_models[model_id]["status"] = status
        self.deployed_models[model_id]["last_updated"] = time.time()
        
        if updates:
            for key, value in updates.items():
                self.deployed_models[model_id][key] = value
    
    async def _refresh_model_states(self):
        """すべてのデプロイ済みモデルの状態を更新"""
        refresh_tasks = []
        
        for model_id in self.deployed_models:
            task = self.get_model_status(model_id)
            refresh_tasks.append(task)
        
        if refresh_tasks:
            await asyncio.gather(*refresh_tasks)
    
    async def _rolling_update(
        self,
        model_id: str,
        adapter,
        new_config: Dict[str, Any]
    ) -> Dict[str, Any]:
        """ローリングアップデート（段階的に置き換え）を実施"""
        # 現在の設定を取得
        current_info = self.deployed_models[model_id]
        
        # 更新を実行
        result = await adapter.update(model_id, new_config, current_info)
        
        return {
            "update_type": "rolling",
            "result": result,
            "timestamp": time.time()
        }
    
    async def _blue_green_update(
        self,
        model_id: str,
        adapter,
        new_config: Dict[str, Any]
    ) -> Dict[str, Any]:
        """
        ブルー/グリーンデプロイメント実装
        新環境を並行デプロイし、問題なければトラフィック切り替え
        """
        # 一時的な新環境用のID
        temp_id = f"{model_id}-green"
        
        try:
            # 新バージョンをテンポラリIDでデプロイ
            green_config = dict(new_config)
            green_config["model_id"] = temp_id
            green_config["is_temporary"] = True
            
            # 新環境デプロイ
            green_result = await adapter.deploy(green_config)
            
            # 新環境テスト（実装によるが、ここではヘルスチェックのみ）
            health_result = await adapter.check_health(temp_id, {"config": green_config})
            
            if not health_result.get("healthy", False):
                # 新環境に問題ありの場合、破棄して終了
                await adapter.undeploy(temp_id, {"config": green_config})
                raise RuntimeError(f"新環境のヘルスチェックに失敗: {health_result}")
            
            # トラフィックを新環境に切り替え
            switch_result = await adapter.switch_traffic(model_id, temp_id)
            
            # 古い環境を削除
            old_info = self.deployed_models[model_id]
            await adapter.undeploy(model_id, old_info)
            
            # 新環境のIDを本番IDに変更（アダプター実装による）
            rename_result = await adapter.rename_deployment(temp_id, model_id)
            
            return {
                "update_type": "blue_green",
                "green_deployment": green_result,
                "health_check": health_result,
                "traffic_switch": switch_result,
                "old_env_removal": True,
                "rename_result": rename_result,
                "timestamp": time.time()
            }
            
        except Exception as e:
            # エラー時のクリーンアップ
            try:
                await adapter.undeploy(temp_id, {"config": new_config})
            except:
                pass  # クリーンアップエラーは無視
                
            raise RuntimeError(f"ブルー/グリーンデプロイに失敗: {str(e)}")
    
    async def _canary_update(
        self,
        model_id: str,
        adapter,
        new_config: Dict[str, Any]
    ) -> Dict[str, Any]:
        """
        カナリアデプロイメント実装
        トラフィックを段階的に新バージョンに移行
        """
        # カナリアデプロイ用の一時ID
        canary_id = f"{model_id}-canary"
        
        try:
            # カナリア設定の作成
            canary_config = dict(new_config)
            canary_config["model_id"] = canary_id
            canary_config["is_canary"] = True
            
            # カナリアデプロイ実行
            canary_result = await adapter.deploy(canary_config)
            
            # 初期トラフィック振り分け（10%）
            initial_split = await adapter.split_traffic(model_id, canary_id, 0.1)
            
            # トラフィック比率を段階的に増加（アダプターの実装により異なる）
            # 以下はシンプルな例として、直接50%、その後100%に増加
            
            # 通常はここで一定期間待機してメトリクスをチェック
            await asyncio.sleep(30)  # 例として30秒待機
            
            # 50%にトラフィック増加
            split_50 = await adapter.split_traffic(model_id, canary_id, 0.5)
            
            # さらに待機してチェック
            await asyncio.sleep(30)  # 例として30秒待機
            
            # 100%に増加（完全切り替え）
            final_split = await adapter.split_traffic(model_id, canary_id, 1.0)
            
            # 古いバージョンを削除
            old_info = self.deployed_models[model_id]
            await adapter.undeploy(model_id, old_info)
            
            # カナリアを正式バージョンにリネーム
            rename_result = await adapter.rename_deployment(canary_id, model_id)
            
            return {
                "update_type": "canary",
                "canary_deployment": canary_result,
                "traffic_splits": [
                    {"ratio": 0.1, "result": initial_split},
                    {"ratio": 0.5, "result": split_50},
                    {"ratio": 1.0, "result": final_split}
                ],
                "old_version_removal": True,
                "rename_result": rename_result,
                "timestamp": time.time()
            }
            
        except Exception as e:
            # エラー時のクリーンアップ
            try:
                # トラフィックを元に戻す
                await adapter.split_traffic(model_id, canary_id, 0.0)
                # カナリアを削除
                await adapter.undeploy(canary_id, {"config": canary_config})
            except:
                pass  # クリーンアップエラーは無視
                
            raise RuntimeError(f"カナリアデプロイに失敗: {str(e)}")
```

### 6.2 デプロイアダプター実装

```python
# backend/app/services/deployment_adapters.py
from abc import ABC, abstractmethod
from typing import Dict, Any

class DeploymentAdapter(ABC):
    """デプロイメント方法ごとの具体的な実装を提供する抽象基底クラス"""
    
    @abstractmethod
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """モデルをデプロイ"""
        pass
    
    @abstractmethod
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """モデルをアンデプロイ"""
        pass
    
    @abstractmethod
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        """既存デプロイを更新"""
        pass
    
    @abstractmethod
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """デプロイの健全性をチェック"""
        pass
    
    @abstractmethod
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        """トラフィックを切り替え（Blue/Greenデプロイ用）"""
        pass
    
    @abstractmethod
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        """トラフィックを分割（カナリアデプロイ用）"""
        pass
    
    @abstractmethod
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        """デプロイメントのIDを変更"""
        pass

class ThirdPartyAPIAdapter(DeploymentAdapter):
    """サードパーティAPIサービス（OpenAI, Claude等）のデプロイ管理"""
    
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """API接続を設定"""
        model_id = config.get("model_id")
        provider = config.get("provider", "openai")  # openai, anthropic, etc
        
        # APIキーの検証や接続テストを実施
        # 実際の実装では、APIキーの暗号化保存や検証を行う
        
        return {
            "model_id": model_id,
            "provider": provider,
            "status": "connected",
            "connection_test": "success"
        }
    
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """API連携を解除"""
        # 実際の実装では、保存されたAPIキーの削除などを行う
        
        return {
            "model_id": model_id,
            "status": "disconnected"
        }
    
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        """API設定を更新"""
        provider = new_config.get("provider", current_info.get("config", {}).get("provider"))
        
        # 設定の更新と検証
        # 実際の実装では、APIキーの更新や接続テストを行う
        
        return {
            "model_id": model_id,
            "provider": provider,
            "status": "updated",
            "connection_test": "success"
        }
    
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """API接続の健全性をチェック"""
        config = model_info.get("config", {})
        provider = config.get("provider", "unknown")
        
        # 実際の実装では、APIへの簡単なリクエストを送信して応答を確認
        
        return {
            "healthy": True,
            "latency_ms": 150,  # ダミー値
            "provider_status": "operational"
        }
    
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        """サードパーティAPIの場合、設定の切り替えを実施"""
        # APIサービスの場合、通常はクライアント側の設定変更で対応
        
        return {
            "from_id": from_id,
            "to_id": to_id,
            "status": "switched"
        }
    
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        """トラフィック分割（クライアント側で実装）"""
        # サードパーティAPIの場合、クライアント側でランダムに振り分け制御
        
        return {
            "primary_id": primary_id,
            "secondary_id": secondary_id,
            "secondary_ratio": secondary_ratio,
            "status": "configured"
        }
    
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        """APIモデル参照の名称変更"""
        # 実際の実装では、内部マッピングテーブルの更新など
        
        return {
            "previous_id": current_id,
            "new_id": new_id,
            "status": "renamed"
        }

class ManagedServiceAdapter(DeploymentAdapter):
    """クラウドマネージドサービス（AWS Bedrock, Vertex AI等）のデプロイ管理"""
    
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """マネージドサービスを設定"""
        model_id = config.get("model_id")
        service = config.get("service", "aws_bedrock")  # aws_bedrock, gcp_vertex, azure_openai
        model_name = config.get("model_name")
        
        # 実際の実装では、クラウドプロバイダーのSDKを使用して設定
        
        return {
            "model_id": model_id,
            "service": service,
            "model_name": model_name,
            "status": "provisioned",
            "endpoint": f"https://api.example.com/v1/models/{model_id}"  # ダミー値
        }
    
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """マネージドサービスの連携を解除"""
        config = model_info.get("config", {})
        service = config.get("service")
        
        # 実際の実装では、リソースの解放などを行う
        
        return {
            "model_id": model_id,
            "service": service,
            "status": "deprovisioned"
        }
    
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        """マネージドサービス設定を更新"""
        current_config = current_info.get("config", {})
        service = new_config.get("service", current_config.get("service"))
        model_name = new_config.get("model_name", current_config.get("model_name"))
        
        # 実際の実装では、クラウドプロバイダーのSDKを使用して設定を更新
        
        return {
            "model_id": model_id,
            "service": service,
            "model_name": model_name,
            "status": "updated"
        }
    
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """マネージドサービスの健全性をチェック"""
        config = model_info.get("config", {})
        service = config.get("service")
        
        # 実際の実装では、サービスのステータスAPIを呼び出す
        
        return {
            "healthy": True,
            "service": service,
            "status_details": "operational",
            "quota_remaining_percentage": 85  # ダミー値
        }
    
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        """マネージドサービスのエンドポイント切り替え"""
        # 実際の実装では、サービス固有のAPI呼び出しを行う
        
        return {
            "from_id": from_id,
            "to_id": to_id,
            "status": "switched"
        }
    
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        """トラフィック分割（サービスのルーティング機能を使用）"""
        # 実際の実装では、サービス固有のルーティング設定を行う
        
        return {
            "primary_id": primary_id,
            "secondary_id": secondary_id,
            "secondary_ratio": secondary_ratio,
            "status": "traffic_split_configured"
        }
    
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        """マネージドサービスでのID変更"""
        # 実際の実装では、クラウドプロバイダーのリネーム操作を行う
        
        return {
            "previous_id": current_id,
            "new_id": new_id,
            "status": "renamed"
        }

class ContainerAdapter(DeploymentAdapter):
    """コンテナベースのデプロイ管理（Kubernetes等）"""
    
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """モデルをコンテナとしてデプロイ"""
        model_id = config.get("model_id")
        container_image = config.get("container_image")
        replicas = config.get("replicas", 1)
        
        # 実際の実装では、KubernetesのAPIを呼び出すなど
        
        return {
            "model_id": model_id,
            "container_image": container_image,
            "replicas_deployed": replicas,
            "status": "deployed",
            "service_url": f"https://model-{model_id}.example.com"  # ダミー値
        }
    
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """コンテナをアンデプロイ"""
        # 実際の実装では、KubernetesのDeploymentを削除するなど
        
        return {
            "model_id": model_id,
            "status": "undeployed"
        }
    
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        """コンテナデプロイを更新"""
        current_config = current_info.get("config", {})
        container_image = new_config.get("container_image", current_config.get("container_image"))
        replicas = new_config.get("replicas", current_config.get("replicas", 1))
        
        # 実際の実装では、Kubernetesのイメージ更新やスケーリングを行う
        
        return {
            "model_id": model_id,
            "container_image": container_image,
            "replicas": replicas,
            "status": "updated"
        }
    
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        """コンテナの健全性チェック"""
        # 実際の実装では、Kubernetesのヘルスチェック結果や、
        # サービスのヘルスエンドポイントをチェックする
        
        return {
            "healthy": True,
            "ready_pods": 3,  # ダミー値
            "total_pods": 3,   # ダミー値
            "average_cpu_usage": "45%"  # ダミー値
        }
    
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        """サービス/Ingressの向き先を切り替え"""
        # 実際の実装では、KubernetesのServiceやIngressを更新
        
        return {
            "from_id": from_id,
            "to_id": to_id,
            "status": "switched"
        }
    
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        """トラフィック分割（Istioなどを使用）"""
        # 実際の実装では、サービスメッシュのルールを設定
        
        return {
            "primary_id": primary_id,
            "secondary_id": secondary_id,
            "secondary_ratio": secondary_ratio,
            "status": "traffic_split_configured"
        }
    
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        """デプロイメント名の変更"""
        # 実際の実装では、Kubernetesリソースのラベル変更などを行う
        
        return {
            "previous_id": current_id,
            "new_id": new_id,
            "status": "renamed"
        }

# その他のアダプター（ServerlessAdapter, EdgeAdapter, OnPremiseAdapter）も
# 同様のインターフェースで実装します。簡潔にするため省略します。

class ServerlessAdapter(DeploymentAdapter):
    """サーバーレス関数としてのデプロイ管理（AWS Lambda, Cloud Functions等）"""
    
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"healthy": True}
    
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}

class EdgeAdapter(DeploymentAdapter):
    """エッジデプロイメント管理（CDN, エッジコンピューティング等）"""
    
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"healthy": True}
    
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}

class OnPremiseAdapter(DeploymentAdapter):
    """オンプレミスデプロイメント管理"""
    
    async def deploy(self, config: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def undeploy(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def update(self, model_id: str, new_config: Dict[str, Any], current_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def check_health(self, model_id: str, model_info: Dict[str, Any]) -> Dict[str, Any]:
        # 実装省略
        return {"healthy": True}
    
    async def switch_traffic(self, from_id: str, to_id: str) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def split_traffic(self, primary_id: str, secondary_id: str, secondary_ratio: float) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
    
    async def rename_deployment(self, current_id: str, new_id: str) -> Dict[str, Any]:
        # 実装省略
        return {"status": "not_implemented"}
```

## 7. パフォーマンス最適化

AIモデルとアプリケーションのパフォーマンスを最適化するための技術についてです。

### 7.1 クエリ最適化と並列処理

```python
# backend/app/services/performance_optimization.py
import asyncio
import time
from typing import List, Dict, Any, Callable, Optional, Tuple
import json
from concurrent.futures import ThreadPoolExecutor
from functools import lru_cache

class QueryOptimizer:
    """AIモデル呼び出しの最適化と並列処理を提供するサービス"""
    
    def __init__(self, model_service, cache_service=None):
        self.model_service = model_service
        self.cache_service = cache_service  # オプショナル
        
        # パフォーマンスメトリクス追跡
        self.latency_metrics = {
            "total_requests": 0,
            "cache_hits": 0,
            "average_latency": 0,
            "model_latencies": {}  # モデル別の平均レイテンシ
        }
        
        # 結果キャッシュのサイズ制限
        self.max_cache_size = 1000
        self._result_cache = {}  # {hash: {"result": result, "timestamp": time}}
    
    async def optimized_query(
        self,
        prompt: str,
        model_id: str = None,
        parameters: Dict[str, Any] = None,
        use_cache: bool = True,
        cache_ttl: int = 3600  # キャッシュの有効期間（秒）
    ) -> Dict[str, Any]:
        """
        最適化されたモデルクエリ
        キャッシュ、最適化、メトリクス追跡を組み込み

        Args:
            prompt: プロンプト文字列
            model_id: 使用するモデルID（省略時はデフォルト）
            parameters: モデルパラメーター
            use_cache: キャッシュを使用するか
            cache_ttl: キャッシュの有効期間（秒）

        Returns:
            レスポンスと最適化メトリクス
        """
        start_time = time.time()
        parameters = parameters or {}
        
        # リクエストのハッシュ生成
        request_hash = self._hash_request(prompt, model_id, parameters)
        
        # キャッシュチェック
        if use_cache:
            cached_result = await self._get_cached_result(request_hash, cache_ttl)
            if cached_result:
                self.latency_metrics["cache_hits"] += 1
                elapsed_time = time.time() - start_time
                
                return {
                    "response": cached_result,
                    "metrics": {
                        "latency_ms": elapsed_time * 1000,
                        "cache_hit": True,
                        "optimized": True
                    }
                }
        
        # キャッシュミスの場合、モデル呼び出し
        response = await self.model_service.generate_text(model_id, prompt, parameters)
        
        # キャッシュ保存
        if use_cache:
            await self._cache_result(request_hash, response)
        
        # メトリクス更新
        elapsed_time = time.time() - start_time
        self._update_metrics(model_id, elapsed_time)
        
        return {
            "response": response,
            "metrics": {
                "latency_ms": elapsed_time * 1000,
                "cache_hit": False,
                "model_id": model_id
            }
        }
    
    async def parallel_queries(
        self,
        requests: List[Dict[str, Any]],
        max_concurrency: int = 5
    ) -> List[Dict[str, Any]]:
        """
        複数のクエリを並列実行

        Args:
            requests: クエリリクエストのリスト [{prompt, model_id, parameters}, ...]
            max_concurrency: 最大同時実行数

        Returns:
            すべてのレスポンスのリスト
        """
        # 同時実行数を制限したタスク実行
        semaphore = asyncio.Semaphore(max_concurrency)
        
        async def _bounded_query(request):
            async with semaphore:
                return await self.optimized_query(
                    request.get("prompt"),
                    request.get("model_id"),
                    request.get("parameters"),
                    request.get("use_cache", True)
                )
        
        # 並列実行
        tasks = [_bounded_query(request) for request in requests]
        results = await asyncio.gather(*tasks)
        
        return results
    
    async def batch_process(
        self,
        items: List[Any],
        process_fn: Callable[[Any], Dict[str, Any]],
        chunk_size: int = 10,
        max_concurrency: int = 5
    ) -> List[Dict[str, Any]]:
        """
        大量のアイテムを処理する高度なバッチ処理

        Args:
            items: 処理するアイテムのリスト
            process_fn: 各アイテムを処理する関数 (item -> request_dict)
            chunk_size: 一度に処理するチャンクサイズ
            max_concurrency: 最大同時実行数

        Returns:
            すべての処理結果
        """
        results = []
        
        # アイテムをチャンクに分割
        for i in range(0, len(items), chunk_size):
            chunk = items[i:i + chunk_size]
            
            # チャンクの各アイテムをリクエストに変換
            requests = [process_fn(item) for item in chunk]
            
            # チャンクを並列処理
            chunk_results = await self.parallel_queries(requests, max_concurrency)
            results.extend(chunk_results)
            
            # レート制限などのために短い待機を挿入（必要に応じて）
            await asyncio.sleep(0.1)
        
        return results
    
    async def chain_queries(
        self,
        initial_prompt: str,
        processing_steps: List[Dict[str, Any]],
        model_id: str = None,
        use_cache: bool = True
    ) -> Dict[str, Any]:
        """
        クエリチェーンの最適化された実行
        複数ステップの処理を効率的に実行

        Args:
            initial_prompt: 初期プロンプト
            processing_steps: 処理ステップのリスト [{"transform_fn": callable, "parameters": {}}]
            model_id: モデルID
            use_cache: キャッシュ使用フラグ

        Returns:
            最終的な処理結果
        """
        current_result = None
        all_metrics = []
        total_start_time = time.time()
        
        # 初期プロンプトを処理
        current_prompt = initial_prompt
        
        # 各処理ステップを順次実行
        for i, step in enumerate(processing_steps):
            step_parameters = step.get("parameters", {})
            transform_fn = step.get("transform_fn")
            
            # モデル実行
            query_result = await self.optimized_query(
                current_prompt,
                model_id,
                step_parameters,
                use_cache
            )
            
            # メトリクス収集
            step_metrics = query_result["metrics"]
            step_metrics["step"] = i
            all_metrics.append(step_metrics)
            
            # レスポンスを取得
            current_result = query_result["response"]
            
            # 次のステップのために結果を変換（最後のステップ以外）
            if i < len(processing_steps) - 1 and transform_fn:
                current_prompt = transform_fn(current_result)
        
        # 合計時間の計算
        total_time = time.time() - total_start_time
        
        return {
            "final_result": current_result,
            "metrics": {
                "total_latency_ms": total_time * 1000,
                "steps_count": len(processing_steps),
                "steps_metrics": all_metrics
            }
        }
    
    def _hash_request(self, prompt: str, model_id: str, parameters: Dict[str, Any]) -> str:
        """リクエストのハッシュを生成"""
        import hashlib
        
        # モデルID、プロンプト、パラメータをまとめてハッシュ化
        model_str = str(model_id) if model_id else "default"
        param_str = json.dumps(parameters, sort_keys=True) if parameters else "{}"
        
        combined = f"{model_str}:{prompt}:{param_str}"
        return hashlib.md5(combined.encode()).hexdigest()
    
    async def _get_cached_result(self, request_hash: str, ttl: int) -> Optional[str]:
        """キャッシュから結果を取得"""
        # 外部キャッシュサービスがある場合はそちらを使用
        if self.cache_service:
            return await self.cache_service.get(f"query:{request_hash}")
        
        # 内部キャッシュを使用
        if request_hash in self._result_cache:
            cache_entry = self._result_cache[request_hash]
            timestamp = cache_entry.get("timestamp", 0)
            
            # TTLチェック
            if time.time() - timestamp <= ttl:
                return cache_entry.get("result")
        
        return None
    
    async def _cache_result(self, request_hash: str, result: str):
        """結果をキャッシュに保存"""
        # 外部キャッシュサービスがある場合はそちらを使用
        if self.cache_service:
            await self.cache_service.set(f"query:{request_hash}", result)
            return
        
        # 内部キャッシュを使用
        self._result_cache[request_hash] = {
            "result": result,
            "timestamp": time.time()
        }
        
        # キャッシュサイズが上限を超えた場合、古いエントリを削除
        if len(self._result_cache) > self.max_cache_size:
            self._clean_cache()
    
    def _clean_cache(self):
        """古いキャッシュエントリを削除"""
        # タイムスタンプでソートし、古いものから半分を削除
        sorted_entries = sorted(
            self._result_cache.items(),
            key=lambda x: x[1]["timestamp"]
        )
        
        # 半分を削除
        entries_to_remove = sorted_entries[:len(sorted_entries) // 2]
        for key, _ in entries_to_remove:
            del self._result_cache[key]
    
    def _update_metrics(self, model_id: str, latency: float):
        """パフォーマンスメトリクスを更新"""
        self.latency_metrics["total_requests"] += 1
        
        # 累積平均を更新
        current_avg = self.latency_metrics["average_latency"]
        total_requests = self.latency_metrics["total_requests"]
        self.latency_metrics["average_latency"] = (
            current_avg * (total_requests - 1) + latency * 1000
        ) / total_requests
        
        # モデル別のメトリクスを更新
        model_key = model_id if model_id else "default"
        if model_key not in self.latency_metrics["model_latencies"]:
            self.latency_metrics["model_latencies"][model_key] = {
                "requests": 0,
                "average_latency": 0
            }
        
        model_metrics = self.latency_metrics["model_latencies"][model_key]
        model_metrics["requests"] += 1
        model_metrics["average_latency"] = (
            model_metrics["average_latency"] * (model_metrics["requests"] - 1) + latency * 1000
        ) / model_metrics["requests"]
    
    def get_performance_metrics(self) -> Dict[str, Any]:
        """現在のパフォーマンスメトリクスを取得"""
        return {
            "total_requests": self.latency_metrics["total_requests"],
            "cache_hit_rate": (
                self.latency_metrics["cache_hits"] / self.latency_metrics["total_requests"]
                if self.latency_metrics["total_requests"] > 0 else 0
            ),
            "average_latency_ms": self.latency_metrics["average_latency"],
            "models_latency": self.latency_metrics["model_latencies"],
            "cache_size": len(self._result_cache) if not self.cache_service else None
        }
```

### 7.2 レスポンスストリーミングと最適化

```python
# backend/app/services/streaming_optimization.py
import asyncio
from typing import List, Dict, Any, AsyncGenerator, Callable
import json
import time

class StreamingOptimizer:
    """AIレスポンスストリーミングの最適化を提供するサービス"""
    
    def __init__(self, model_service):
        self.model_service = model_service
    
    async def stream_response(
        self,
        prompt: str,
        model_id: str = None,
        parameters: Dict[str, Any] = None,
        chunk_processor: Callable[[str], str] = None
    ) -> AsyncGenerator[Dict[str, Any], None]:
        """
        最適化されたストリーミングレスポンス

        Args:
            prompt: プロンプト文字列
            model_id: 使用するモデルID
            parameters: モデルパラメーター
            chunk_processor: チャンク処理関数（オプション）

        Yields:
            チャンク単位のレスポンスとメトリクス
        """
        start_time = time.time()
        parameters = parameters or {}
        
        # ストリーミングパラメータを追加
        streaming_params = {**parameters, "stream": True}
        
        # チャンク数とバイトカウンタ
        chunk_count = 0
        total_bytes = 0
        
        # LLMにストリーミングリクエスト送信
        async for chunk in self.model_service.generate_stream(model_id, prompt, streaming_params):
            chunk_count += 1
            chunk_size = len(chunk.encode('utf-8'))
            total_bytes += chunk_size
            
            # チャンク処理関数があれば適用
            processed_chunk = chunk_processor(chunk) if chunk_processor else chunk
            
            # 経過時間の計算
            elapsed_time = time.time() - start_time
            
            yield {
                "chunk": processed_chunk,
                "metrics": {
                    "chunk_index": chunk_count,
                    "chunk_size_bytes": chunk_size,
                    "elapsed_time_ms": elapsed_time * 1000
                },
                "is_final": False
            }
        
        # 最終メトリクス
        total_elapsed = time.time() - start_time
        
        yield {
            "chunk": "",
            "metrics": {
                "total_chunks": chunk_count,
                "total_bytes": total_bytes,
                "total_time_ms": total_elapsed * 1000,
                "average_chunk_size": total_bytes / chunk_count if chunk_count > 0 else 0,
                "average_throughput_bytes_sec": total_bytes / total_elapsed if total_elapsed > 0 else 0
            },
            "is_final": True
        }
    
    async def background_processing_stream(
        self,
        prompt: str,
        model_id: str = None,
        parameters: Dict[str, Any] = None,
        initial_response: str = "処理中です...",
        polling_interval: float = 0.5
    ) -> AsyncGenerator[Dict[str, Any], None]:
        """
        バックグラウンド処理＋ストリーミング応答
        長時間かかる処理を非同期に実行しながら進捗を報告

        Args:
            prompt: プロンプト文字列
            model_id: 使用するモデルID
            parameters: モデルパラメーター
            initial_response: 初期レスポンス文字列
            polling_interval: 進捗確認間隔（秒）

        Yields:
            進捗状況とレスポンスチャンク
        """
        # タスク開始を通知
        yield {
            "chunk": initial_response,
            "status": "started",
            "progress": 0.0,
            "is_final": False
        }
        
        # バックグラウンドタスクとして実行
        task = asyncio.create_task(
            self.model_service.generate_text(model_id, prompt, parameters or {})
        )
        
        start_time = time.time()
        last_update_time = start_time
        
        # 完了まで定期的に進捗を報告
        while not task.done():
            await asyncio.sleep(polling_interval)
            
            # 経過時間を基に疑似進捗を計算
            elapsed = time.time() - start_time
            # 最大30秒を想定した進捗の例（実際の進捗推定はより洗練された方法を使用）
            progress = min(0.95, elapsed / 30.0)  
            
            # 前回から更新があった場合のみ通知
            current_time = time.time()
            if current_time - last_update_time >= polling_interval:
                last_update_time = current_time
                yield {
                    "chunk": "",
                    "status": "processing",
                    "progress": progress,
                    "elapsed_seconds": elapsed,
                    "is_final": False
                }
        
        # タスク完了と結果取得
        try:
            result = task.result()
            total_elapsed = time.time() - start_time
            
            # 結果を返す
            yield {
                "chunk": result,
                "status": "completed",
                "progress": 1.0,
                "total_time": total_elapsed,
                "is_final": True
            }
            
        except Exception as e:
            # エラー時
            yield {
                "chunk": f"処理中にエラーが発生しました: {str(e)}",
                "status": "error",
                "error": str(e),
                "is_final": True
            }
    
    async def adaptive_streaming(
        self,
        prompt: str,
        model_id: str = None,
        parameters: Dict[str, Any] = None,
        chunk_size_target: int = 20,
        max_latency_ms: int = 500
    ) -> AsyncGenerator[Dict[str, Any], None]:
        """
        ネットワークとクライアント環境に適応するストリーミング
        チャンクサイズと送信間隔を調整して最適なUXを提供

        Args:
            prompt: プロンプト文字列
            model_id: 使用するモデルID
            parameters: モデルパラメーター
            chunk_size_target: 目標チャンクサイズ（文字数）
            max_latency_ms: 許容最大レイテンシ（ミリ秒）

        Yields:
            最適化されたレスポンスチャンク
        """
        buffer = ""
        last_send_time = time.time()
        start_time = last_send_time
        total_chars = 0
        
        # ストリーミングパラメータ設定
        streaming_params = {**parameters, "stream": True} if parameters else {"stream": True}
        
        # モデルからストリーミング取得
        async for chunk in self.model_service.generate_stream(model_id, prompt, streaming_params):
            buffer += chunk
            now = time.time()
            elapsed_since_last = (now - last_send_time) * 1000  # ミリ秒
            
            # バッファが目標サイズに達したか、最大レイテンシを超えた場合に送信
            if len(buffer) >= chunk_size_target or elapsed_since_last >= max_latency_ms:
                total_chars += len(buffer)
                elapsed = now - start_time
                
                yield {
                    "chunk": buffer,
                    "metrics": {
                        "chunk_size": len(buffer),
                        "elapsed_ms": elapsed * 1000,
                        "chars_per_second": total_chars / elapsed if elapsed > 0 else 0,
                        "latency_ms": elapsed_since_last
                    },
                    "is_final": False
                }
                
                # バッファをクリアし、タイムスタンプを更新
                buffer = ""
                last_send_time = now
                
                # スループット測定に基づいてチャンクサイズを調整
                if elapsed > 2.0:  # 最適化判断するのに十分なデータが集まったら
                    chars_per_second = total_chars / elapsed
                    # 送信間隔(0.1秒)あたりの最適文字数を計算
                    optimal_chunk = max(10, int(chars_per_second * 0.1))
                    chunk_size_target = optimal_chunk
        
        # 残りのバッファがあれば送信
        if buffer:
            total_time = time.time() - start_time
            total_chars += len(buffer)
            
            yield {
                "chunk": buffer,
                "metrics": {
                    "chunk_size": len(buffer),
                    "elapsed_ms": total_time * 1000,
                    "chars_per_second": total_chars / total_time if total_time > 0 else 0,
                    "is_final": False
                }
            }
        
        # 最終メトリクス
        total_time = time.time() - start_time
        yield {
            "chunk": "",
            "metrics": {
                "total_chars": total_chars,
                "total_time_ms": total_time * 1000,
                "average_throughput": total_chars / total_time if total_time > 0 else 0,
                "dynamic_chunk_size": chunk_size_target
            },
            "is_final": True
        }
```

### 7.3 リソース消費最適化

```python
# backend/app/services/resource_optimization.py
from typing import Dict, Any, List, Callable
import time
import asyncio
import threading
from datetime import datetime
import heapq

class ResourceManager:
    """AIコンポーネントのリソース使用を最適化するサービス"""
    
    def __init__(self):
        # リソース使用状況の追跡
        self.resource_usage = {
            "api_calls": {
                "total": 0,
                "rate_limited": 0,
                "by_model": {},
                "by_hour": {}
            },
            "tokens": {
                "input": 0,
                "output": 0,
                "by_model": {}
            },
            "costs": {
                "total": 0.0,
                "by_model": {}
            }
        }
        
        # レート制限の設定
        self.rate_limits = {}  # {model_id: {"tpm": 100000, "rpm": 1000}}
        
        # レート制限のトラッキング
        self._request_timestamps = {}  # {model_id: [timestamps...]}
        self._token_timestamps = {}    # {model_id: [token_counts...]}
        
        # 優先度付きリクエストキュー (priority, insert_time, request_data)
        self._request_queue = []
        self._queue_lock = threading.Lock()
        
        # バックグラウンドワーカー
        self._worker_running = False
        self._worker_task = None
    
    def start_workers(self):
        """リクエスト処理ワーカーを開始"""
        if not self._worker_running:
            self._worker_running = True
            self._worker_task = asyncio.create_task(self._process_queue())
    
    def stop_workers(self):
        """リクエスト処理ワーカーを停止"""
        self._worker_running = False
        if self._worker_task:
            self._worker_task.cancel()
    
    def set_rate_limits(self, model_id: str, tpm_limit: int = None, rpm_limit: int = None):
        """モデルのレート制限を設定"""
        if model_id not in self.rate_limits:
            self.rate_limits[model_id] = {}
        
        if tpm_limit is not None:
            self.rate_limits[model_id]["tpm"] = tpm_limit
        
        if rpm_limit is not None:
            self.rate_limits[model_id]["rpm"] = rpm_limit
        
        # 履歴の初期化
        if model_id not in self._request_timestamps:
            self._request_timestamps[model_id] = []
        
        if model_id not in self._token_timestamps:
            self._token_timestamps[model_id] = []
    
    async def enqueue_request(
        self,
        request_fn: Callable,
        model_id: str,
        priority: int = 1,
        retry_attempts: int = 3,
        timeout: float = 60.0
    ) -> Dict[str, Any]:
        """
        リクエストを優先度付きキューに追加

        Args:
            request_fn: リクエスト実行関数
            model_id: モデルID
            priority: 優先度（低い数値が高優先度）
            retry_attempts: 再試行回数
            timeout: タイムアウト（秒）

        Returns:
            リクエスト結果
        """
        # リクエストデータを作成
        request_data = {
            "fn": request_fn,
            "model_id": model_id,
            "priority": priority,
            "retry_attempts": retry_attempts,
            "timeout": timeout,
            "enqueue_time": time.time()
        }
        
        # フューチャーを作成して結果を待つためのオブジェクト
        future = asyncio.Future()
        request_data["future"] = future
        
        # キューに追加
        with self._queue_lock:
            heapq.heappush(
                self._request_queue,
                (priority, time.time(), request_data)  # 優先度、挿入時間、データ
            )
        
        # 結果を待機
        try:
            return await asyncio.wait_for(future, timeout=timeout)
        except asyncio.TimeoutError:
            return {
                "error": "request_timeout",
                "message": f"Request timed out after {timeout} seconds"
            }
    
    async def _process_queue(self):
        """キューからのリクエスト処理ワーカー"""
        while self._worker_running:
            request_data = None
            
            # キューからアイテムを取得
            with self._queue_lock:
                if self._request_queue:
                    _, _, request_data = heapq.heappop(self._request_queue)
            
            if request_data:
                # リクエスト処理
                model_id = request_data.get("model_id")
                future = request_data.get("future")
                
                if future and not future.done():
                    # レート制限のチェック
                    can_proceed, wait_time = self._check_rate_limits(model_id)
                    
                    if can_proceed:
                        # リクエストを実行
                        try:
                            result = await self._execute_request(request_data)
                            future.set_result(result)
                        except Exception as e:
                            # エラー時は結果にエラーを設定
                            future.set_result({
                                "error": "execution_error",
                                "message": str(e)
                            })
                    else:
                        # レート制限によりリクエストを再キューイング
                        # 待ち時間の後に再試行
                        await asyncio.sleep(wait_time)
                        
                        # 有効期限をチェック
                        enqueue_time = request_data.get("enqueue_time", 0)
                        timeout = request_data.get("timeout", 60.0)
                        
                        if time.time() - enqueue_time < timeout:
                            # 期限内なら再度キューに入れる
                            with self._queue_lock:
                                heapq.heappush(
                                    self._request_queue,
                                    (request_data["priority"], time.time(), request_data)
                                )
                        else:
                            # タイムアウト
                            future.set_result({
                                "error": "rate_limit_timeout",
                                "message": f"Request timed out due to rate limiting"
                            })
                            
                            # 統計の更新
                            self.resource_usage["api_calls"]["rate_limited"] += 1
            else:
                # キューが空の場合は待機
                await asyncio.sleep(0.01)
    
    def _check_rate_limits(self, model_id: str) -> tuple[bool, float]:
        """
        レート制限をチェック

        Returns:
            (処理可能かどうか, 待機すべき時間)
        """
        now = time.time()
        
        if model_id not in self.rate_limits:
            return True, 0  # 制限なし
        
        limits = self.rate_limits.get(model_id, {})
        rpm_limit = limits.get("rpm")
        tpm_limit = limits.get("tpm")
        
        # リクエスト数/分の制限チェック
        if rpm_limit:
            # 1分以内のリクエスト履歴を更新
            self._request_timestamps[model_id] = [
                ts for ts in self._request_timestamps[model_id]
                if now - ts < 60
            ]
            
            # 現在のレート計算
            current_rpm = len(self._request_timestamps[model_id])
            
            if current_rpm >= rpm_limit:
                # 最も古いタイムスタンプから60秒経過するまでの待ち時間を計算
                oldest = min(self._request_timestamps[model_id]) if self._request_timestamps[model_id] else now - 60
                wait_time = 60 - (now - oldest)
                return False, max(0, wait_time)
        
        # トークン数/分の制限チェック
        if tpm_limit:
            # 1分以内のトークン使用履歴を更新
            self._token_timestamps[model_id] = [
                (ts, count) for ts, count in self._token_timestamps[model_id]
                if now - ts < 60
            ]
            
            # 現在のトークン合計計算
            current_tpm = sum(count for _, count in self._token_timestamps[model_id])
            
            if current_tpm >= tpm_limit:
                # 最も古いエントリから60秒経過するまでの待ち時間を計算
                oldest = min(ts for ts, _ in self._token_timestamps[model_id]) if self._token_timestamps[model_id] else now - 60
                wait_time = 60 - (now - oldest)
                return False, max(0, wait_time)
        
        # すべての制限をパス
        return True, 0
    
    async def _execute_request(self, request_data: Dict[str, Any]) -> Dict[str, Any]:
        """リクエストを実行"""
        request_fn = request_data.get("fn")
        model_id = request_data.get("model_id")
        retry_attempts = request_data.get("retry_attempts", 0)
        
        attempt = 0
        last_error = None
        
        while attempt <= retry_attempts:
            try:
                # リクエスト履歴を更新
                now = time.time()
                self._request_timestamps[model_id].append(now)
                
                # API呼び出し数の更新
                self.resource_usage["api_calls"]["total"] += 1
                if model_id not in self.resource_usage["api_calls"]["by_model"]:
                    self.resource_usage["api_calls"]["by_model"][model_id] = 0
                self.resource_usage["api_calls"]["by_model"][model_id] += 1
                
                # 時間帯別の呼び出し数を更新
                hour = datetime.now().strftime("%Y-%m-%d-%H")
                if hour not in self.resource_usage["api_calls"]["by_hour"]:
                    self.resource_usage["api_calls"]["by_hour"][hour] = 0
                self.resource_usage["api_calls"]["by_hour"][hour] += 1
                
                # リクエストを実行
                result = await request_fn()
                
                # トークン使用量とコスト計算（モデル実装依存）
                if "usage" in result:
                    usage = result["usage"]
                    input_tokens = usage.get("prompt_tokens", 0)
                    output_tokens = usage.get("completion_tokens", 0)
                    total_tokens = input_tokens + output_tokens
                    
                    # トークン履歴の更新
                    self._token_timestamps[model_id].append((now, total_tokens))
                    
                    # トークン使用量を更新
                    self.resource_usage["tokens"]["input"] += input_tokens
                    self.resource_usage["tokens"]["output"] += output_tokens
                    
                    if model_id not in self.resource_usage["tokens"]["by_model"]:
                        self.resource_usage["tokens"]["by_model"][model_id] = {
                            "input": 0, "output": 0
                        }
                    
                    self.resource_usage["tokens"]["by_model"][model_id]["input"] += input_tokens
                    self.resource_usage["tokens"]["by_model"][model_id]["output"] += output_tokens
                    
                    # コスト計算（モデル別単価に基づく）
                    cost = self._calculate_cost(model_id, input_tokens, output_tokens)
                    self.resource_usage["costs"]["total"] += cost
                    
                    if model_id not in self.resource_usage["costs"]["by_model"]:
                        self.resource_usage["costs"]["by_model"][model_id] = 0.0
                    
                    self.resource_usage["costs"]["by_model"][model_id] += cost
                
                return result
                
            except Exception as e:
                last_error = str(e)
                attempt += 1
                
                # エラータイプに応じて戦略的に待機
                if "rate_limit" in last_error.lower():
                    # レート制限エラーの場合は長めに待機
                    await asyncio.sleep(min(5 * attempt, 30))
                else:
                    # その他のエラーは短く待機
                    await asyncio.sleep(min(1 * attempt, 10))
        
        # 全てのリトライが失敗した場合
        return {
            "error": "retry_exhausted",
            "message": f"Failed after {retry_attempts} attempts. Last error: {last_error}"
        }
    
    def _calculate_cost(self, model_id: str, input_tokens: int, output_tokens: int) -> float:
        """モデルとトークン数からコストを計算"""
        # 各モデルの単価設定（1000トークンあたりのUSD）
        pricing = {
            "gpt-4o": {"input": 5.0 / 1000, "output": 15.0 / 1000},
            "gpt-4o-mini": {"input": 0.15 / 1000, "output": 0.6 / 1000},
            "gpt-3.5-turbo": {"input": 0.5 / 1000, "output": 1.5 / 1000},
            "claude-3-sonnet": {"input": 3.0 / 1000, "output": 15.0 / 1000},
            "claude-3-haiku": {"input": 0.25 / 1000, "output": 1.25 / 1000},
            "gemini-1.5-pro": {"input": 3.5 / 1000, "output": 10.0 / 1000},
            "default": {"input": 1.0 / 1000, "output": 5.0 / 1000},
        }
        
        # モデルの価格設定を取得（存在しない場合はデフォルト）
        model_pricing = pricing.get(model_id, pricing["default"])
        
        # コスト計算
        input_cost = input_tokens * model_pricing["input"]
        output_cost = output_tokens * model_pricing["output"]
        
        return input_cost + output_cost
    
    def get_resource_usage_report(self) -> Dict[str, Any]:
        """リソース使用状況レポートを生成"""
        now = time.time()
        
        # 現在のレート計算
        current_rates = {}
        for model_id in self.rate_limits.keys():
            # 直近1分間のリクエスト数
            recent_requests = [
                ts for ts in self._request_timestamps.get(model_id, [])
                if now - ts < 60
            ]
            current_rpm = len(recent_requests)
            
            # 直近1分間のトークン数
            recent_tokens = [
                count for ts, count in self._token_timestamps.get(model_id, [])
                if now - ts < 60
            ]
            current_tpm = sum(recent_tokens)
            
            current_rates[model_id] = {
                "rpm": current_rpm,
                "tpm": current_tpm
            }
        
        # キューの状況
        with self._queue_lock:
            queue_size = len(self._request_queue)
            oldest_request = min([data[1] for data in self._request_queue]) if self._request_queue else None
            queue_age = now - oldest_request if oldest_request else 0
        
        # 詳細レポートを生成
        return {
            "resource_usage": self.resource_usage,
            "current_rates": current_rates,
            "queue_status": {
                "size": queue_size,
                "oldest_request_age_seconds": queue_age
            },
            "rate_limits": self.rate_limits,
            "timestamp": datetime.now().isoformat()
        }
```


## 8. AIシステムのモニタリングと観測可能性 

### 8.1 リアルタイムモニタリングシステム 

```python
# backend/app/services/monitoring.py
import asyncio
import time
from datetime import datetime, timedelta
from typing import Dict, Any, List, Set, Optional, Tuple
import json
import statistics
import uuid

class AIMonitoringSystem:
    """AIシステムの総合的なリアルタイムモニタリングを提供するサービス"""
    
    def __init__(self, storage_service, notification_service=None):
        self.storage_service = storage_service
        self.notification_service = notification_service
        
        # モニタリング設定
        self.config = {
            "sampling_rate": 1.0,  # サンプリング率 (0.0-1.0)
            "retention_period_days": 30,  # データ保持期間（日数）
            "threshold_alerts": {
                "latency_p95_ms": 2000,     # 95パーセンタイルレイテンシしきい値（ms）
                "error_rate": 0.05,         # エラー率しきい値 (0.0-1.0)
                "token_usage_spike": 2.0,   # 通常の使用量に対する倍率のトークン使用量スパイク
                "model_drift_threshold": 0.2  # モデルドリフト検出しきい値
            }
        }
        
        # メトリクス格納
        self.metrics = {
            "requests": [],               # リクエストメトリクス
            "latencies": {},              # モデル/エンドポイント別レイテンシ
            "error_rates": {},            # モデル/エンドポイント別エラー率
            "token_usage": {},            # モデル別トークン使用量
            "drift_scores": {},           # モデルドリフトスコア
            "endpoint_health": {}         # エンドポイント健全性
        }
        
        # アラート追跡
        self.active_alerts = {}           # 現在アクティブなアラート
        self.alert_history = []           # アラート履歴
        
        # バックグラウンドタスク
        self.monitoring_tasks = []
        self._shutdown_flag = False
    
    async def start_monitoring(self):
        """モニタリングシステムを起動"""
        # 定期的な監視タスクを開始
        self.monitoring_tasks = [
            asyncio.create_task(self._run_metrics_aggregation()),
            asyncio.create_task(self._run_alert_checks()),
            asyncio.create_task(self._run_data_cleanup())
        ]
        
        print(f"AIモニタリングシステムを起動しました (サンプリング率: {self.config['sampling_rate'] * 100}%)")
    
    async def stop_monitoring(self):
        """モニタリングシステムを停止"""
        self._shutdown_flag = True
        
        # タスクをキャンセル
        for task in self.monitoring_tasks:
            task.cancel()
        
        # すべてのタスクが完了するのを待機
        if self.monitoring_tasks:
            await asyncio.gather(*self.monitoring_tasks, return_exceptions=True)
        
        self.monitoring_tasks = []
        print("AIモニタリングシステムを停止しました")
    
    async def track_request(self, request_data: Dict[str, Any]) -> str:
        """
        AIリクエストを追跡
        
        Args:
            request_data: リクエスト情報 {model_id, endpoint, prompt, parameters, etc.}
            
        Returns:
            トラッキングID
        """
        # サンプリング - 設定率に基づいて一部のリクエストのみ記録
        import random
        if random.random() > self.config["sampling_rate"]:
            return ""  # サンプリングから除外
        
        # トラッキングIDを生成
        tracking_id = str(uuid.uuid4())
        
        # リクエスト開始時間を記録
        start_time = time.time()
        
        # リクエストメタデータを設定
        tracking_data = {
            "tracking_id": tracking_id,
            "model_id": request_data.get("model_id", "unknown"),
            "endpoint": request_data.get("endpoint", "default"),
            "timestamp": datetime.now().isoformat(),
            "input_tokens": self._estimate_tokens(request_data.get("prompt", "")),
            "parameters": request_data.get("parameters", {}),
            "start_time": start_time,
            "status": "started"
        }
        
        # 追跡データを保存
        await self.storage_service.store_request_tracking(tracking_id, tracking_data)
        
        return tracking_id
    
    async def complete_request_tracking(
        self, 
        tracking_id: str,
        response_data: Dict[str, Any],
        error: Optional[str] = None
    ):
        """
        AIリクエストの追跡を完了
        
        Args:
            tracking_id: トラッキングID
            response_data: レスポンスデータ
            error: エラーメッセージ（あれば）
        """
        if not tracking_id:
            return  # サンプリングから除外されたリクエスト
        
        # 既存の追跡データを取得
        tracking_data = await self.storage_service.get_request_tracking(tracking_id)
        if not tracking_data:
            print(f"警告: トラッキングID {tracking_id} の追跡データが見つかりません")
            return
        
        # 完了時間とレイテンシを計算
        end_time = time.time()
        latency_ms = (end_time - tracking_data.get("start_time", end_time)) * 1000
        
        # レスポンスメタデータを設定
        tracking_data.update({
            "end_time": end_time,
            "latency_ms": latency_ms,
            "status": "error" if error else "completed",
            "error": error,
            "output_tokens": self._estimate_tokens(response_data.get("text", "")) if not error else 0,
            "total_tokens": tracking_data.get("input_tokens", 0) + 
                           (self._estimate_tokens(response_data.get("text", "")) if not error else 0)
        })
        
        # 使用量情報があれば追加
        if "usage" in response_data:
            tracking_data["token_usage"] = response_data["usage"]
        
        # 追跡データを更新
        await self.storage_service.update_request_tracking(tracking_id, tracking_data)
        
        # メトリクスに追加
        self._add_to_metrics(tracking_data)
    
    async def get_real_time_metrics(self, time_window: int = 60) -> Dict[str, Any]:
        """
        リアルタイムメトリクスを取得
        
        Args:
            time_window: メトリクスのウィンドウ（秒）
            
        Returns:
            リアルタイムメトリクス
        """
        now = time.time()
        cutoff = now - time_window
        
        # 指定時間内のリクエストを取得
        recent_requests = await self.storage_service.get_recent_requests(
            end_time=now, 
            start_time=cutoff
        )
        
        # メトリクスの計算
        metrics = self._calculate_metrics(recent_requests)
        
        # アクティブなアラートを追加
        metrics["active_alerts"] = list(self.active_alerts.values())
        
        return metrics
    
    async def get_historical_metrics(
        self,
        start_time: datetime,
        end_time: datetime = None,
        interval: str = "hour"
    ) -> Dict[str, Any]:
        """
        履歴メトリクスを取得
        
        Args:
            start_time: 開始時間
            end_time: 終了時間（デフォルトは現在）
            interval: 集計間隔 ("minute", "hour", "day")
            
        Returns:
            履歴メトリクス
        """
        if end_time is None:
            end_time = datetime.now()
        
        # 期間内のリクエストを取得
        historical_requests = await self.storage_service.get_requests_in_range(
            start_time=start_time.timestamp(),
            end_time=end_time.timestamp()
        )
        
        # 指定間隔でグループ化したメトリクス
        result = self._group_metrics_by_interval(historical_requests, start_time, end_time, interval)
        
        # アラート履歴を追加
        result["alert_history"] = [
            alert for alert in self.alert_history
            if start_time <= datetime.fromisoformat(alert["timestamp"]) <= end_time
        ]
        
        return result
    
    async def add_custom_metric(self, metric_name: str, value: Any, labels: Dict[str, str] = None):
        """
        カスタムメトリクスを追加
        
        Args:
            metric_name: メトリクス名
            value: メトリクス値
            labels: メトリックラベル
        """
        custom_metric = {
            "name": metric_name,
            "value": value,
            "timestamp": datetime.now().isoformat(),
            "labels": labels or {}
        }
        
        # カスタムメトリクスを保存
        await self.storage_service.store_custom_metric(custom_metric)
    
    async def reset_alert(self, alert_id: str):
        """
        アクティブなアラートをリセット
        
        Args:
            alert_id: アラートID
        """
        if alert_id in self.active_alerts:
            alert = self.active_alerts[alert_id]
            alert["resolved"] = True
            alert["resolved_timestamp"] = datetime.now().isoformat()
            
            # アラート履歴に移動
            self.alert_history.append(alert)
            
            # アクティブリストから削除
            del self.active_alerts[alert_id]
            
            # ストレージに更新を保存
            await self.storage_service.update_alert(alert_id, alert)
    
    def _add_to_metrics(self, tracking_data: Dict[str, Any]):
        """追跡データをメトリクスに追加"""
        # リクエストを記録
        self.metrics["requests"].append(tracking_data)
        
        # レイテンシを記録
        model_id = tracking_data.get("model_id", "unknown")
        endpoint = tracking_data.get("endpoint", "default")
        key = f"{model_id}:{endpoint}"
        
        if "latency_ms" in tracking_data:
            if key not in self.metrics["latencies"]:
                self.metrics["latencies"][key] = []
            self.metrics["latencies"][key].append(tracking_data["latency_ms"])
        
        # エラーを記録
        has_error = tracking_data.get("status") == "error"
        if key not in self.metrics["error_rates"]:
            self.metrics["error_rates"][key] = {"total": 0, "errors": 0}
        
        self.metrics["error_rates"][key]["total"] += 1
        if has_error:
            self.metrics["error_rates"][key]["errors"] += 1
        
        # トークン使用量を記録
        if "token_usage" in tracking_data or "total_tokens" in tracking_data:
            if model_id not in self.metrics["token_usage"]:
                self.metrics["token_usage"][model_id] = {
                    "input": 0, "output": 0, "total": 0, "requests": 0
                }
            
            if "token_usage" in tracking_data:
                usage = tracking_data["token_usage"]
                self.metrics["token_usage"][model_id]["input"] += usage.get("prompt_tokens", 0)
                self.metrics["token_usage"][model_id]["output"] += usage.get("completion_tokens", 0)
                self.metrics["token_usage"][model_id]["total"] += usage.get("total_tokens", 0)
            elif "total_tokens" in tracking_data:
                self.metrics["token_usage"][model_id]["total"] += tracking_data["total_tokens"]
                self.metrics["token_usage"][model_id]["input"] += tracking_data.get("input_tokens", 0)
                self.metrics["token_usage"][model_id]["output"] += tracking_data.get("output_tokens", 0)
            
            self.metrics["token_usage"][model_id]["requests"] += 1
    
    async def _run_metrics_aggregation(self):
        """定期的なメトリクス集計バックグラウンドタスク"""
        try:
            while not self._shutdown_flag:
                # メモリ内メトリクスの集計と永続化
                await self._aggregate_and_persist_metrics()
                
                # エンドポイント健全性チェック
                await self._check_endpoint_health()
                
                # モデルドリフト検出
                await self._check_model_drift()
                
                # 30秒待機
                await asyncio.sleep(30)
        except asyncio.CancelledError:
            print("メトリクス集計タスクがキャンセルされました")
        except Exception as e:
            print(f"メトリクス集計エラー: {e}")
    
    async def _run_alert_checks(self):
        """定期的なアラートチェックバックグラウンドタスク"""
        try:
            while not self._shutdown_flag:
                # アラート条件をチェック
                await self._check_alert_conditions()
                
                # 15秒待機
                await asyncio.sleep(15)
        except asyncio.CancelledError:
            print("アラートチェックタスクがキャンセルされました")
        except Exception as e:
            print(f"アラートチェックエラー: {e}")
    
    async def _run_data_cleanup(self):
        """定期的なデータクリーンアップバックグラウンドタスク"""
        try:
            while not self._shutdown_flag:
                # 古いデータをクリーンアップ
                cutoff_date = datetime.now() - timedelta(days=self.config["retention_period_days"])
                await self.storage_service.cleanup_old_data(cutoff_date.timestamp())
                
                # 古いアラート履歴をクリーンアップ
                self.alert_history = [
                    alert for alert in self.alert_history
                    if datetime.fromisoformat(alert["timestamp"]) > cutoff_date
                ]
                
                # 6時間（21600秒）待機
                await asyncio.sleep(21600)
        except asyncio.CancelledError:
            print("データクリーンアップタスクがキャンセルされました")
        except Exception as e:
            print(f"データクリーンアップエラー: {e}")
    
    async def _aggregate_and_persist_metrics(self):
        """メトリクスの集計と永続化"""
        # 過去5分のメトリクスを集計
        time_window = 300  # 5分
        now = time.time()
        cutoff = now - time_window
        
        # リクエスト配列を時間でフィルタリング
        self.metrics["requests"] = [
            req for req in self.metrics["requests"]
            if req.get("start_time", now) >= cutoff
        ]
        
        # レイテンシ配列を時間でフィルタリング
        for key in self.metrics["latencies"]:
            # ここでは単純化のため、すべてのレイテンシを保持
            # 実際の実装では時間別にフィルタリングする必要がある
            if len(self.metrics["latencies"][key]) > 1000:
                # 最新の1000サンプルのみ保持
                self.metrics["latencies"][key] = self.metrics["latencies"][key][-1000:]
        
        # 集計メトリクスを計算
        aggregated = {
            "timestamp": datetime.now().isoformat(),
            "time_window_seconds": time_window,
            "request_count": len(self.metrics["requests"]),
            "latency_stats": self._calculate_latency_stats(),
            "error_rates": self._calculate_error_rates(),
            "token_usage": self.metrics["token_usage"],
            "endpoint_health": self.metrics["endpoint_health"]
        }
        
        # 集計メトリクスを保存
        await self.storage_service.store_aggregated_metrics(aggregated)
    
    async def _check_endpoint_health(self):
        """エンドポイント健全性のチェック"""
        # エンドポイントをチェック（実際の実装ではヘルスチェックを実施）
        endpoints = set()
        
        # メトリクスからエンドポイントを収集
        for tracking_data in self.metrics["requests"]:
            endpoint = tracking_data.get("endpoint", "default")
            endpoints.add(endpoint)
        
        # 各エンドポイントの健全性をチェック
        for endpoint in endpoints:
            # ここでは簡易的にエラー率に基づく健全性評価を実装
            health_status = "healthy"
            error_rate = 0.0
            
            # エンドポイントのエラー率を計算
            for key, data in self.metrics["error_rates"].items():
                if key.endswith(f":{endpoint}") and data["total"] > 0:
                    error_rate = data["errors"] / data["total"]
                    if error_rate > 0.1:  # 10%以上のエラー率を異常と判断
                        health_status = "degraded"
                    if error_rate > 0.3:  # 30%以上のエラー率を重大と判断
                        health_status = "critical"
            
            # 健全性ステータスを更新
            self.metrics["endpoint_health"][endpoint] = {
                "status": health_status,
                "error_rate": error_rate,
                "checked_at": datetime.now().isoformat()
            }
    
    async def _check_model_drift(self):
        """モデルドリフトの検出"""
        # モデルドリフト検出は実装が複雑なため、ここでは簡易的な代替処理を実装
        # 実際の実装では、モデル出力の分布変化やエラーパターンの変化を分析
        
        # ベースライン分布（正常時の動作パターン）
        # 実際の実装では、このデータは事前に計算して保存しておく
        baselines = {
            "gpt-4o": {
                "avg_latency_ms": 1200,
                "error_rate": 0.02,
                "tokens_per_request": 1500
            },
            "claude-3-sonnet": {
                "avg_latency_ms": 1500,
                "error_rate": 0.03,
                "tokens_per_request": 1800
            }
        }
        
        # 各モデルのドリフトを計算
        for model_id in self.metrics["token_usage"].keys():
            if model_id not in baselines:
                continue
            
            baseline = baselines[model_id]
            current_metrics = {}
            
            # 現在の平均レイテンシを計算
            latencies = []
            for key, values in self.metrics["latencies"].items():
                if key.startswith(f"{model_id}:"):
                    latencies.extend(values)
            
            if latencies:
                current_metrics["avg_latency_ms"] = sum(latencies) / len(latencies)
            
            # 現在のエラー率を計算
            total_requests = 0
            total_errors = 0
            for key, data in self.metrics["error_rates"].items():
                if key.startswith(f"{model_id}:"):
                    total_requests += data["total"]
                    total_errors += data["errors"]
            
            if total_requests > 0:
                current_metrics["error_rate"] = total_errors / total_requests
            
            # トークン使用量を計算
            usage = self.metrics["token_usage"].get(model_id, {})
            if usage.get("requests", 0) > 0:
                current_metrics["tokens_per_request"] = usage.get("total", 0) / usage["requests"]
            
            # ドリフトスコアの計算（単純な偏差の合計）
            if current_metrics:
                drift_score = 0.0
                count = 0
                
                if "avg_latency_ms" in current_metrics and baseline["avg_latency_ms"] > 0:
                    latency_drift = abs(current_metrics["avg_latency_ms"] - baseline["avg_latency_ms"]) / baseline["avg_latency_ms"]
                    drift_score += min(latency_drift, 1.0)  # 最大1.0に制限
                    count += 1
                
                if "error_rate" in current_metrics and baseline["error_rate"] > 0:
                    # エラー率は重み付けを高くする
                    error_drift = abs(current_metrics["error_rate"] - baseline["error_rate"]) / max(0.01, baseline["error_rate"])
                    drift_score += min(error_drift * 2.0, 1.0)  # 重み2倍、最大1.0
                    count += 1
                
                if "tokens_per_request" in current_metrics and baseline["tokens_per_request"] > 0:
                    token_drift = abs(current_metrics["tokens_per_request"] - baseline["tokens_per_request"]) / baseline["tokens_per_request"]
                    drift_score += min(token_drift, 1.0)  # 最大1.0に制限
                    count += 1
                
                # 平均ドリフトスコアを計算
                if count > 0:
                    avg_drift = drift_score / count
                    self.metrics["drift_scores"][model_id] = {
                        "score": avg_drift,
                        "timestamp": datetime.now().isoformat(),
                        "metrics_compared": list(current_metrics.keys())
                    }
    
    async def _check_alert_conditions(self):
        """アラート条件のチェック"""
        alerts = []
        now = datetime.now()
        
        # レイテンシアラート
        for key, latencies in self.metrics["latencies"].items():
            if not latencies:
                continue
                
            model_id, endpoint = key.split(":")
            
            # 95パーセンタイルのレイテンシを計算
            if len(latencies) >= 10:  # 十分なサンプル数がある場合のみ
                sorted_latencies = sorted(latencies)
                p95_index = int(len(sorted_latencies) * 0.95)
                p95_latency = sorted_latencies[p95_index]
                
                if p95_latency > self.config["threshold_alerts"]["latency_p95_ms"]:
                    alert_id = f"latency:{model_id}:{endpoint}:{now.strftime('%Y%m%d%H')}"
                    
                    # まだ同じアラートがアクティブでない場合のみ追加
                    if alert_id not in self.active_alerts:
                        alert = {
                            "id": alert_id,
                            "type": "high_latency",
                            "severity": "warning",
                            "message": f"高いレイテンシが検出されました: {model_id} on {endpoint}",
                            "details": {
                                "model_id": model_id,
                                "endpoint": endpoint,
                                "p95_latency_ms": p95_latency,
                                "threshold_ms": self.config["threshold_alerts"]["latency_p95_ms"],
                                "sample_size": len(latencies)
                            },
                            "timestamp": now.isoformat(),
                            "resolved": False
                        }
                        
                        alerts.append(alert)
        
        # エラー率アラート
        for key, data in self.metrics["error_rates"].items():
            if data["total"] < 10:  # 十分なサンプル数がある場合のみ
                continue
                
            model_id, endpoint = key.split(":")
            error_rate = data["errors"] / data["total"]
            
            if error_rate > self.config["threshold_alerts"]["error_rate"]:
                alert_id = f"error_rate:{model_id}:{endpoint}:{now.strftime('%Y%m%d%H')}"
                
                # まだ同じアラートがアクティブでない場合のみ追加
                if alert_id not in self.active_alerts:
                    alert = {
                        "id": alert_id,
                        "type": "high_error_rate",
                        "severity": "critical" if error_rate > 0.2 else "warning",
                        "message": f"高いエラー率が検出されました: {model_id} on {endpoint}",
                        "details": {
                            "model_id": model_id,
                            "endpoint": endpoint,
                            "error_rate": error_rate,
                            "threshold": self.config["threshold_alerts"]["error_rate"],
                            "total_requests": data["total"],
                            "error_count": data["errors"]
                        },
                        "timestamp": now.isoformat(),
                        "resolved": False
                    }
                    
                    alerts.append(alert)
        
        # モデルドリフトアラート
        for model_id, drift_data in self.metrics["drift_scores"].items():
            drift_score = drift_data.get("score", 0)
            
            if drift_score > self.config["threshold_alerts"]["model_drift_threshold"]:
                alert_id = f"drift:{model_id}:{now.strftime('%Y%m%d%H')}"
                
                # まだ同じアラートがアクティブでない場合のみ追加
                if alert_id not in self.active_alerts:
                    alert = {
                        "id": alert_id,
                        "type": "model_drift",
                        "severity": "warning",
                        "message": f"モデルドリフトが検出されました: {model_id}",
                        "details": {
                            "model_id": model_id,
                            "drift_score": drift_score,
                            "threshold": self.config["threshold_alerts"]["model_drift_threshold"],
                            "metrics_compared": drift_data.get("metrics_compared", [])
                        },
                        "timestamp": now.isoformat(),
                        "resolved": False
                    }
                    
                    alerts.append(alert)
        
        # アラートを登録
        for alert in alerts:
            alert_id = alert["id"]
            self.active_alerts[alert_id] = alert
            
            # アラートを保存
            await self.storage_service.store_alert(alert_id, alert)
            
            # 通知を送信（設定されていれば）
            if self.notification_service:
                await self.notification_service.send_alert(alert)
    
    def _calculate_metrics(self, requests: List[Dict[str, Any]]) -> Dict[str, Any]:
        """リクエストリストからメトリクスを計算"""
        # 基本カウント
        total_requests = len(requests)
        if total_requests == 0:
            return {
                "request_count": 0,
                "timestamp": datetime.now().isoformat()
            }
        
        # モデル別のグループ化
        models = {}
        endpoints = {}
        errors = 0
        total_latency = 0
        latencies = []
        
        for req in requests:
            model_id = req.get("model_id", "unknown")
            endpoint = req.get("endpoint", "default")
            
            # モデル統計
            if model_id not in models:
                models[model_id] = {"requests": 0, "errors": 0, "tokens": 0}
            models[model_id]["requests"] += 1
            
            # エンドポイント統計
            endpoint_key = f"{model_id}:{endpoint}"
            if endpoint_key not in endpoints:
                endpoints[endpoint_key] = {"requests": 0, "errors": 0}
            endpoints[endpoint_key]["requests"] += 1
            
            # エラー統計
            if req.get("status") == "error":
                errors += 1
                models[model_id]["errors"] += 1
                endpoints[endpoint_key]["errors"] += 1
            
            # レイテンシ統計
            if "latency_ms" in req:
                latency = req["latency_ms"]
                latencies.append(latency)
                total_latency += latency
            
            # トークン統計
            if "total_tokens" in req:
                models[model_id]["tokens"] += req["total_tokens"]
        
        # 統計計算
        avg_latency = total_latency / len(latencies) if latencies else 0
        p95_latency = None
        p99_latency = None
        
        if latencies:
            sorted_latencies = sorted(latencies)
            p95_index = int(len(sorted_latencies) * 0.95)
            p99_index = int(len(sorted_latencies) * 0.99)
            p95_latency = sorted_latencies[p95_index]
            p99_latency = sorted_latencies[p99_index]
        
        # レスポンスメトリクスの構築
        return {
            "request_count": total_requests,
            "error_count": errors,
            "error_rate": errors / total_requests if total_requests > 0 else 0,
            "latency_stats": {
                "avg_ms": avg_latency,
                "p95_ms": p95_latency,
                "p99_ms": p99_latency,
                "min_ms": min(latencies) if latencies else None,
                "max_ms": max(latencies) if latencies else None
            },
            "models": models,
            "endpoints": endpoints,
            "timestamp": datetime.now().isoformat()
        }
    
    def _calculate_latency_stats(self) -> Dict[str, Any]:
        """レイテンシ統計を計算"""
        result = {}
        
        for key, latencies in self.metrics["latencies"].items():
            if not latencies:
                continue
            
            # 基本統計量
            avg_latency = sum(latencies) / len(latencies)
            
            # パーセンタイル計算
            if len(latencies) >= 10:  # 十分なサンプル数がある場合のみ
                sorted_latencies = sorted(latencies)
                p50_index = int(len(sorted_latencies) * 0.5)
                p90_index = int(len(sorted_latencies) * 0.9)
                p95_index = int(len(sorted_latencies) * 0.95)
                p99_index = int(len(sorted_latencies) * 0.99)
                
                result[key] = {
                    "count": len(latencies),
                    "avg_ms": avg_latency,
                    "min_ms": sorted_latencies[0],
                    "max_ms": sorted_latencies[-1],
                    "p50_ms": sorted_latencies[p50_index],
                    "p90_ms": sorted_latencies[p90_index],
                    "p95_ms": sorted_latencies[p95_index],
                    "p99_ms": sorted_latencies[p99_index],
                    "std_dev": statistics.stdev(latencies) if len(latencies) > 1 else 0
                }
            else:
                result[key] = {
                    "count": len(latencies),
                    "avg_ms": avg_latency,
                    "min_ms": min(latencies),
                    "max_ms": max(latencies)
                }
        
        return result
    
    def _calculate_error_rates(self) -> Dict[str, Any]:
        """エラー率統計を計算"""
        result = {}
        
        for key, data in self.metrics["error_rates"].items():
            if data["total"] > 0:
                result[key] = {
                    "error_rate": data["errors"] / data["total"],
                    "error_count": data["errors"],
                    "total_requests": data["total"]
                }
        
        return result
    
    def _group_metrics_by_interval(
        self,
        requests: List[Dict[str, Any]],
        start_time: datetime,
        end_time: datetime,
        interval: str
    ) -> Dict[str, Any]:
        """リクエストを時間間隔でグループ化してメトリクスを計算"""
        # 間隔ごとのリクエストをグループ化
        grouped_requests = {}
        
        # 間隔に基づいてフォーマット文字列を選択
        if interval == "minute":
            fmt = "%Y-%m-%d %H:%M"
            delta = timedelta(minutes=1)
        elif interval == "hour":
            fmt = "%Y-%m-%d %H"
            delta = timedelta(hours=1)
        else:  # default to day
            fmt = "%Y-%m-%d"
            interval = "day"
            delta = timedelta(days=1)
        
        # 各時間間隔のキーを生成
        current = start_time
        while current <= end_time:
            key = current.strftime(fmt)
            grouped_requests[key] = []
            current += delta
        
        # リクエストを対応する間隔に割り当て
        for req in requests:
            timestamp = datetime.fromtimestamp(req.get("start_time", 0))
            key = timestamp.strftime(fmt)
            
            if key in grouped_requests:
                grouped_requests[key].append(req)
        
        # 各間隔のメトリクスを計算
        interval_metrics = {}
        
        for key, reqs in grouped_requests.items():
            interval_metrics[key] = self._calculate_metrics(reqs)
        
        return {
            "interval": interval,
            "metrics_by_interval": interval_metrics,
            "start_time": start_time.isoformat(),
            "end_time": end_time.isoformat()
        }
    
    def _estimate_tokens(self, text: str) -> int:
        """テキストのトークン数を概算（簡易実装）"""
        if not text:
            return 0
        
        # 簡易的な推定: 英語では約4文字で1トークン
        return len(text) // 4 + 1

# ストレージと通知サービスのインターフェース例（実際の実装は省略）
class MetricsStorageService:
    async def store_request_tracking(self, tracking_id: str, data: Dict[str, Any]):
        # 実際にはSupabase/FirebaseやRedisに保存
        print(f"Storing tracking data for {tracking_id}: {json.dumps(data, indent=2, ensure_ascii=False)}")
        pass
    
    async def get_request_tracking(self, tracking_id: str) -> Dict[str, Any]:
        # 実際にはSupabase/FirebaseやRedisから取得
        print(f"Getting tracking data for {tracking_id}")
        return {} # ダミー
    
    async def update_request_tracking(self, tracking_id: str, data: Dict[str, Any]):
        # 実際にはSupabase/FirebaseやRedisのデータを更新
        print(f"Updating tracking data for {tracking_id}: {json.dumps(data, indent=2, ensure_ascii=False)}")
        pass
    
    async def get_recent_requests(self, end_time: float, start_time: float) -> List[Dict[str, Any]]:
        # 実際にはデータベースからクエリ
        print(f"Getting recent requests from {datetime.fromtimestamp(start_time)} to {datetime.fromtimestamp(end_time)}")
        return [] # ダミー
    
    async def get_requests_in_range(self, start_time: float, end_time: float) -> List[Dict[str, Any]]:
        # 実際にはデータベースからクエリ
        print(f"Getting requests in range from {datetime.fromtimestamp(start_time)} to {datetime.fromtimestamp(end_time)}")
        return [] # ダミー
    
    async def store_aggregated_metrics(self, metrics: Dict[str, Any]):
        # 実際には時系列DBや分析ストアに保存
        print(f"Storing aggregated metrics: {json.dumps(metrics, indent=2, ensure_ascii=False)}")
        pass
    
    async def store_custom_metric(self, metric: Dict[str, Any]):
        # 実際にはメトリクスストアに保存
        print(f"Storing custom metric: {json.dumps(metric, indent=2, ensure_ascii=False)}")
        pass
    
    async def store_alert(self, alert_id: str, alert_data: Dict[str, Any]):
        # 実際にはアラートDBやログに保存
        print(f"Storing alert {alert_id}: {json.dumps(alert_data, indent=2, ensure_ascii=False)}")
        pass
    
    async def update_alert(self, alert_id: str, alert_data: Dict[str, Any]):
        # アラートDBの情報を更新
        print(f"Updating alert {alert_id}: {json.dumps(alert_data, indent=2, ensure_ascii=False)}")
        pass
    
    async def cleanup_old_data(self, cutoff_timestamp: float):
        # データベースから古いデータを削除
        print(f"Cleaning up data older than {datetime.fromtimestamp(cutoff_timestamp)}")
        pass

class NotificationService:
    async def send_alert(self, alert: Dict[str, Any]):
        # 実際にはメール、Slackなどに通知
        print(f"Sending alert notification: {alert['message']}")
        pass
```

### 8.2 ログ分析とデバッグフレームワーク

AIシステムの動作を詳細に追跡し、問題発生時の原因究明を支援するためのログ分析とデバッグフレームワークを構築します。

#### 8.2.1 構造化ログの標準

すべてのAIコンポーネントと関連サービスは、一貫した構造化ログ形式（JSON推奨）でログを出力します。
これにより、ログの集約、検索、分析が容易になります。

```python
# backend/app/core/logging_config.py
import logging
import sys
import json
from pythonjsonlogger import jsonlogger
import uuid

# アプリケーション全体の相関IDを管理するためのコンテキスト変数
# from contextvars import ContextVar
# correlation_id_var: ContextVar[str] = ContextVar("correlation_id", default=None)
# (FastAPIのミドルウェアなどで設定想定)

class CustomJsonFormatter(jsonlogger.JsonFormatter):
    def add_fields(self, log_record, record, message_dict):
        super(CustomJsonFormatter, self).add_fields(log_record, record, message_dict)
        
        # タイムスタンプをISO形式に
        if not log_record.get('timestamp'):
            log_record['timestamp'] = datetime.fromtimestamp(record.created).isoformat()
        
        # ログレベル名を大文字に
        log_record['level'] = record.levelname
        
        # モジュール情報
        log_record['module'] = record.module
        log_record['funcName'] = record.funcName
        log_record['lineno'] = record.lineno
        
        # 相関ID (もしあれば)
        # correlation_id = correlation_id_var.get()
        # if correlation_id:
        #     log_record['correlation_id'] = correlation_id
            
        # エラー情報
        if record.exc_info:
            log_record['exc_info'] = self.formatException(record.exc_info)
            log_record['exc_type'] = record.exc_info[0].__name__
            log_record['exc_msg'] = str(record.exc_info[1])

def setup_logging(log_level: str = "INFO"):
    """
    アプリケーションのロギングを設定
    """
    logger = logging.getLogger()
    logger.setLevel(log_level.upper())
    
    # 既存のハンドラをクリア
    for handler in logger.handlers[:]:
        logger.removeHandler(handler)
    
    # JSON形式のログハンドラ
    log_handler = logging.StreamHandler(sys.stdout)
    formatter = CustomJsonFormatter(
        '%(timestamp)s %(level)s %(name)s %(module)s %(funcName)s %(lineno)d %(message)s'
    )
    log_handler.setFormatter(formatter)
    logger.addHandler(log_handler)
    
    # Uvicornアクセスログを無効化（FastAPIの場合）
    # logging.getLogger("uvicorn.access").handlers = []
    # logging.getLogger("uvicorn.access").propagate = False
    
    logging.info("Logging setup complete.")

# FastAPIアプリケーションの起動時に呼び出す
# setup_logging()

# 使用例:
# logger = logging.getLogger(__name__)
# logger.info("これは情報ログです", extra={"key": "value", "user_id": "123"})
# try:
#     1/0
# except ZeroDivisionError:
#     logger.error("ゼロ除算エラーが発生しました", exc_info=True)
```

**構造化ログに含めるべき共通フィールド例*

*   `timestamp`: ISO 8601 形式のタイムスタンプ
*   `level`: ログレベル (DEBUG, INFO, WARNING, ERROR, CRITICAL)
*   `message`: ログメッセージ本文
*   `service_name`: ログを出力したサービス名 (例: `hrx-ai-backend`, `rag-service`)
*   `module`: ログを出力したモジュール名
*   `funcName`: ログを出力した関数名
*   `lineno`: ログを出力した行番号
*   `correlation_id`: リクエスト全体を追跡するためのID (OpenTelemetryのTrace IDと連携)
*   `request_id`: 個別のリクエストID
*   `user_id`: 操作を実行したユーザーID (可能な場合)
*   `model_id`: 使用されたAIモデルのID (該当する場合)
*   `input_data_digest`: 入力データのハッシュまたは一部 (デバッグ用、機密情報マスキング)
*   `output_data_digest`: 出力データのハッシュまたは一部 (デバッグ用、機密情報マスキング)
*   `duration_ms`: 処理時間 (ミリ秒)
*   `error_code`: エラーコード (エラー発生時)
*   `stack_trace`: スタックトレース (エラー発生時)
*   `custom_fields`: 各モジュール固有の追加情報 (例: `prompt_length`, `retrieved_docs_count`)

#### 8.2.2 ログ集約と検索プラットフォーム

収集された構造化ログは、中央のログ管理システムに集約します。
`Development_Specification.md` のインフラスタック (2.5) で `OpenTelemetry` が言及されているため、これと連携可能なバックエンドが推奨されます。

*   **Elastic Stack (ELK)**: Elasticsearch (検索・分析), Logstash (収集・処理), Kibana (可視化)
    *   メリット: 強力な検索・分析機能、豊富な可視化オプション、成熟したエコシステム。
    *   デメリット: 運用コストが高い場合がある。
*   **Grafana Loki**: Grafana との親和性が高く、軽量でコスト効率が良い。
    *   メリット: Prometheus や Grafana との統合が容易、小〜中規模システムに適している。
    *   デメリット: Elasticsearch ほどの高度な検索機能はない。
*   **クラウドプロバイダーのログサービス**: AWS CloudWatch Logs, Google Cloud Logging, Azure Monitor Logs
    *   メリット: マネージドサービスであり運用が容易、クラウド環境との統合が深い。
    *   デメリット: プロバイダーロックインの可能性、コスト構造。

**ログ収集エージェント**

*   **Fluentd / Fluent Bit**: 軽量で高性能なログコレクター。多くのプラグインがあり、様々なデータソースと出力先に対応。
*   **OpenTelemetry Collector**: トレース、メトリクス、ログを収集・処理・エクスポートできる。エコシステムとの親和性が高い。

**ログ分析クエリ例 (Kibana Query Language - KQL)**

*   特定の `correlation_id` を持つすべてのログ: `correlation_id : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`
*   `ERROR` レベルのログをサービス別に集計: `level : ERROR AND service_name : * | stats count by service_name`
*   特定のモデルID (`gpt-4o`) で、処理時間が500msを超えたリクエスト: `model_id : "gpt-4o" AND duration_ms > 500`
*   過去1時間に発生したエラーのトレンド: `level : ERROR AND timestamp > now-1h | date_histogram field=timestamp interval=1m`

#### 8.2.3 AI特有のログとデバッグ情報

AIシステムのデバッグには、標準的なログに加えてAI処理に特化した情報が必要です。

*   **プロンプトとLLMレスポンス:**
    *   最終的にLLMに送信された完全なプロンプト。
    *   LLMからの生のレスポンス。
    *   レスポンスのトークン数、生成時間。
    *   **注意:** 機密情報が含まれる場合はマスキング処理を施す。
*   **RAGプロセスのログ:**
    *   ユーザーの元のクエリ。
    *   クエリ拡張の結果 (もしあれば)。
    *   ベクトル検索に使用された埋め込みベクトルの一部またはハッシュ。
    *   検索されたドキュメントのID、関連度スコア、チャンクの内容のダイジェスト。
    *   コンテキスト構築に使用されたドキュメントとその順序。
*   **予測モデルのログ:**
    *   モデルへの入力フィーチャー (マスキングまたはダイジェスト)。
    *   モデルの予測結果と信頼度スコア。
    *   予測に最も寄与したフィーチャー (SHAP値など、解釈可能なモデルの場合)。
*   **コンテンツセーフティフィルターのログ:**
    *   入力テキストとフィルターの判定結果。
    *   検出された安全でないコンテンツのカテゴリとスコア。
    *   適用されたフィルターの種類。

**デバッグ支援のためのログ戦略*

*   **ステップごとのログ:** AI処理パイプラインの各主要ステップで、入力、中間出力、パラメータをログに記録する。
*   **決定論的なID:** 可能な限り、処理全体を通じて一意のID (例: `tracking_id`) を引き回し、ログに関連付ける。
*   **バージョニング情報:** 使用されたモデルのバージョン、プロンプトテンプレートのバージョン、データソースのバージョンなどをログに含める。
*   **「なぜ」のログ:** AIが特定の判断を下した理由や、特定のデータを選択した根拠をログに出力する (例: RAGでのドキュメント選択基準)。

```python
# backend/app/services/ai_service_example.py
import logging
import uuid

logger = logging.getLogger(__name__)

# (correlation_id_var は logging_config.py からインポート想定)
# from app.core.logging_config import correlation_id_var 

class AIServiceExample:
    def __init__(self, llm_provider, vector_store):
        self.llm_provider = llm_provider
        self.vector_store = vector_store

    async def process_query_with_rag(self, user_query: str, user_id: str, context_params: dict):
        # correlation_id = correlation_id_var.get()
        # if not correlation_id:
        #     correlation_id = str(uuid.uuid4())
        #     correlation_id_var.set(correlation_id)
        # 便宜上、ここではダミーの相関IDを使用します
        correlation_id = str(uuid.uuid4())

        request_id = str(uuid.uuid4())
        log_extra = {
            "correlation_id": correlation_id,
            "request_id": request_id,
            "user_id": user_id,
            "ai_service": "RAGQueryProcessor"
        }

        logger.info(f"RAG処理開始: クエリ='{user_query[:50]}...'", extra=log_extra)

        # 1. クエリ拡張 (省略)
        expanded_query = user_query # ここでは拡張しない
        logger.debug(f"拡張クエリ: '{expanded_query[:50]}...'", extra=log_extra)

        # 2. ベクトル検索
        try:
            retrieved_docs = await self.vector_store.search(
                query=expanded_query,
                top_k=context_params.get("top_k", 3),
                filters=context_params.get("filters", {})
            )
            doc_ids = [doc.id for doc in retrieved_docs]
            logger.info(
                f"{len(retrieved_docs)}件のドキュメントを検索しました: ID={doc_ids}",
                extra={**log_extra, "retrieved_doc_count": len(retrieved_docs), "doc_ids": doc_ids}
            )
        except Exception as e:
            logger.error("ベクトル検索中にエラー発生", exc_info=True, extra=log_extra)
            raise

        # 3. コンテキスト構築
        context_str = "\n".join([doc.content for doc in retrieved_docs])
        logger.debug(f"構築されたコンテキスト長: {len(context_str)}文字", extra=log_extra)

        # 4. プロンプト生成
        final_prompt = f"以下の情報に基づいて質問に答えてください:\n\n情報:\n{context_str}\n\n質問: {user_query}"
        # 注意: 実際のプロンプトは機密情報を含む可能性があるため、ログ出力時はマスキングやダイジェスト化を検討
        logger.info(
            "最終プロンプト生成完了",
            extra={**log_extra, "prompt_length": len(final_prompt), "prompt_digest": hash(final_prompt)}
        )

        # 5. LLM呼び出し
        try:
            llm_response = await self.llm_provider.generate(
                model_id="gpt-4o", # 例
                prompt=final_prompt,
                params={"max_tokens": 500}
            )
            logger.info(
                "LLMからの応答受信完了",
                extra={
                    **log_extra,
                    "llm_response_length": len(llm_response.get("text","")),
                    "llm_token_usage": llm_response.get("usage", {})
                }
            )
            return llm_response.get("text")
        except Exception as e:
            logger.error("LLM呼び出し中にエラー発生", exc_info=True, extra=log_extra)
            raise
```

#### 8.2.4 エラーハンドリングとトラブルシューティング

堅牢なエラーハンドリングと詳細なエラーログは、問題解決の鍵です。

*   **例外処理:** アプリケーション全体で一貫した例外処理戦略を適用します。FastAPIの例外ハンドラを活用。
*   **エラーログの詳細度:** エラー発生時には、スタックトレース、エラーコード、関連するリクエストパラメータ、現在のシステム状態など、可能な限り多くのコンテキスト情報を含めます。
*   **再試行メカニズムとログ:** 一時的なエラー（ネットワークエラー、レート制限など）に対する再試行ロジックを実装し、再試行の試行回数と結果をログに記録します。
*   **ユーザー向けエラーメッセージ:** ユーザーに表示するエラーメッセージは分かりやすく、問題解決の手がかりを与えるものにしつつ、内部的な詳細情報はログにのみ記録します。

```python
# backend/app/api/dependencies.py (または適切なエラーハンドリングミドルウェア)
from fastapi import Request, HTTPException
from fastapi.responses import JSONResponse
import logging
import traceback
import uuid

logger = logging.getLogger(__name__)

async def global_exception_handler(request: Request, exc: Exception):
    # correlation_id = correlation_id_var.get() or str(uuid.uuid4())
    # 便宜上、ここではダミーの相関IDを使用します
    correlation_id = str(uuid.uuid4())

    error_id = str(uuid.uuid4())
    
    # エラー詳細をログに出力
    log_extra = {
        "correlation_id": correlation_id,
        "error_id": error_id,
        "request_url": str(request.url),
        "request_method": request.method,
        "client_host": request.client.host,
        "exception_type": type(exc).__name__,
    }
    
    if isinstance(exc, HTTPException):
        # FastAPIのHTTPExceptionの場合
        logger.error(
            f"HTTPException: {exc.status_code} - {exc.detail}",
            extra={**log_extra, "status_code": exc.status_code, "detail": exc.detail},
            exc_info=(type(exc), exc, exc.__traceback__) # 明示的にトレースバック情報を含める
        )
        return JSONResponse(
            status_code=exc.status_code,
            content={
                "error_id": error_id,
                "message": f"エラーが発生しました。サポートにお問い合わせの際はエラーID ({error_id}) をお伝えください。",
                "detail": exc.detail # 開発環境では詳細を返すことも検討
            },
        )
    else:
        # その他の予期せぬ例外
        logger.critical(
            f"Unhandled Exception: {str(exc)}",
            extra=log_extra,
            exc_info=(type(exc), exc, exc.__traceback__) # 明示的にトレースバック情報を含める
        )
        return JSONResponse(
            status_code=500,
            content={
                "error_id": error_id,
                "message": f"予期せぬサーバーエラーが発生しました。サポートにお問い合わせの際はエラーID ({error_id}) をお伝えください。",
                # "detail": str(exc) # 本番環境では詳細なエラーメッセージを返さない
            },
        )

# FastAPIアプリケーションに登録
# from fastapi import FastAPI
# app = FastAPI()
# app.add_exception_handler(Exception, global_exception_handler)
```

#### 8.2.5 分散トレーシングの活用

`Development_Specification.md` 2.5 で `OpenTelemetry` が指定されているため、これを活用してリクエストが複数のサービスやコンポーネントを通過する際の処理フローを追跡します。

*   **トレーシングコンテキストの伝播**
    *   リクエストがシステムに入った時点で一意のトレースID (Trace ID) とスパンID (Span ID) を生成。
    *   これらのIDを、マイクロサービス間の通信 (HTTPヘッダー、メッセージキューのメタデータなど) を通じて伝播させる。
*   **主要処理のスパン化**
    *   AIパイプラインの各ステップ（RAG検索、LLM呼び出し、後処理など）を個別のスパンとして計測。
    *   各スパンには、処理時間、重要な属性 (例: `model_id`, `prompt_length`)、イベント、エラー情報を含める。
*   **バックエンドの選定**
    *   Jaeger, Zipkin, Grafana Tempo など、OpenTelemetry互換のトレーシングバックエンドを選定。
*   **可視化と分析**
    *   トレースデータを可視化し、リクエスト全体のフロー、各コンポーネントのレイテンシ、ボトルネックを特定。
    *   エラーが発生したリクエストのトレースを分析し、問題の原因箇所を特定。


```python
# backend/app/services/ai_trace_example.py
from opentelemetry import trace
# from opentelemetry.sdk.trace import TracerProvider
# from opentelemetry.sdk.trace.export import BatchSpanProcessor, ConsoleSpanExporter
# from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor

# OpenTelemetryのセットアップ (アプリケーション起動時に一度だけ実行)
# provider = TracerProvider()
# processor = BatchSpanProcessor(ConsoleSpanExporter()) # 実際にはJaegerExporterなどを使用
# provider.add_span_processor(processor)
# trace.set_tracer_provider(provider)

# FastAPIInstrumentor.instrument_app(app) # FastAPIアプリを自動計装

tracer = trace.get_tracer(__name__)

class AITracedService:
    def __init__(self, llm_provider):
        self.llm_provider = llm_provider

    async def traced_rag_pipeline(self, user_query: str, user_id: str):
        # 現在のスパンを取得、または新しいトレースを開始
        with tracer.start_as_current_span("rag_pipeline_request") as parent_span:
            parent_span.set_attribute("user_id", user_id)
            parent_span.set_attribute("query.length", len(user_query))
            
            # 1. 文書検索
            with tracer.start_as_current_span("document_retrieval") as retrieval_span:
                # (文書検索処理)
                await asyncio.sleep(0.1) # ダミー処理
                retrieved_doc_count = 5
                retrieval_span.set_attribute("retrieved_doc_count", retrieved_doc_count)
                retrieval_span.add_event("Search completed", {"engine": "vector_db"})
            
            # 2. プロンプト生成
            with tracer.start_as_current_span("prompt_generation") as prompt_span:
                # (プロンプト生成処理)
                await asyncio.sleep(0.05) # ダミー処理
                final_prompt = f"Query: {user_query}, Context: ..."
                prompt_span.set_attribute("prompt.length", len(final_prompt))
            
            # 3. LLM呼び出し
            with tracer.start_as_current_span("llm_call") as llm_span:
                llm_span.set_attribute("llm.model_id", "gpt-4o")
                try:
                    # (LLM呼び出し処理)
                    # response = await self.llm_provider.generate(...)
                    await asyncio.sleep(0.5) # ダミー処理
                    llm_response_text = "AI Response"
                    llm_span.set_attribute("llm.response_length", len(llm_response_text))
                    llm_span.set_status(trace.Status(trace.StatusCode.OK))
                except Exception as e:
                    llm_span.set_status(trace.Status(trace.StatusCode.ERROR, description=str(e)))
                    llm_span.record_exception(e)
                    raise
            
            # 4. 後処理
            with tracer.start_as_current_span("post_processing") as post_span:
                # (後処理)
                await asyncio.sleep(0.02) # ダミー処理
                post_span.add_event("Formatting applied")
            
            parent_span.set_attribute("result.length", len(llm_response_text))
            return llm_response_text       
```




#### 8.2.6 AIデバッグツールの統合

AI/LLMアプリケーションの開発とデバッグを支援する専用ツールを統合します。

*   **LangSmith (LangChain連携):**
    *   LangChainで構築されたアプリケーションの実行トレース、プロンプト、レスポンス、中間ステップを詳細に可視化。
    *   プロンプトのデバッグ、パフォーマンス分析、エラー特定に非常に有効。
    *   RAGパイプラインの各ステップ（検索、コンテキスト生成、LLM呼び出し）を追跡可能。
    *   A/Bテストやプロンプトバージョニングにも対応。

    ```python
    # LangSmithのセットアップ (環境変数で設定)
    # os.environ["LANGCHAIN_TRACING_V2"] = "true"
    # os.environ["LANGCHAIN_ENDPOINT"] = "https://api.smith.langchain.com"
    # os.environ["LANGCHAIN_API_KEY"] = "YOUR_LANGSMITH_API_KEY"
    # os.environ["LANGCHAIN_PROJECT"] = "HRX-AI-Dev"

    # LangChainのChainやAgentを使用すると自動的にトレースされる
    # from langchain.chains import LLMChain
    # from langchain_openai import ChatOpenAI
    # llm = ChatOpenAI(model="gpt-4o")
    # chain = LLMChain(llm=llm, prompt=...)
    # chain.invoke({"input": "some query"}) # この実行がLangSmithに記録される
    ```

*   **Weights & Biases (W&B):**
    *   実験管理、モデルバージョニング、データセットバージョニング、ハイパーパラメータ最適化などに強力。
    *   カスタムモデルのトレーニングやファインチューニングを行う場合に特に有効。
    *   プロンプトエンジニアリングの実験や評価結果の追跡にも利用可能。

*   **カスタムデバッグエンドポイント:**
    *   特定のAI機能の内部状態や中間結果を確認するためのデバッグ用APIエンドポイントを開発環境限定で用意する。
    *   例: `/debug/rag-trace?query=...` でRAGプロセスの詳細なステップバイステップの情報を返す。

*   **LLM評価フレームワーク (`Development_Specification.md` 2.3):**
    *   モデルの出力品質を客観的に評価するためのフレームワーク (`Ragas` などRAG評価に特化したものや、カスタム評価基準に基づくもの) を利用。
    *   デバッグプロセスの一環として、問題のあるケースに対してこれらの評価を実行し、原因を特定。

これらのログ分析とデバッグフレームワークを組み合わせることで、AIシステムの信頼性を高め、問題発生時の迅速な対応を可能にします。
特にAIアプリケーションは非決定的であったり、予期せぬ挙動をすることがあるため、観測可能性の確保は非常に重要です。

---

## 9. 実装例とユースケース別ガイダンス

このセクションでは、HRX-AIの主要機能におけるAI統合の具体的な実装例とガイダンスをです。各ユースケースは、`Development_Specification.md` の機能モジュール詳細に基づいています。

### 9.1 AIパワードタレントアクイジション (`Development_Specification.md` 3.1 参照)

#### 9.1.1 インテリジェント候補者スクリーニング (3.1.1)

*   **ユースケース概要:** 履歴書・職務経歴書を自動解析し、スキルをマッピング。ジョブディスクリプション (JD) と候補者をセマンティックにマッチングし、スコアリングと優先順位付けを行う。バイアス検出と公平性フィルターも適用する。
*   **主要AIコンポーネント**
    *   LLM (GPT-4o, Claude 3.x): 履歴書・JDの解析、スキル抽出、セマンティックマッチング、スコアリングロジックの生成。
    *   埋め込みモデル (OpenAI `text-embedding-ada-002` 等): 履歴書とJDのベクトル表現生成。
    *   ベクトルデータベース: 候補者スキルとJD要件のベクトル保存・検索。
    *   カスタムNLPモデル (Hugging Face Transformers): 固有表現抽出 (NER) の強化、バイアス検出モデル (オプション)。
    *   RAG: JD作成支援、スクリーニング基準の動的調整。
*   **データ要件**
    *   入力: 履歴書/職務経歴書 (PDF, DOCX, TXT)、ジョブディスクリプション。
    *   参照データ: 企業独自のスキル辞書、過去の採用成功/失敗データ (バイアス検出・成功予測用)。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["フロントエンド: 採用担当者UI"] --> B{"バックエンドAPI<br>(FastAPI)"}
    B --> C["履歴書パーサー<br>(Apache Tika, PyMuPDF)"]
    C --> D["LLM: スキル抽出 & 標準化"]
    D --> E["エンベディング生成"]
    E --> F["ベクトルDB"]
    B --> G["JDパーサー & エンベディング"]
    G --> F
    F --> H["LLM: マッチング & スコアリング"]
    H --> I["バイアス検出モジュール"]
    I --> J["最終スコア & レポート"]
    J --> B
```

*   **プロンプトエンジニアリングのポイント**
    *   **スキル抽出:** 「以下の履歴書から、[プログラミング言語、フレームワーク、ソフトスキル、業界経験年数]などのスキルを抽出し、標準化された形式（例: `{skill: "Python", proficiency: "expert", years: 5}`）でJSONリストとして出力してください。」
    *   **セマンティックマッチング:** 「以下のJDと候補者スキルプロファイルを比較し、各JD要件に対する候補者の適合度を0-10で評価し、その根拠を説明してください。特に潜在的なスキルや類似経験も考慮してください。」
    *   **スコアリングロジック:** （企業ポリシーに基づき）「必須スキルの一致度70%、歓迎スキルの一致度20%、カルチャーフィットの可能性10%の重み付けで総合スコアを計算してください。」
    *   **バイアス検出:** 「以下の候補者評価において、性別、年齢、学歴、国籍などに関連する潜在的なバイアスを示唆する記述や評価があれば指摘してください。」
*   **評価とチューニングの考慮事項**
    *   **スキル抽出精度:** 人手による正解データセットとの比較 (Precision, Recall, F1)。
    *   **マッチングスコアの相関:** 採用担当者の評価との相関、最終的な採用成功率との相関。
    *   **バイアス指標:** 公平性メトリクス (例: Demographic Parity, Equalized Odds) の継続的モニタリング。
    *   **フィードバックループ:** 採用担当者がAIの評価を修正した場合、そのフィードバックをモデル改善に活用。
*   **潜在的な課題と対策**
    *   履歴書の多様なフォーマット: 強力なパーサーとLLMの柔軟な解釈能力が必要。失敗時はOCR処理も検討。
    *   スキルの同義語・類似語の認識: 埋め込みモデルとシソーラスの活用。LLMによる標準化。
    *   過度なキーワードマッチングへの依存: セマンティック検索とLLMによる文脈理解を重視。
    *   AIによるバイアスの再生産: 定期的なバイアス監査と、バイアス緩和技術の導入。多様なデータセットでの学習。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/screening_service.py
    class ScreeningService:
        async def parse_resume(self, resume_file_content: bytes, file_type: str) -> Dict:
            # ... 履歴書解析ロジック (Tika, PyMuPDFなど)
            raw_text = self._extract_text(resume_file_content, file_type)
            
            # LLMでスキル抽出
            # prompt = f"履歴書テキスト:\n{raw_text}\n\nこの履歴書からスキルを抽出し、JSON形式で返してください。"
            # extracted_skills = await llm_provider.generate(model_id="gpt-4o-mini", prompt=prompt)
            # return json.loads(extracted_skills)
            pass

        async def match_candidate_to_jd(self, candidate_profile: Dict, jd_text: str) -> Dict:
            # ... JD解析、スキル抽出
            # ... 候補者プロファイルとJDのベクトル化
            # ... ベクトル類似度計算
            # ... LLMによる詳細マッチングとスコアリング
            # prompt = f"""
            # ジョブディスクリプション:
            # {jd_text}
            #
            # 候補者プロファイル:
            # {json.dumps(candidate_profile, ensure_ascii=False)}
            #
            # 上記を比較し、マッチングスコア(0-100)、強み、懸念点をJSONで返してください。
            # """
            # match_analysis = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(match_analysis)
            pass

        async def detect_bias(self, screening_report: Dict) -> Dict:
            # ... LLMや専用モデルでバイアス検出
            # prompt = f"以下のスクリーニングレポートに潜在的なバイアスが含まれていないか確認してください:\n{json.dumps(screening_report, ensure_ascii=False)}"
            # bias_report = await llm_provider.generate(model_id="gpt-4o", prompt=prompt) # より慎重なモデルが良い
            # return json.loads(bias_report)
            pass
    ```

#### 9.1.2 予測採用分析 (3.1.2)

*   **ユースケース概要:** 候補者の過去データ、スキル、経験、評価に基づいて、入社後の成功確率や定着期間を予測。採用ROI、採用チャネル効率、未充足ポジションの影響コストなどを分析・予測する。
*   **主要AIコンポーネント**
    *   予測モデル (Scikit-learn, XGBoost, PyTorch/JAXによるカスタムモデル): 候補者成功予測、離職予測。
    *   LLM: 予測結果の説明生成、戦略的採用計画のドラフト生成。
    *   時系列分析モデル (ARIMA, Prophet): 応募傾向予測。
    *   データ分析ライブラリ (Pandas, Polars): データ前処理、特徴量エンジニアリング。
*   **データ要件**
    *   入力: 候補者データ (スキル、経験、学歴、面接評価スコア、採用チャネルなど)、JDデータ。
    *   学習データ: 過去の採用者データ (入社後のパフォーマンス評価、勤続期間、昇進履歴、離職理由など)。
    *   市場データ: 競合他社の採用情報、業界の給与水準、労働市場トレンド。
    *   財務データ: 採用コスト、ポジションごとの売上/利益貢献度 (ROI計算用)。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["HRデータレイク/DWH"] --> B["データ前処理 & 特徴量エンジニアリング"]
    B --> C["予測モデル学習パイプライン<br>(Python, Scikit-learn/PyTorch)"]
    C --> D["学習済みモデルレジストリ"]
    
    E["新規候補者データ/市場データ"] --> F["予測API<br>(FastAPI)"]
    F --> D
    F --> G["LLM: 予測結果解釈 & レポート生成"]
    G --> H["ダッシュボード/フロントエンド"]
    
    I["時系列採用データ"] --> J["時系列予測モデル<br>(Prophet/ARIMA)"]
    J --> G
```

*   **プロンプトエンジニアリングのポイント (LLMによる解釈・計画生成)**
    *   **予測結果の説明:** 「候補者Aの入社後6ヶ月以内のパフォーマンススコアが8以上である確率は75%と予測されました。この予測の主な根拠となるトップ3の要因を説明してください。」
    *   **戦略的採用計画生成:** 「過去3年間の採用データ、現在の市場トレンド、および今後1年間の事業計画 (添付資料参照) に基づき、エンジニアリング部門における戦略的採用計画のドラフトを作成してください。重点採用ポジション、推奨採用チャネル、目標採用人数、予算案を含めてください。」
    *   **未充足ポジション影響分析:** 「ポジションX (年俸Y万円、期待売上貢献Z万円/月) が未充足であることによる、今後3ヶ月間の機会損失コストとチームへの影響を分析し、報告書形式でまとめてください。」
*   **評価とチューニングの考慮事項**
    *   **予測精度:** 成功予測モデル (AUC-ROC, Precision-Recall)、定着期間予測 (RMSE, MAE)。
    *   **特徴量の重要度分析:** モデルの解釈性向上、不要な特徴量の削除。
    *   **モデルの公平性:** 異なる属性グループ間での予測精度やエラー率の均一性を評価 (例: Aequitas)。
    *   **時系列予測の精度:** MAPE, SMAPE。季節性やトレンドの適切なキャプチャ。
    *   **定期的なモデル再学習:** データドリフトに対応するため、新しいデータでモデルを定期的に更新。
*   **潜在的な課題と対策**
    *   過去データのバイアス: 学習データ内の既存バイアスを特定し、バイアス緩和手法 (再重み付け、Fairlearnなど) を適用。
    *   成功の定義の曖昧さ: 「成功」の定義を明確にし、複数の指標 (パフォーマンス、定着、昇進など) を組み合わせる。
    *   データのスパース性: 特にニッチな職種や小規模企業では、十分な学習データが得られない可能性。転移学習やデータ拡張を検討。
    *   予測結果の誤解: LLMによる説明生成で、予測の不確実性や前提条件を明記する。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/predictive_hiring_service.py
    import joblib # または onnxruntime など

    class PredictiveHiringService:
        def __init__(self):
            # self.success_predictor = joblib.load("path/to/success_model.pkl")
            # self.retention_predictor = joblib.load("path/to/retention_model.pkl")
            # self.trend_forecaster = joblib.load("path/to/trend_model.pkl") # Prophet/ARIMAモデル
            pass

        async def predict_candidate_success(self, candidate_features: Dict) -> Dict:
            # ... 特徴量エンジニアリング
            # processed_features = self._preprocess_features(candidate_features)
            # success_probability = self.success_predictor.predict_proba(processed_features)[:, 1]
            # contributing_factors = self._get_shap_values(self.success_predictor, processed_features) # SHAP等で要因分析
            # return {"probability": success_probability[0], "factors": contributing_factors}
            pass
            
        async def analyze_hiring_roi(self, position_data: Dict, channel_data: List[Dict]) -> Dict:
            # ... 採用コスト、チャネル別効果、推定LTVなどを計算
            # ... LLMで分析レポート生成
            pass

        async def forecast_application_trends(self, position_title: str, historical_data: List) -> Dict:
            # ... 時系列モデルで応募者数を予測
            # forecast = self.trend_forecaster.predict(future_periods=12) # 12ヶ月予測
            # return {"forecast": forecast.to_dict()}
            pass
    ```

#### 9.1.3 面接最適化システム (3.1.3)

*   **ユースケース概要:** AIがJDと候補者スキルに基づいて面接質問を生成。構造化された回答評価フレームワークを提供。面接官のフィードバックを分析し、パフォーマンス指標を提示。バーチャル初回面接シミュレーター機能。
*   **主要AIコンポーネント**
    *   LLM (GPT-4o, Claude 3.x): 面接質問生成、回答評価、フィードバック分析、バーチャル面接官。
    *   RAG: 企業の面接ガイドライン、過去の良質な質問リスト、コンピテンシーモデルを知識ベースとして活用。
    *   音声認識 (Whisper等) およびNVC (Non-Verbal Cues) 分析モデル (オプション、バーチャル面接用)。
*   **データ要件**
    *   入力: JD、候補者の履歴書/スキルプロファイル、企業のコンピテンシーモデル、面接タイプ (行動面接、技術面接など)。
    *   参照データ: 過去の面接質問と評価結果、高評価/低評価の回答例、面接官のフィードバック。
    *   トレーニングデータ (バーチャル面接用): 面接動画・音声データ (プライバシーに十分配慮)。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["フロントエンド: 面接官UI/候補者UI"] --> B{"バックエンドAPI"}
    B --> C["LLM: 面接質問生成エンジン<br>(JD, 候補者情報, コンピテンシー連携)"]
    C --> A
    
    D["面接記録<br>(音声/テキスト/動画)"] --> B
    B --> E["LLM: 回答評価 & フィードバック分析"]
    E --> F["評価フレームワークDB"]
    E --> G["面接官パフォーマンス指標"]
    G --> A
    
    H["バーチャル面接モジュール"] --> B
    B --> I["LLM: バーチャル面接官"]
    I --> H
    H --> D
```

*   **プロンプトエンジニアリングのポイント**
    *   **質問生成:** 「職務: {JDの要約}, 候補者スキル: {スキルの要約}, 評価コンピテンシー: {コンピテンシーリスト}。この候補者のための行動面接の質問を5つ生成してください。STARメソッドで回答しやすい形式で、各質問がどのコンピテンシーを評価するためのものか明記してください。」
    *   **回答評価:** 「質問: {質問文}\n候補者の回答: {回答文}\n評価基準: {コンピテンシー定義、期待される行動レベル}\nこの回答を0-5のスケールで評価し、評価の根拠、良かった点、改善点を具体的に示してください。」
    *   **フィードバック分析:** 「以下の面接フィードバック群から、共通して指摘されている強みと弱み、評価のばらつき、潜在的なバイアスを抽出し、要約してください。」
*   **評価とチューニングの考慮事項**
    *   **質問の質:** 生成された質問の適切性、網羅性、バイアスのなさ (人事担当者によるレビュー)。
    *   **評価の一貫性:** AIによる評価と複数の面接官による評価の一致度 (Kappa係数など)。
    *   **フィードバック分析の精度:** 人手による分析結果との比較。
    *   **バーチャル面接のエンゲージメント:** 候補者の満足度、ストレスレベル。
*   **潜在的な課題と対策**
    *   画一的な質問生成: 候補者やJDのニュアンスを捉えきれない可能性。多様な質問パターンの学習、RAGによる深掘り。
    *   評価の客観性と公平性: LLMの潜在的バイアス。評価基準の明確化、複数評価者によるキャリブレーション、バイアス検出。
    *   バーチャル面接の自然さ: LLMの応答速度、声質、対話フローの改善。マルチモーダル対応。
    *   プライバシー懸念 (特にバーチャル面接): 候補者への透明な説明と同意取得。データの匿名化・仮名化。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/interview_optimization_service.py
    class InterviewOptimizationService:
        async def generate_interview_questions(self, jd_text: str, candidate_profile: Dict, competencies: List[str]) -> List[Dict]:
            # prompt = f"""
            # ジョブディスクリプション: {jd_text}
            # 候補者情報: {json.dumps(candidate_profile, ensure_ascii=False)}
            # 評価コンピテンシー: {', '.join(competencies)}
            #
            # 上記に基づき、構造化面接のための質問を5つ生成してください。
            # 各質問が評価するコンピテンシーと、期待される回答のポイントも示してください。
            # JSONリスト形式で返してください: [{"question": "...", "competency": "...", "expected_points": ["..."]}, ...]
            # """
            # questions_json = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(questions_json)
            pass

        async def evaluate_interview_response(self, question: str, response: str, evaluation_rubric: Dict) -> Dict:
            # prompt = f"""
            # 質問: {question}
            # 回答: {response}
            # 評価ルーブリック: {json.dumps(evaluation_rubric, ensure_ascii=False)}
            #
            # この回答をルーブリックに基づき評価し、スコア(0-5)と詳細なフィードバックをJSONで返してください。
            # {"score": X, "feedback": "...", "strengths": ["..."], "areas_for_improvement": ["..."]}
            # """
            # evaluation_json = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(evaluation_json)
            pass

        async def analyze_feedback_patterns(self, feedback_list: List[Dict]) -> Dict:
            # ... LLMで複数のフィードバックから共通テーマやバイアスを抽出
            pass
    ```

### 9.2 従業員パフォーマンス＆開発エンジン (`Development_Specification.md` 3.2 参照)

#### 9.2.1 ダイナミックパフォーマンス管理 (3.2.1)

*   **ユースケース概要:** 継続的フィードバックシステムを支援。目標達成予測と進捗トラッキング。多角的評価データ (360度評価、プロジェクト評価など) を統合し、パフォーマンストレンドを分析。コンテキストを認識したパフォーマンス評価を行う。
*   **主要AIコンポーネント**
    *   LLM: フィードバックの要約・感情分析、目標設定支援、パフォーマンスレビューコメント生成支援。
    *   予測モデル: 目標達成確率予測、パフォーマンストレンド予測。
    *   NLP: テキストフィードバックからのキーテーマ抽出。
*   **データ要件**
    *   入力: 従業員設定目標 (OKR, MBOなど)、進捗報告、マネージャー/同僚からのフィードバック、プロジェクト成果物、自己評価。
    *   参照データ: 過去のパフォーマンスデータ、職務記述書、コンピテンシーフレームワーク。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["従業員/マネージャーUI"] --> B{"バックエンドAPI"}
    B --> C["データ収集モジュール<br>(目標, 進捗, フィードバック)"]
    C --> D["パフォーマンスDB"]
    D --> E["予測モデル: 目標達成/トレンド予測"]
    E --> B
    F["フィードバックデータ"] --> G["LLM/NLP: 分析 & 要約"]
    G --> B
    B --> H["LLM: レビューコメント生成支援"]
    H --> A
```

*   **プロンプトエンジニアリングのポイント**
    *   **フィードバック要約:** 「以下のフィードバック群を要約し、主要な肯定的意見、建設的批判、提案されたアクションをそれぞれ3点ずつ抽出してください。また、全体の感情をポジティブ/ニュートラル/ネガティブで判定してください。」
    *   **目標達成支援:** 「目標: {目標内容}, 現在の進捗: {進捗状況}, 障害: {報告された障害}。この目標達成を支援するための具体的なアドバイスや、進捗を加速するためのネクストアクションを3つ提案してください。」
    *   **パフォーマンスレビューコメント生成支援** 「従業員: {名前}, 期間: {期間}, 目標達成度: {データ}, 強み: {リスト}, 課題: {リスト}。これらの情報に基づき、パフォーマンスレビューの「総括」セクションのコメントドラフトを作成してください。具体的で建設的な内容にしてください。」
*   **評価とチューニングの考慮事項**
    *   **目標達成予測の精度:** 実績との比較 (MAE, RMSE)。
    *   **フィードバック分析の質:** 人事担当者による評価、抽出されたテーマの妥当性。
    *   **生成されたコメントの自然さと有用性:** マネージャーによる評価。
*   **潜在的な課題と対策:**
    *   フィードバックの質のばらつき: LLMによるフィードバック改善提案機能。
    *   目標の曖昧さ: LLMによるSMART目標設定支援。
    *   評価バイアスの混入: フィードバック分析時にバイアスワードを検出し警告。多角的なデータソースの活用。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/performance_service.py
    class PerformanceService:
        async def analyze_feedback_batch(self, feedback_texts: List[str]) -> Dict:
            # ... 各フィードバックをLLMで分析 (感情、テーマ)
            # ... 結果を集約してレポート生成
            pass

        async def predict_goal_achievement(self, goal_data: Dict, progress_history: List[Dict]) -> Dict:
            # ... 特徴量作成
            # ... 予測モデルで達成確率を予測
            pass

        async def suggest_review_comments(self, performance_summary: Dict) -> str:
            # prompt = f"以下のパフォーマンスサマリーに基づき、建設的なレビューコメントを生成してください:\n{json.dumps(performance_summary, ensure_ascii=False)}"
            # comment = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return comment
            pass
    ```

#### 9.2.2 スキル・コンピテンシーマッピング (3.2.2)

*   **ユースケース概要:** 組織全体のスキルグラフを構築。従業員のスキルプロファイル、プロジェクト経験、研修履歴などからスキルギャップを自動検出。役割（ロール）ごとに必要なスキルを動的に更新し、未来指向のスキル予測を行う。内部人材マーケットプレイスの基盤となる。
*   **主要AIコンポーネント**
    *   LLM: 非構造化データ (履歴書、プロジェクト説明、フィードバック等) からのスキル抽出と標準化。スキル間の関連性推論。
    *   エンベディングモデル & ベクトルDB: スキル、職務、プロジェクト、従業員プロファイルのベクトル表現と類似度検索。
    *   グラフデータベース (Neo4j等、オプション): スキル、従業員、役割、プロジェクト間の複雑な関係性を表現・分析。
    *   予測モデル: 将来必要とされるスキルの予測。
*   **データ要件**
    *   入力: 従業員プロファイル (自己申告スキル、経験、学歴、資格)、研修受講履歴、プロジェクトアサイン履歴と成果物、パフォーマンスレビュー、職務記述書。
    *   参照データ: 標準スキル分類体系 (例: ESCO, O*NET)、業界トレンドレポート、競合他社の求人情報。
*   **統合アーキテクチャ**
```mermaid
graph TD
    A["各種HRデータソース"] --> B["データ収集 & クリーニング"]
    B --> C["LLM: スキル抽出 & 標準化エンジン"]
    C --> D["スキルマスターDB / ベクトルDB"]
    D --> E["スキルグラフ構築モジュール<br>(GraphDB or リレーショナルDB)"]
    E --> F["スキルギャップ分析エンジン"]
    F --> G["フロントエンド: スキルマップ, ギャップ表示"]
    H["市場トレンド/将来予測データ"] --> I["予測モデル: 将来スキル予測"]
    I --> E
    J["役割定義DB"] --> E
```

*   **プロンプトエンジニアリングのポイント**
    *   **スキル抽出 (プロジェクト説明から):** 「以下のプロジェクト説明文から、このプロジェクトで活用された、または習得された可能性のある技術スキル、ビジネススキル、ソフトスキルを最大10個抽出し、スキル名と推定レベル（初級/中級/上級）をJSON形式でリストアップしてください。」
    *   **スキル標準化:** 「スキル '{raw_skill_name}' は、標準スキルリスト {standard_skills_json} の中のどのスキルに最も近いですか？最も適切な標準スキル名と、その判断理由をJSONで返してください。」
    *   **スキル関連性推論:** 「スキル '{skill_A}' とスキル '{skill_B}' の関連性を0-1のスコアで評価し、その理由を説明してください。また、これら2つのスキルを組み合わせることで価値が高まる職務例を挙げてください。」
*   **評価とチューニングの考慮事項**
    *   **スキル抽出の網羅性と精度:** 人手でタグ付けしたデータセットとの比較。
    *   **スキル標準化の一貫性:** 異なる表現のスキルが正しく同一スキルにマッピングされるか。
    *   **スキルグラフの妥当性:** ドメインエキスパートによるレビュー。
    *   **スキルギャップ分析の有用性:** 従業員やマネージャーからのフィードバック。
*   **潜在的な課題と対策**
    *   スキルの粒度と抽象度の不一致: スキル分類体系の階層化。LLMによる粒度調整支援。
    *   暗黙知やソフトスキルの定量化困難: 360度フィードバックや行動評価からの間接的推論。LLMによる記述的評価の解釈。
    *   スキルの陳腐化: 定期的なスキルライブラリの更新と、新しいスキルの自動検出メカニズム。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/skill_mapping_service.py
    class SkillMappingService:
        async def extract_skills_from_text(self, text_content: str) -> List[Dict]:
            # prompt = f"以下のテキストから関連スキルを抽出し、スキル名と推定レベルをJSONリストで返してください:\n{text_content}"
            # skills_json = await llm_provider.generate(model_id="gpt-4o-mini", prompt=prompt)
            # return json.loads(skills_json)
            pass

        async def build_skill_graph(self, employee_skills: List[Dict], role_requirements: List[Dict]):
            # ... スキル、従業員、役割間の関連性を計算しグラフデータ構造を生成
            # ... グラフDBへの保存またはリレーショナルDBでの表現
            pass
            
        async def identify_skill_gaps(self, employee_id: str, target_role_id: str) -> Dict:
            # ... 従業員の現在スキルと目標役割の要求スキルを比較
            # ... ギャップのあるスキル、過剰なスキル、移転可能なスキルを特定
            pass
    ```

#### 9.2.3 パーソナライズド成長計画 (3.2.3)

*   **ユースケース概要:** AIが個々の従業員のスキルギャップ、キャリア志向、学習スタイルに基づいて、最適な学習リソース (社内外) を推奨。キャリアパス最適化シミュレーター。メンタリングマッチング。スキル習得進捗予測。マイクロラーニング統合。
*   **主要AIコンポーネント**
    *   レコメンデーションエンジン (協調フィルタリング, コンテンツベース, ハイブリッド): 学習リソース、メンター推奨。
    *   LLM: キャリアパスに関する対話型アドバイス、学習計画のカスタマイズ、進捗フィードバック生成。
    *   予測モデル: スキル習得期間予測、キャリア目標達成確率予測。
    *   RAG: 学習コンテンツ、キャリアフレームワーク、メンタープロファイルを知識ベースとして活用。
*   **データ要件**
    *   入力: 従業員スキルプロファイル、キャリア目標、学習履歴、パフォーマンスデータ、利用可能な学習コンテンツメタデータ、メンタープロファイル。
    *   参照データ: 職務ごとの標準キャリアパス、スキル習得難易度データ。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["従業員UI: キャリア目標設定/学習"] --> B{"バックエンドAPI"}
    B --> C["レコメンデーションエンジン"]
    C --> D["学習コンテンツDB"]
    C --> E["メンターDB"]
    C --> B
    
    F["従業員データ<br>(スキル, 目標, 進捗)"] --> B
    B --> G["LLM: パーソナライズド計画生成 & アドバイス"]
    G --> A
    
    B --> H["予測モデル: スキル習得進捗予測"]
    H --> A
```

*   **プロンプトエンジニアリングのポイント**
    *   **学習リソース推奨理由:** 「従業員の現在のスキルセット {current_skills} と目標スキル {target_skills} を考慮し、コース {course_name} を推奨する理由を3点説明してください。特に関連性の高いスキルと期待される効果を強調してください。」
    *   **キャリアパス相談:** 「私は現在 {current_role} です。将来 {target_career_goal} を目指しており、特に {interested_areas} に興味があります。私のスキルプロファイル {skill_summary} を踏まえ、現実的なキャリアステップと、各ステップで習得すべき重要スキル、推奨されるアクションをアドバイスしてください。」
    *   **メンタリングマッチング説明:** 「メンター {mentor_name} ({mentor_expertise}) とメンティー {mentee_name} ({mentee_goals}) をマッチングしました。このマッチングが双方にとって有益であると考えられる理由を、具体的なスキルや経験の共通点・補完点を挙げて説明してください。」
*   **評価とチューニングの考慮事項**
    *   **推奨精度:** 学習リソースのクリック率、完了率、満足度。メンターマッチングの成功率。
    *   **キャリアパスシミュレーションの妥当性:** ユーザーフィードバック、実際のキャリア遷移データとの比較。
    *   **スキル習得予測の精度:** 予測期間と実際の習得期間の差異。
*   **潜在的な課題と対策**
    *   コールドスタート問題: 新規従業員や新しい学習コンテンツに対する推奨が困難。コンテンツベースの特徴量や属性ベースのマッチングで補完。LLMによる探索的推奨。
    *   学習リソースの質の担保: ユーザーレビューや評価システムを導入。LLMによるコンテンツ概要と品質評価の試行。
    *   メンターのミスマッチ: 詳細なプロファイルと期待値のマッチング。相性に関する事前アンケート。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/development_planning_service.py
    class DevelopmentPlanningService:
        async def recommend_learning_resources(self, employee_id: str, skill_gaps: List[str], learning_preferences: Dict) -> List[Dict]:
            # ... 従業員のスキル、目標、嗜好に基づいてレコメンデーションモデルを呼び出し
            # ... LLMで推奨理由を生成
            pass

        async def find_suitable_mentors(self, employee_id: str, development_goals: List[str]) -> List[Dict]:
            # ... メンターDBから候補を検索し、マッチングスコアを計算
            # ... LLMでマッチング理由を生成
            pass

        async def simulate_career_path(self, employee_id: str, target_role: str, steps: int = 3) -> List[Dict]:
            # ... 現在のスキルと目標役割に基づき、中間ステップと必要スキルを予測/提案
            # ... LLMで各ステップの説明とアクションプランを生成
            pass
    ```

### 9.3 組織設計＆ワークフォース計画 (`Development_Specification.md` 3.3 参照)

#### 9.3.1 組織分析エンジン (3.3.1)

*   **ユースケース概要:** チーム構成の最適化モデルを提案。組織ネットワーク分析 (ONA) を実施し、コラボレーションパターンを検出。組織の健全性指標ダッシュボードを提供し、文化適合性マッピングを行う。
*   **主要AIコンポーネント**
    *   グラフ分析ライブラリ (NetworkX, PyVis): ONAデータの可視化と中心性指標などの計算。
    *   クラスタリングアルゴリズム (K-Means, DBSCAN): 従業員やチームのグループ化、隠れたコミュニティの発見。
    *   LLM: コラボレーションパターンの解釈、組織健全性に関する定性データの分析 (サーベイコメントなど)、文化適合性に関する記述の分析。
    *   最適化アルゴリズム (遺伝的アルゴリズム、シミュレーテッドアニーリングなど): チーム構成最適化。
*   **データ要件**
    *   入力: 組織構造データ (階層、部門、チーム)、従業員コミュニケーションデータ (メールメタデータ、チャットログメタデータ – プライバシーに配慮し集約・匿名化)、プロジェクト共同作業データ、従業員サーベイデータ (エンゲージメント、組織文化に関する設問)、スキルデータ。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["コミュニケーションデータ/組織構造データ/サーベイデータ"] --> B["データ収集 & 匿名化/集約"]
    B --> C["ONAエンジン<br>(NetworkX, GraphDB)"]
    C --> D["コラボレーションパターン検出 & 指標計算"]
    D --> E["フロントエンド: ONA可視化, ダッシュボード"]
    
    F["従業員スキル/属性データ"] --> G["チーム構成最適化モデル"]
    G --> H["LLM: 最適化結果の解釈 & 提案"]
    H --> E
    
    I["サーベイ自由記述/文化評価データ"] --> J["LLM/NLP: 定性分析 & 文化マッピング"]
    J --> E
```


*   **プロンプトエンジニアリングのポイント (LLMによる解釈)**
    *   **コラボレーションパターン解釈:** 「添付の組織ネットワーク図と中心性指標に基づき、現在のマーケティング部門内の主要な情報ハブとなっている人物、部門間のブリッジとなっている人物、孤立している可能性のあるチームを特定し、それぞれの状況が組織の効率性に与える影響について考察してください。」
    *   **組織健全性コメント分析:** 「以下の従業員サーベイの自由記述コメント群から、組織文化、リーダーシップ、コミュニケーションに関する主要なポジティブテーマとネガティブテーマをそれぞれ5つずつ抽出し、具体的なコメント例と共に要約してください。」
    *   **チーム構成提案の理由説明:** 「AIが提案した新しいプロジェクトチーム構成（メンバーリスト添付）について、なぜこの構成が目標達成に最適と判断されたのか、各メンバーのスキル、経験、過去の協調実績などを踏まえて説明してください。」
*   **評価とチューニングの考慮事項**
    *   **ONA指標の妥当性:** 実際のコミュニケーション状況や業務効率との相関。
    *   **チーム構成最適化の効果:** シミュレーション環境での評価、実際のチームパフォーマンスとの比較。
    *   **定性データ分析の精度:** 人手による分析結果との比較 (テーマ抽出の一貫性、感情分析の正確性)。
*   **潜在的な課題と対策**
    *   プライバシー懸念: コミュニケーションデータの取り扱いは特に慎重に。匿名化・集約処理の徹底、従業員への透明な説明と同意。分析結果は個人特定につながらない形で提示。
    *   データのサイロ化: 様々なシステムからのデータ統合の困難性。API連携の強化、データウェアハウスの活用。
    *   ONA結果の誤解釈: ネットワーク図や指標が全てではないことを強調。定性情報と組み合わせて多角的に判断。
    *   「最適」なチーム構成の定義: 目標 (生産性、イノベーション、安定性など) に応じて最適化の基準を変える必要。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/organizational_analysis_service.py
    import networkx as nx

    class OrganizationalAnalysisService:
        async def build_ona_graph(self, communication_data: List[Dict], org_structure: Dict) -> nx.Graph:
            # G = nx.Graph()
            # ... 従業員をノードとして追加
            # ... コミュニケーションデータに基づいてエッジを追加 (重み付けなど)
            # return G
            pass

        async def analyze_collaboration_patterns(self, graph: nx.Graph) -> Dict:
            # centrality = nx.degree_centrality(graph)
            # betweenness = nx.betweenness_centrality(graph)
            # communities = list(nx.community.label_propagation_communities(graph)) # 例
            # ... LLMでこれらの指標を解釈
            # return {"centrality_report": "...", "community_structure": "..."}
            pass

        async def optimize_team_composition(self, available_employees: List[Dict], project_requirements: Dict, constraints: Dict) -> List[Dict]:
            # ... スキル、経験、性格特性などを考慮した最適化アルゴリズムを実行
            # ... LLMで提案理由を生成
            pass
    ```

#### 9.3.2 戦略的人員計画 (3.3.2)

*   **ユースケース概要:** 将来のビジネス目標、市場トレンド、退職予測、スキル進化などを考慮し、数ヶ月～数年先の人材ニーズを予測。シナリオベースの人員計画を立案し、能力ベースでの配置最適化、スキルベースでの組織再構築を支援。
*   **主要AIコンポーネント**
    *   時系列予測モデル (Prophet, ARIMA, LSTM等): 人員数、特定スキルの需要/供給予測。
    *   LLM: シナリオ記述、計画案生成、リスク分析、推奨事項の提示。
    *   シミュレーションエンジン: 様々な条件下での人員計画の影響をシミュレーション。
    *   RAG: 業界レポート、競合分析、社内戦略文書を知識ベースとして活用。
*   **データ要件**
    *   入力: 現在の人員構成データ、スキルインベントリ、退職率データ、採用パイプラインデータ、ビジネス戦略/目標、財務予算、市場トレンドデータ (業界成長率、技術進化予測など)。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["ビジネス戦略/市場データ/HRデータ"] --> B["データ統合 & 前処理"]
    B --> C["時系列予測モデル<br>(人員/スキル需要予測)"]
    C --> D["シナリオプランニングエンジン"]
    D --> E["LLM: 計画案生成 & リスク分析"]
    E --> F["フロントエンド: 人員計画ダッシュボード, シミュレーションUI"]
    
    G["組織スキルデータ"] --> H["スキルベース組織再構築プランナー<br>(LLM + 最適化)"]
    H --> F
```


*   **プロンプトエンジニアリングのポイント**
    *   **人員ニーズ予測 (シナリオベース):** 「シナリオ: {市場成長率X%, 新製品Y投入, 競合Z社の動向}。このシナリオに基づき、今後2年間で各部門（営業、開発、マーケティング）に必要となる人員数と主要スキルセットを予測し、その根拠を説明してください。また、潜在的な採用ボトルネックも指摘してください。」
    *   **組織再構築提案:** 「現在の組織スキル分布（添付）と、3年後のビジネス目標（添付）を考慮し、目標達成に向けたスキルベースの組織再構築案を2つ提案してください。各案のメリット・デメリット、移行ステップ、想定される課題を記述してください。」
*   **評価とチューニングの考慮事項**
    *   **予測精度:** 人員数予測 (MAPE)、スキル需要予測 (カテゴリ別精度)。
    *   **シナリオの妥当性:** 生成された計画案がビジネスロジックに整合しているか、ドメインエキスパートによるレビュー。
    *   **シミュレーション結果の信頼性:** モデルの仮定と現実の乖離をチェック。
*   **潜在的な課題と対策**
    *   未来の不確実性: 複数のシナリオを提示し、各シナリオの発生確率や影響度を分析。モンテカルロシミュレーションの活用。
    *   定性的な要素のモデル化: LLMによる市場レポートや戦略文書の解釈を予測モデルの入力として活用。
    *   部門間の利害調整: AIの提案はあくまでデータに基づくものとし、最終決定は人間が行うことを明確化。LLMに異なる視点からの意見を生成させる。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/workforce_planning_service.py
    class WorkforcePlanningService:
        async def forecast_future_headcount(self, historical_headcount: List[Dict], business_drivers: Dict, forecast_horizon_months: int) -> Dict:
            # ... 時系列モデルで部門別・スキル別ヘッドカウントを予測
            # ... ビジネスドライバー (成長率、新規プロジェクト等) を外因変数として組み込む
            pass

        async def generate_scenario_plan(self, scenario_description: str, current_workforce_data: Dict) -> Dict:
            # prompt = f"""
            # シナリオ: {scenario_description}
            # 現在の人員状況: {json.dumps(current_workforce_data, ensure_ascii=False)}
            #
            # 上記シナリオに基づき、今後3年間の戦略的人員計画を立案してください。
            # 必要な人員増減、重点スキル、採用/育成戦略、リスク、予算影響などを包括的に記述してください。
            # """
            # plan = await llm_provider.generate(model_id="gpt-4o", prompt=prompt) # 長文生成対応モデル
            # return {"plan_text": plan, "key_recommendations": self._extract_recommendations(plan)}
            pass
    ```

#### 9.3.3 ロケーション＆ワークスタイル分析 (3.3.3)

*   **ユースケース概要:** リモート/ハイブリッドワークの生産性分析。地域別人材データの可視化。最適な勤務スタイル (オフィス、リモート、ハイブリッド) を従業員やチームの特性に応じて提案。施設利用効率化のレコメンデーション。
*   **主要AIコンポーネント**
    *   統計分析・機械学習モデル: 生産性データと勤務スタイルの相関分析、クラスター分析。
    *   LLM: 分析結果の解釈、勤務スタイル提案の理由説明、効率化レコメンデーション生成。
    *   地理空間分析ツール (GeoPandas, Kepler.gl等連携): 地域別人材データの可視化。
*   **データ要件**
    *   入力: 従業員の勤務場所データ (オフィス出社ログ、リモートワーク申請)、生産性指標 (プロジェクト完了率、タスク処理時間、KPI達成度など)、コミュニケーションツール利用状況、施設利用データ (会議室予約、オフィス入退館記録)、従業員サーベイ (ワークスタイル満足度、通勤ストレスなど)、地域別人材市場データ。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["勤務場所データ/生産性データ/施設利用データ"] --> B["データ統合 & 分析エンジン"]
    B --> C["統計モデル: 生産性-ワークスタイル相関分析"]
    B --> D["地理空間分析: 地域別人材マップ"]
    C --> E["LLM: 分析結果解釈 & 最適ワークスタイル提案"]
    D --> E
    E --> F["フロントエンド: 分析ダッシュボード, 提案UI"]
```

*   **プロンプトエンジニアリングのポイント**
    *   **ワークスタイル提案:** 「従業員A (職務: {職務}, チーム: {チーム}, 通勤時間: {時間}, パフォーマンス指標: {データ}, ワークスタイル希望: {希望})。この従業員に最適な勤務スタイル (オフィス週X日、リモート週Y日) とその理由を提案してください。考慮すべきチーム連携や個人的事情があればそれも加味してください。」
    *   **施設利用効率化:** 「過去3ヶ月の会議室予約データとオフィス入退館データ（添付）を分析し、現在の施設利用の非効率な点を3つ指摘し、それぞれの改善策を提案してください。」
*   **評価とチューニングの考慮事項**
    *   **生産性指標の妥当性:** 選択した指標が実際に生産性を反映しているか。職種による違いを考慮。
    *   **提案されたワークスタイルの受容度:** 従業員満足度、チームパフォーマンスの変化。
    *   **施設利用レコメンデーションの効果:** 実際のコスト削減、スペース有効活用度。
*   **潜在的な課題と対策**
    *   生産性の客観的測定の難しさ: 複数の定量的・定性的指標を組み合わせる。職種ごとに指標を調整。
    *   従業員のプライバシー: 勤務場所やコミュニケーションデータの収集・分析は透明性を確保し、同意を得る。個人が特定できない集約レベルでの分析を主とする。
    *   ワークスタイルの公平性: 特定の従業員グループに不利益が生じないよう配慮。多様なニーズに対応できる柔軟なポリシーを推奨。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/workstyle_analysis_service.py
    class WorkstyleAnalysisService:
        async def analyze_productivity_by_workstyle(self, productivity_data: List[Dict], workstyle_data: List[Dict]) -> Dict:
            # ... データマージと統計分析 (例: ANOVA, 回帰分析)
            # ... LLMで結果のインサイトを生成
            pass

        async def recommend_optimal_workstyle(self, employee_profile: Dict, team_context: Dict) -> Dict:
            # prompt = f"""
            # 従業員プロファイル: {json.dumps(employee_profile, ensure_ascii=False)}
            # チームコンテキスト: {json.dumps(team_context, ensure_ascii=False)}
            #
            # この従業員とチームにとって最適なワークスタイル (オフィス勤務日数、リモート勤務日数、フレキシブルタイムの活用など) を提案し、
            # その理由と期待される効果、注意点をJSONで返してください。
            # """
            # recommendation = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(recommendation)
            pass
    ```

### 9.4 エンプロイーエクスペリエンス＆リテンション (`Development_Specification.md` 3.4 参照)

#### 9.4.1 予測リテンションエンジン (3.4.1)

*   **ユースケース概要:** 複数因子 (勤務状況、パフォーマンス、エンゲージメント、市場価値など) を考慮して従業員の離職リスクを予測。早期介入アラートシステム。定着要因分析。退職コスト計算とリテンション施策の投資対効果最適化。
*   **主要AIコンポーネント**
    *   予測モデル (機械学習: ランダムフォレスト, XGBoost; ディープラーニング: LSTM等): 離職確率予測。
    *   LLM: 離職リスク要因の解釈、パーソナライズされたリテンション戦略提案。
    *   RAG: 過去の成功したリテンション施策、退職者インタビュー記録を知識ベースとして活用。
*   **データ要件**
    *   入力: 従業員属性データ、勤怠データ、給与・昇進履歴、パフォーマンス評価、エンゲージメントサーベイ結果、スキルデータ、マネージャー評価、組織変更履歴、(可能であれば) 競合他社の求人状況や給与水準。
    *   学習データ: 過去の従業員データと実際の離職有無、離職理由。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["HRデータ全般"] --> B["特徴量エンジニアリング & データ前処理"]
    B --> C["離職予測モデル<br>(学習/推論)"]
    C --> D["リスクスコア & 要因分析"]
    D --> E["早期介入アラートシステム"]
    D --> F["LLM: リテンション戦略提案"]
    E --> G["フロントエンド: マネージャー向けダッシュボード"]
    F --> G
    
    H["退職コスト DB/ロジック"] --> I["LLM: ROI分析 & 投資最適化"]
    I --> G
```

*   **プロンプトエンジニアリングのポイント**
    *   **離職リスク要因説明:** 「従業員Bの離職リスクスコアは {スコア} と予測されました。このスコアに最も影響を与えている上位3つの要因を挙げ、それぞれがどのようにリスクに寄与しているか説明してください。」
    *   **リテンション戦略提案:** 「高離職リスクと判定された従業員C (職務: {職務}, 主なリスク要因: {要因リスト}) に対する、個別化されたリテンション戦略を3つ提案してください。各戦略の期待効果と実行ステップも記述してください。過去の類似ケースでの成功事例があれば参考にしてください。」
*   **評価とチューニングの考慮事項**
    *   **予測精度:** AUC-ROC, Precision@K (高リスク群の予測精度), Recall@K。
    *   **要因分析の妥当性:** SHAP値などの解釈手法を用い、ドメイン知識と照合。
    *   **アラートの有効性:** アラート後の介入による実際の離職率低下効果。偽陽性・偽陰性のバランス。
    *   **退職コスト計算の精度:** 計算ロジックの定期的な見直し。
*   **潜在的な課題と対策**
    *   離職理由の多様性と複雑性: 単一モデルで全てを捉えるのは困難。複数のモデルやルールベースのアプローチを組み合わせる。定性的な情報 (1on1記録など) の活用。
    *   予測の自己成就的予言化: 予測結果の取り扱いには細心の注意。マネージャーへの適切なガイダンスとトレーニング。ポジティブな介入に焦点を当てる。
    *   プライバシーと倫理: 予測モデルが特定の属性グループに対して不公平な結果を出さないか継続的に監視。透明性の確保。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/retention_service.py
    class RetentionService:
        def __init__(self):
            # self.churn_predictor = joblib.load("path/to/churn_model.pkl")
            pass

        async def predict_turnover_risk(self, employee_features: Dict) -> Dict:
            # risk_score = self.churn_predictor.predict_proba(employee_features)[:, 1]
            # contributing_factors = self._explain_prediction(self.churn_predictor, employee_features) # e.g., SHAP
            # return {"risk_score": risk_score[0], "factors": contributing_factors}
            pass

        async def suggest_retention_actions(self, employee_id: str, risk_profile: Dict) -> List[Dict]:
            # prompt = f"""
            # 従業員ID: {employee_id}
            # リスクプロファイル: {json.dumps(risk_profile, ensure_ascii=False)}
            #
            # この従業員のための具体的なリテンションアクションを3つ提案してください。
            # 各アクションには、期待効果と実行ステップを含めてください。
            # JSONリスト形式で返してください: [{"action": "...", "expected_effect": "...", "steps": ["..."]}, ...]
            # """
            # actions_json = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(actions_json)
            pass
    ```

#### 9.4.2 エンゲージメント分析 (3.4.2)

*   **ユースケース概要:** リアルタイムエンゲージメントパルスサーベイを実施。テキストコメントやコミュニケーションデータからセンチメント分析。エンゲージメントの主要ドライバーを特定。カスタム介入策の効果測定。組織文化のインパクト分析。
*   **主要AIコンポーネント**
    *   LLM/NLPモデル: センチメント分析、トピックモデリング、キーフレーズ抽出、自由記述コメントの要約。
    *   統計分析モデル: エンゲージメントスコアと他の要因 (パフォーマンス、離職率など) との相関分析。
*   **データ要件**
    *   入力: パルスサーベイ回答 (選択式・自由記述)、従業員フィードバック (eNPSコメントなど)、コミュニケーションツールの集約データ (チャネル活性度、絵文字使用頻度など – プライバシー保護前提)、組織イベント履歴。
*   **統合アーキテクチャ**

```mermaid
graph LR
    A["パルスサーベイ/フィードバック収集UI"] --> B{"バックエンドAPI"}
    B --> C["データストア"]
    C --> D["LLM/NLP: テキスト分析エンジン<br>(センチメント, トピック, 要約)"]
    D --> E["エンゲージメント指標計算"]
    E --> F["フロントエンド: エンゲージメントダッシュボード"]
    
    G["介入施策ログ"] --> E
```

*   **プロンプトエンジニアリングのポイント**
    *   **センチメント分析 (自由記述):** 「以下の従業員コメントのセンチメントを分析し、ポジティブ/ニュートラル/ネガティブのいずれかと、その確信度 (0-1) を判定してください。また、センチメントの根拠となる主要なキーワードを抽出してください。コメント: '{comment_text}'」
    *   **トピックモデリング/要約:** 「過去1ヶ月間の従業員フィードバックコメント群（添付）から、主要なトピックを5つ抽出し、各トピックに関する代表的な意見を要約してください。」
    *   **エンゲージメントドライバー特定支援:** 「エンゲージメントスコア（添付）と他の従業員データ（パフォーマンス、勤続年数、部門など）を比較し、エンゲージメントに最も強く影響を与えている可能性のある要因を3つ挙げ、その考察を述べてください。」
*   **評価とチューニングの考慮事項**
    *   **センチメント分析の精度:** 人手によるラベリングとの比較。業界特有の表現への対応。
    *   **トピックモデリングの質:** 抽出されたトピックの解釈可能性と一貫性。
    *   **ドライバー分析の妥当性:** ドメイン知識との照合、介入施策による効果検証。
*   **潜在的な課題と対策**
    *   サーベイ疲れ: パルスサーベイの頻度と質問数を適切に設計。AIによる質問の動的調整。
    *   ネガティブフィードバックへの対応: 迅速かつ建設的な対応プロセスの確立。AIによるトリアージ支援。
    *   分析結果の行動への結びつけ: 分析結果を具体的なアクションプランに落とし込むためのワークショップやツール提供。LLMによるアクション提案。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/engagement_service.py
    class EngagementService:
        async def analyze_survey_comment(self, comment_text: str) -> Dict:
            # prompt = f"コメント: '{comment_text}'\nこのコメントのセンチメント分析(positive/neutral/negative)、主要トピック、キーフレーズをJSONで返してください。"
            # analysis = await llm_provider.generate(model_id="gpt-4o-mini", prompt=prompt)
            # return json.loads(analysis)
            pass

        async def identify_engagement_drivers(self, survey_results: List[Dict], employee_data: List[Dict]) -> List[str]:
            # ... 統計分析 (相関分析、回帰分析など) を実行
            # ... LLMで結果を解釈し、主要ドライバーを特定
            pass
    ```

#### 9.4.3 ウェルネス＆バランス (3.4.3)

*   **ユースケース概要:** 勤務パターン、コミュニケーション量、カレンダー情報などから、従業員のストレスやバーンアウトのリスクを早期に予測。ワークロードのバランシングを推奨。ウェルネスプログラムの効果測定。チームダイナミクスの最適化とメンタルヘルス早期支援。
*   **主要AIコンポーネント**
    *   異常検知モデル: 勤務時間、会議時間、メール/チャット量などの通常パターンからの逸脱を検出。
    *   予測モデル: バーンアウトリスク予測 (過去のバーンアウト事例データや関連指標から学習)。
    *   LLM: ストレス要因の解釈、パーソナライズされたウェルネスアドバイス、ワークロード調整提案。
    *   NLP: コミュニケーションデータ (匿名化・集約化) からのストレス兆候の分析。
*   **データ要件**
    *   入力: 勤怠ログ、カレンダーデータ (会議時間、予定密度)、コミュニケーションツールの利用統計 (メッセージ数、応答時間 – 集約・匿名化)、プロジェクト負荷データ、(オプトイン形式での) ウェアラブルデバイスデータ、ストレスチェックサーベイ結果。
    *   学習データ (バーンアウト予測用): 過去のバーンアウト事例と関連する先行指標データ。
*   **統合アーキテクチャ**

```mermaid
graph LR
    A["勤怠/カレンダー/コミュニケーションデータ"] --> B["データ収集 & 匿名化/集約"]
    B --> C["異常検知 & パターン分析エンジン"]
    C --> D["バーンアウトリスク予測モデル"]
    D --> E["ウェルネスダッシュボード & アラート"]
    E --> F["フロントエンド: マネージャー/従業員向け"]
    
    D --> G["LLM: パーソナライズドウェルネス提案"]
    G --> F
```

*   **プロンプトエンジニアリングのポイント**
    *   **ワークロード調整提案:** 「従業員Dの過去4週間の勤務パターン（添付: 平均残業時間、会議密度、タスクリスト）と現在のプロジェクト負荷を考慮し、過負荷の兆候があれば指摘してください。具体的なワークロード調整案（タスクの優先順位付け、委任可能な業務、会議の効率化など）を3つ提案してください。」
    *   **ウェルネスアドバイス:** 「従業員Eが最近ストレスレベルの上昇を報告しています（サーベイ結果添付）。この従業員が試せるセルフケア戦略や、利用可能な社内ウェルネスリソースを3つ提案してください。提案は共感的かつ具体的なものにしてください。」
*   **評価とチューニングの考慮事項**
    *   **バーンアウト予測の精度と倫理的影響:** 高精度な予測と同時に、ラベリングや誤診断のリスクを最小化。予測結果はあくまで注意喚起とし、専門家の判断を仰ぐ。
    *   **異常検知の感度:** 偽陽性/偽陰性のバランス調整。
    *   **提案されたウェルネス施策の利用率と効果:** 従業員フィードバック、ストレスレベルの変化。
*   **潜在的な課題と対策**
    *   従業員のプライバシーと監視懸念: データ収集の目的と範囲を明確に伝え、同意を得る。分析は個人が特定できない集約レベルを基本とし、個人向けアラートはオプトイン方式を検討。
    *   ストレス要因の多様性: 仕事以外の要因も影響するため、AIの分析結果はあくまで一助と位置づける。
    *   「監視されている」という印象を与えないUI/UX: ポジティブな支援とリソース提供を前面に出す。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/wellness_service.py
    class WellnessService:
        async def predict_burnout_risk(self, employee_work_patterns: Dict, engagement_data: Dict) -> Dict:
            # ... 特徴量エンジニアリング
            # ... 予測モデルでリスクスコアを計算
            # ... LLMでリスク要因を説明
            pass

        async def suggest_workload_balancing(self, employee_id: str, current_load: Dict, team_capacity: Dict) -> List[str]:
            # prompt = f"""
            # 従業員ID: {employee_id} の現在の負荷状況とチームのキャパシティを考慮し、
            # ワークロードバランスを改善するための具体的な提案をリストで返してください。
            # 負荷状況: {json.dumps(current_load, ensure_ascii=False)}
            # チームキャパシティ: {json.dumps(team_capacity, ensure_ascii=False)}
            # """
            # suggestions = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return suggestions.split('\n') # 例
            pass
    ```

### 9.5 ピープルアナリティクス＆意思決定ハブ (`Development_Specification.md` 3.5 参照)

このセクションは、これまでの分析結果を統合し、経営層や人事リーダーの意思決定を支援する機能群です。AIは主に、データの集約、可視化支援、インサイト抽出、予測、シミュレーション、戦略的アドバイスの生成を担います。

#### 9.5.1 エクゼクティブインサイトダッシュボード (3.5.1)

*   **ユースケース概要:** ビジネスKPIと人材指標の相関分析。人材リスク早期警告。戦略マップと人材アラインメント。ワンクリックエグゼクティブレポート。DEIスコアカードと進捗追跡。
*   **主要AIコンポーネント**
    *   LLM: 複雑なデータ相関の自然言語による説明、エグゼクティブレポートの自動生成、リスク警告の要約と推奨アクション。
    *   データ可視化ライブラリ連携 (D3.js, Recharts等): AIが分析した結果を基に、適切なグラフ種類や表示方法を提案。
    *   時系列分析と異常検知モデル: KPIのトレンド予測と異常値の早期警告。
*   **データ要件**
    *   入力: ビジネスKPIデータ (売上、利益、顧客満足度など)、全ての人材関連データ (採用、パフォーマンス、エンゲージメント、離職、スキル、給与など)、戦略目標データ、DEI関連データ。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["ビジネスKPIデータ/全HRデータ"] --> B["データウェアハウス/データマート"]
    B --> C["分析エンジン<br>(相関分析, KPIトラッキング)"]
    C --> D["LLM: インサイト抽出 & レポート生成"]
    D --> E["フロントエンド: エグゼクティブダッシュボード"]
    
    F["リスクモデル群<br>(離職, バーンアウト等)"] --> G["人材リスク集約 & 早期警告システム"]
    G --> D
```


*   **プロンプトエンジニアリングのポイント**
    *   **KPI相関説明:** 「過去1年間の{ビジネスKPI名}と{人材指標名}の相関分析結果（添付グラフ参照）について、主な洞察と経営層が注目すべきポイントを3点、平易な言葉で説明してください。」
    *   **エグゼクティブレポート生成:** 「以下の主要人材指標（採用効率、離職率、エンゲージメントスコア、トップパフォーマー比率）の最新データと前月比、戦略目標との乖離に基づいて、経営会議向けの1ページサマリーレポートを作成してください。主要な成果、課題、推奨アクションを簡潔にまとめてください。」
    *   **リスク警告:** 「最新の人材データから、今後3ヶ月以内に顕在化する可能性のある重大な人材リスク（例: 特定部門の大量離職、重要スキルの不足）を特定し、そのリスクレベル、潜在的影響、推奨される予防策を報告してください。」
*   **評価とチューニングの考慮事項**
    *   **インサイトの質と実用性:** 経営層や人事リーダーからのフィードバック。
    *   **レポートの明瞭さと網羅性:** 情報が不足していないか、誤解を招く表現がないか。
    *   **早期警告の精度:** 警告の偽陽性・偽陰性。警告後のアクションによるリスク回避率。
*   **潜在的な課題と対策**
    *   データのサイロ化と品質: 統合データ基盤の構築とデータガバナンスの徹底。
    *   KPI間の複雑な因果関係の誤解釈: 相関関係と因果関係を区別。LLMによる説明でも慎重な表現を用いる。
    *   経営層のITリテラシー: 専門用語を避け、視覚的に分かりやすいダッシュボードと簡潔な説明を提供。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/executive_dashboard_service.py
    class ExecutiveDashboardService:
        async def generate_executive_summary(self, current_kpi_data: Dict, strategic_goals: Dict) -> str:
            # prompt = f"""
            # 最新KPIデータ: {json.dumps(current_kpi_data, ensure_ascii=False)}
            # 戦略目標: {json.dumps(strategic_goals, ensure_ascii=False)}
            #
            # 上記に基づき、経営層向けの1ページ人材戦略サマリーレポートを作成してください。
            # 主要成果、課題、短期的な推奨アクションを含めてください。
            # """
            # summary_report = await llm_provider.generate(model_id="gpt-4o", prompt=prompt) # 高品質な要約が求められる
            # return summary_report
            pass

        async def analyze_kpi_correlations(self, business_kpi_series: pd.Series, hr_kpi_series: pd.Series) -> str:
            # correlation = business_kpi_series.corr(hr_kpi_series)
            # prompt = f"ビジネスKPIとHR KPIの相関係数は {correlation:.2f} です。この相関の解釈と、考えられる要因、ビジネスへの示唆を説明してください。"
            # interpretation = await llm_provider.generate(model_id="gpt-4o-mini", prompt=prompt)
            # return interpretation
            pass
    ```

#### 9.5.2 予測分析＆シミュレーション (3.5.2)

*   **ユースケース概要:** "What-If" シナリオモデリング (例: 特定のポリシー変更が離職率やエンゲージメントに与える影響)。組織変更の影響シミュレーター。人材投資対効果 (ROI) 最適化エンジン。トレンド予測とアラート。
*   **主要AIコンポーネント**
    *   シミュレーションモデル (エージェントベースモデリング, システムダイナミクス等): 組織変更やポリシー変更の影響をシミュレート。
    *   予測モデル (既存の離職予測、エンゲージメント予測モデル等を活用)。
    *   最適化アルゴリズム: 予算制約下での人材投資ROI最大化。
    *   LLM: シナリオ設定の支援、シミュレーション結果の解釈と報告書作成。
*   **データ要件**
    *   入力: 現在の組織・従業員データ、変更を検討しているポリシーや組織構造案、過去の施策とその結果データ、予算制約。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["ユーザーUI: シナリオ設定"] --> B{"バックエンドAPI"}
    B --> C["シミュレーションエンジン<br>(予測モデル群と連携)"]
    C --> D["LLM: シミュレーション結果の分析 & レポート"]
    D --> A
    
    E["投資オプション/制約"] --> F["ROI最適化エンジン"]
    F --> D
```


*   **プロンプトエンジニアリングのポイント**
    *   **シナリオ設定支援:** 「リモートワークポリシーを『週2日オフィス出社必須』に変更する場合のシミュレーションを行いたい。この変更が影響を与えうる主要な人材指標 (例: 生産性、エンゲージメント、離職率、コミュニケーションコスト) は何か？また、シミュレーションの前提条件として考慮すべき点は何か？」
    *   **シミュレーション結果解釈:** 「シナリオ『{シナリオ説明}』のシミュレーション結果（添付データ）に基づき、主要なポジティブな影響とネガティブな影響をリストアップし、総合的な評価と推奨事項を述べてください。」
    *   **投資最適化提案:** 「年間人材開発予算 {予算額} の範囲内で、{目標指標 (例: トップタレント定着率10%向上)} を最大化するための最適な投資配分（研修A, メンターシップB, 福利厚生Cなど）を提案し、その根拠を示してください。」
*   **評価とチューニングの考慮事項**
    *   **シミュレーションモデルの妥当性:** 過去データを用いたバックテスト。専門家によるモデルロジックのレビュー。
    *   **感度分析:** 入力パラメータの変化が結果に与える影響を分析し、モデルの頑健性を評価。
    *   **ROI最適化の現実性:** 提案された投資配分が実行可能か、他の制約条件を考慮しているか。
*   **潜在的な課題と対策**
    *   複雑な現実の単純化: シミュレーションは常に現実の近似。モデルの限界を明示し、複数のシナリオで検証。
    *   予測の連鎖による誤差の増幅: 各予測モデルの精度を個別に高める。不確実性を結果に含めて表示。
    *   「最適解」への過信: AIの提案はあくまで参考。人間の判断と戦略的意図を重視。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/simulation_service.py
    class SimulationService:
        async def run_what_if_simulation(self, scenario_parameters: Dict, base_data: Dict) -> Dict:
            # ... シナリオパラメータに基づいてベースデータを変更
            # ... 関連する予測モデル (離職、エンゲージメント等) を実行し、影響を推定
            # ... LLMで結果をサマライズ
            pass

        async def optimize_hr_investment(self, budget: float, investment_options: List[Dict], target_metric: str) -> Dict:
            # ... 最適化アルゴリズム (線形計画法、遺伝的アルゴリズム等) を使用して最適な投資配分を探索
            # ... LLMで提案の根拠を説明
            pass
    ```

#### 9.5.3 AIアシスト意思決定サポート (3.5.3)

*   **ユースケース概要:** 分析結果やシミュレーションに基づいて、具体的な推奨アクションを提示。エビデンスベースの意思決定フレームワークを提供。コラボレーティブな意思決定ツール (複数関係者の意見集約支援など)。決定事項のトラッキングと効果測定。生成AIパワードの戦略的アドバイザー機能。
*   **主要AIコンポーネント**
    *   LLM: 推奨アクションの生成と優先順位付け、意思決定の根拠説明、複数意見の要約と比較、戦略的アドバイスの生成。
    *   RAG: ベストプラクティス、過去の意思決定事例、業界標準などを知識ベースとして活用。
    *   ルールエンジン (オプション): 特定の条件に基づいて標準的な推奨アクションをトリガー。
*   **データ要件**
    *   入力: これまでの分析結果 (KPI、予測、シミュレーション結果)、組織の戦略目標、利用可能なリソース、過去の意思決定とその結果。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["分析/シミュレーション結果/戦略目標"] --> B["意思決定エンジン<br>(LLM + RAG + ルール)"]
    B --> C["推奨アクション & 根拠提示"]
    C --> D["フロントエンド: 意思決定支援UI"]
    
    E["ユーザーによる決定/フィードバック"] --> B
    B --> F["決定トラッキング & 効果測定DB"]
```


*   **プロンプトエンジニアリングのポイント**
    *   **推奨アクション生成:** 「分析結果（添付）と組織の最優先目標（{目標}）に基づき、今後3ヶ月以内に取るべき人事関連のトップ3アクションを提案してください。各アクションについて、期待される効果、必要なリソース、潜在的リスク、実行の優先度（高/中/低）を明記してください。」
    *   **戦略的アドバイス:** 「{現状の課題や機会} を踏まえ、当社の人材戦略に関する長期的なアドバイスを3つ提案してください。各アドバイスは、データに基づいた根拠と、業界のベストプラクティスを参考にしてください。」
    *   **意見集約と分析:** 「以下の複数のマネージャーからの意見（添付）を分析し、主要な論点、合意点、対立点を整理してください。また、これらの意見を踏まえた上で、バランスの取れた解決策や次の議論の進め方を提案してください。」
*   **評価とチューニングの考慮事項**
    *   **推奨アクションの質と実行可能性:** ユーザーによる評価、アクション実行後のKPI変化。
    *   **意思決定プロセスの効率向上:** 意思決定にかかる時間の短縮、関係者の満足度。
    *   **戦略的アドバイスの洞察力:** 経営層からのフィードバック。
*   **潜在的な課題と対策**
    *   AIへの過度な依存: AIはあくまでサポートツールであり、最終的な意思決定責任は人間にあることを明確にする。
    *   コンテキスト理解の限界: 複雑な組織の力学や非公式な情報をAIが完全に理解するのは困難。人間の直感や経験を補完するものとして活用。
    *   推奨アクションの一般化: 組織の特殊性を考慮したカスタマイズ機能。ユーザーが推奨を調整できるインターフェース。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/decision_support_service.py
    class DecisionSupportService:
        async def generate_recommended_actions(self, analysis_summary: Dict, strategic_priorities: List[str]) -> List[Dict]:
            # prompt = f"""
            # 分析サマリー: {json.dumps(analysis_summary, ensure_ascii=False)}
            # 戦略的優先事項: {', '.join(strategic_priorities)}
            #
            # 上記に基づき、具体的な推奨アクションを優先度順にリストで提案してください。
            # 各アクションには期待効果、リソース、リスクを含めてください。
            # JSON形式: [{"action": "...", "priority": "High/Medium/Low", ...}, ...]
            # """
            # recommendations = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(recommendations)
            pass

        async def provide_strategic_advice(self, current_situation_report: str, company_goals: str) -> str:
            # ... RAGで業界レポートやベストプラクティスを検索
            # ... LLMで上記情報と入力に基づいて戦略的アドバイスを生成
            pass
    ```



### 9.6 持続可能な人材管理 (`Development_Specification.md` 3.6 参照)

#### 9.6.1 DEI分析とアクション (3.6.1)

*   **ユースケース概要:** 組織内の多様性指標 (性別、年齢、人種、障害の有無など) を計測し、目標値を設定。インクルージョンに関するサーベイ結果やフィードバックを分析。採用、昇進、報酬における公平性のギャップを検出。DEIイニシアティブのROI測定と効果的なアクションを推奨。マイクロアグレッションの検出と対応推奨。
*   **主要AIコンポーネント**
    *   統計分析モデル: 多様性指標の計算、公平性ギャップの統計的有意性の検証 (例: 差の検定、回帰分析)。
    *   LLM/NLP: インクルージョンサーベイの自由記述分析 (感情、テーマ抽出)、マイクロアグレッションの可能性のある表現の検出、DEIアクションプランの提案、レポート生成。
    *   RAG: DEI関連の学術論文、ベストプラクティス、法的要件などを知識ベースとして活用。
*   **データ要件**
    *   入力: 従業員属性データ (自己申告ベース、プライバシー保護)、採用データ、昇進データ、報酬データ、パフォーマンス評価データ、インクルージョンサーベイ結果 (定量的・定性的)、DEI関連研修の参加履歴、苦情・相談データ (匿名化)。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[従業員属性/採用/昇進/報酬/サーベイデータ] --> B[データ集約 & 匿名化処理];
        B --> C[統計分析エンジン: 多様性指標 & 公平性ギャップ分析];
        C --> D[DEIダッシュボード];
        
        E[サーベイ自由記述/フィードバック] --> F[LLM/NLP: インクルージョン分析 & マイクロアグレッション検出];
        F --> D;
        
        C & F --> G[LLM: DEIアクションプラン推奨 & ROI分析支援];
        G --> D;
    ```
*   **プロンプトエンジニアリングのポイント**
    *   **インクルージョンサーベイ分析:** 「以下のインクルージョンサーベイの自由記述コメントから、インクルージョンに関する従業員の主な懸念事項と肯定的な意見をそれぞれ5つずつ抽出し、具体的なコメント例と共に要約してください。特に、異なる属性グループ間で意見の傾向に違いがあれば指摘してください。」
    *   **マイクロアグレッション検出支援 (注意: 非常に慎重な運用が必要):** 「以下のコミュニケーションログ（匿名化済み）の中に、マイクロアグレッションや無意識のバイアスを示唆する可能性のある表現があれば指摘し、その理由と、よりインクルーシブな代替表現を提案してください。文脈を考慮し、過度な指摘は避けてください。」
    *   **DEIアクションプラン提案:** 「現在のDEI指標（添付）とインクルージョン分析結果（添付）に基づき、今後6ヶ月間で取り組むべきDEIイニシアティブを3つ提案してください。各イニシアティブの目標、具体的なアクション、期待される効果、成功指標を記述してください。」
*   **評価とチューニングの考慮事項**
    *   **公平性ギャップ検出の精度:** 統計的妥当性、誤検出・見逃しのリスク評価。
    *   **マイクロアグレッション検出の精度と倫理:** 専門家によるレビュー、偽陽性/偽陰性の影響評価。非常にセンシティブなため、AIはあくまで補助ツールとして活用。
    *   **推奨アクションの効果:** DEI指標の改善度、従業員満足度の変化。
*   **潜在的な課題と対策**
    *   データ収集の壁とプライバシー: DEI関連データは非常に機微。完全な匿名性と自発的な情報提供を保証。分析結果は個人が特定できない集約レベルでのみ利用。
    *   マイクロアグレッション検出の主観性と誤判定: LLMの判断は参考情報とし、人間による最終確認と対話が必須。教育・啓発を主目的とする。
    *   DEIの複雑性と文化依存性: グローバル企業の場合、地域ごとの文化や法的背景を考慮した分析と対策が必要。LLMに地域コンテキストを提供。
    *   「パフォーマンス」としてのDEI: DEIへの取り組みが形式的にならないよう、本質的なインクルージョン文化の醸成を支援する。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/dei_service.py
    class DEIService:
        async def analyze_diversity_metrics(self, employee_data: List[Dict]) -> Dict:
            # ... 属性データに基づいて多様性指標 (例: 性別比率、年齢分布) を計算
            # ... 目標値との比較
            pass

        async def detect_fairness_gaps(self, promotion_data: List[Dict], compensation_data: List[Dict], control_variables: List[str]) -> Dict:
            # ... 回帰分析などを用いて、属性と昇進/報酬の間に統計的に有意な差がないか検証 (コントロール変数を考慮)
            # ... LLMで結果を解釈し、レポートを生成
            pass

        async def analyze_inclusion_feedback(self, feedback_texts: List[str]) -> Dict:
            # prompt = f"以下の従業員フィードバック群から、インクルージョンに関する主要テーマと感情を分析してください:\n{json.dumps(feedback_texts)}"
            # analysis = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(analysis)
            pass
    ```

#### 9.6.2 ESG人材フレームワーク (3.6.2)

*   **ユースケース概要:** サステナビリティ関連スキルをマッピング。従業員による環境フットプリント削減提案の収集と評価。ソーシャルインパクトのあるイニシアティブへの従業員参加支援。組織のガバナンス指標（透明性、倫理など）に関する従業員意識調査と改善提案。規制コンプライアンス支援。
*   **主要AIコンポーネント**
    *   LLM/NLP: ESGレポート、サステナビリティ関連文書の解析、スキル抽出、従業員提案の評価・要約、改善提案生成。
    *   RAG: ESG基準、規制文書、サステナビリティ関連のベストプラクティスを知識ベースとして活用。
    *   スキルマッピングエンジン (9.2.2 と連携)。
*   **データ要件:**
    *   入力: 従業員スキルデータ、研修データ、ESG関連の社内イニシアティブ情報、従業員からの環境/社会貢献提案、サステナビリティレポート、関連法規制文書。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[従業員スキル/ESG提案/サステナビリティレポート] --> B[データ収集 & 分析エンジン];
        B --> C[LLM/NLP: ESGスキルマッピング & 提案評価];
        C --> D[ESGダッシュボード & レポート];
        
        E[規制文書/業界基準] --> F[RAGシステム];
        F --> C; 
        F --> G[LLM: コンプライアンスチェック支援 & 改善提案];
        G --> D;
    ```
*   **プロンプトエンジニアリングのポイント**
    *   **ESGスキルマッピング:** 「当社のサステナビリティ目標（添付）と主要なESG課題を考慮し、これらの達成に必要なスキルセットを特定してください。また、現在の従業員スキルデータベース（検索APIあり）から、これらのスキルを持つ従業員や育成候補者をリストアップする方法を提案してください。」
    *   **従業員提案評価:** 「従業員からの環境フットプリント削減提案: '{提案内容}'。この提案の実現可能性、潜在的インパクト（コスト削減、環境負荷低減）、実行に必要なリソースを評価し、総合的な推奨度（高/中/低）とその理由を述べてください。」
    *   **ガバナンス改善提案:** 「当社の倫理規定と最近の従業員意識調査結果（添付）を分析し、組織のガバナンス（透明性、倫理観、意思決定プロセス）を向上させるための具体的な施策を3つ提案してください。各施策の期待効果と導入時の注意点も記述してください。」
*   **評価とチューニングの考慮事項**
    *   **ESGスキル特定のマッチ度:** 業界標準や専門家の意見との整合性。
    *   **提案評価の客観性:** 複数の評価者による評価との比較。
    *   **コンプライアンスチェックの網羅性:** 関連法規の更新への追従。
*   **潜在的な課題と対策**
    *   ESGの広範性と複雑性: 分析対象とする範囲を明確化 (例: 環境、社会、ガバナンスの特定領域)。
    *   グリーンウォッシングへの懸念: AIの提案が表面的なものに留まらないよう、具体的な行動と測定可能な成果に焦点を当てる。
    *   従業員の自発的参加の促進: AIをアイデア創出やマッチングのツールとして提供し、エンゲージメントを高める。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/esg_talent_service.py
    class ESGTalentService:
        async def map_sustainability_skills(self, esg_goals: str, employee_skill_data: List[Dict]) -> Dict:
            # ... LLMでESG目標から必要スキルを定義
            # ... 従業員スキルデータと照合してマッピング
            pass

        async def evaluate_employee_esg_proposal(self, proposal_text: str, evaluation_criteria: Dict) -> Dict:
            # prompt = f"""
            # 従業員提案: {proposal_text}
            # 評価基準: {json.dumps(evaluation_criteria, ensure_ascii=False)}
            #
            # 上記提案を評価基準に基づき評価し、スコア、強み、懸念点、推奨事項をJSONで返してください。
            # """
            # evaluation = await llm_provider.generate(model_id="gpt-4o", prompt=prompt)
            # return json.loads(evaluation)
            pass
    ```

### 9.7 給与・報酬分析エンジン (`Development_Specification.md` 3.7 参照)

#### 9.7.1 報酬ベンチマーキングと公平性分析 (3.7.1)

*   **ユースケース概要:** 社内外の給与データに基づき、市場競争力のある報酬ベンチマークを作成。職種、等級、地域、経験年数などを考慮した給与分布を分析。統計的手法を用いて、性別、年齢、人種などの属性間での給与格差を検出し、公平性を評価する。
*   **主要AIコンポーネント**
    *   統計分析モデル: 給与分布分析 (箱ひげ図、ヒストグラム)、回帰分析 (給与に影響を与える要因の特定)、統計的検定 (t検定, ANOVAなど属性間格差の検証)。
    *   LLM: 分析結果の解釈とレポート生成、給与構造に関する洞察の抽出。
    *   データ収集・統合モジュール: 外部市場データ (Payscale, Salary.com等のAPI連携やデータインポート) と社内データの統合。
*   **データ要件**
    *   入力: 社内従業員給与データ (基本給、賞与、手当、等級、職務、勤続年数、評価、属性情報)、外部市場給与データ (職種別、地域別、経験レベル別)。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[社内給与データ/外部市場給与データ] --> B[データ統合 & クリーニング];
        B --> C[統計分析エンジン: 分布分析 & 格差検出];
        C --> D[LLM: 結果解釈 & レポート生成];
        D --> E[フロントエンド: 報酬分析ダッシュボード];
    ```
*   **プロンプトエンジニアリングのポイント (LLMによる解釈)**
    *   **市場ベンチマークレポート:** 「当社の{職種}の給与中央値は{社内中央値}ドルですが、市場データ（添付）によると同職種の市場中央値は{市場中央値}ドルです。この差異について、考えられる要因と、当社の報酬戦略への示唆を述べてください。」
    *   **公平性分析サマリー:** 「給与公平性分析の結果（統計レポート添付）、{属性A}と{属性B}の間で統計的に有意な給与格差が検出されました（調整後差額X%）。この結果の解釈と、考えられる原因、さらなる調査や対応策について提言してください。」
*   **評価とチューニングの考慮事項**
    *   **市場データソースの信頼性と最新性:** 複数のデータソースを比較検証。定期的なデータ更新。
    *   **統計モデルの妥当性:** モデルの仮定が満たされているか (残差分析など)。コントロール変数の適切な選択。
    *   **公平性分析の感度:** 格差検出の閾値設定。統計的有意性と実質的有意性の両方を考慮。
*   **潜在的な課題と対策**
    *   社内データと市場データの粒度の違い: 職務レベルのマッピング、統計的調整手法の利用。
    *   説明できない給与格差の特定: 回帰分析の残差が大きい従業員を特定し、個別の事情を調査。
    *   分析結果の伝え方: 給与情報は非常にセンシティブ。誤解を招かないよう、透明性高く、かつ慎重にコミュニケーション。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/compensation_analysis_service.py
    import pandas as pd
    from scipy import stats

    class CompensationAnalysisService:
        async def benchmark_salary_against_market(self, internal_salaries: pd.DataFrame, market_data: pd.DataFrame, job_role: str) -> Dict:
            # ... internal_salariesとmarket_dataをマージし、特定のjob_roleでフィルタリング
            # ... 統計的比較 (例: 中央値、パーセンタイル比較)
            # ... LLMで比較結果のレポートを生成
            pass

        async def analyze_pay_equity(self, employee_data: pd.DataFrame, demographic_column: str, salary_column: str, control_columns: List[str]) -> Dict:
            # # 例: 線形回帰モデルでコントロール変数の影響を除去した後のデモグラフィック変数の影響を分析
            # formula = f"{salary_column} ~ {demographic_column} + " + " + ".join(control_columns)
            # model = ols(formula, data=employee_data).fit()
            # demographic_coeff = model.params[demographic_column]
            # p_value = model.pvalues[demographic_column]
            # ... LLMで統計結果を解釈
            # return {"coefficient": demographic_coeff, "p_value": p_value, "interpretation": "..."}
            pass
    ```

#### 9.7.2 報酬最適化エンジン (3.7.2)

*   **ユースケース概要:** 個々の従業員の総報酬 (給与、賞与、福利厚生、株式など) をインタラクティブに可視化。パフォーマンスや市場価値に基づいた報酬シミュレーター。給与調整が財務全体に与えるインパクトを予測。個々の従業員に最適化された報酬パッケージを推奨。
*   **主要AIコンポーネント**
    *   最適化アルゴリズム: 予算制約と公平性制約の下で、パフォーマンスや市場価値を最大化する報酬配分を探索。
    *   LLM: 報酬パッケージの構成要素とその価値を従業員に分かりやすく説明。パーソナライズされた推奨理由を生成。
    *   予測モデル: 給与変更が従業員の満足度や定着率に与える影響を予測 (オプション)。
*   **データ要件**
    *   入力: 従業員データ (現行給与、パフォーマンス、スキル、市場価値推定)、会社の報酬ポリシー、予算、福利厚生オプションとコスト。
*   **統合アーキテクチャ**
```mermaid
graph LR
    A["従業員データ/報酬ポリシー/予算"] --> B["報酬最適化エンジン<br>(最適化アルゴリズム + LLM)"]
    B --> C["パーソナライズド報酬パッケージ案"]
    C --> D["フロントエンド: 報酬シミュレーター, 総報酬ステートメント"]
    
    E["給与調整シナリオ"] --> F["財務インパクト予測モデル"]
    F --> D
```


*   **プロンプトエンジニアリングのポイント:**
    *   **総報酬ステートメント説明:** 「従業員Fの総報酬パッケージ（基本給、期待賞与、株式オプション価値、福利厚生の会社負担額など）の内訳を、各項目が従業員のキャリアと生活にどのような価値をもたらすかを平易な言葉で説明してください。」
    *   **パーソナライズ報酬推奨:** 「従業員G (役割: {役割}, パフォーマンス: {評価}, 市場価値: {推定}, ライフステージ: {情報}) に対し、現在の報酬パッケージをどのように調整すれば、モチベーションと定着を最大化できるか、具体的な報酬構成案と調整理由を提案してください。総人件費の増加はX%以内とします。」
*   **評価とチューニングの考慮事項**
    *   **最適化結果の公平性と透明性:** 提案された報酬が社内ポリシーや公平性の原則に準拠しているか。
    *   **財務インパクト予測の精度:** 実際のコストとの比較。
    *   **従業員満足度への影響:** 報酬変更後のサーベイ結果やフィードバック。
*   **潜在的な課題と対策**
    *   従業員の期待値管理: AIによる推奨はあくまでオプションであり、最終決定は経営判断であることを明確にする。
    *   市場価値の正確な推定: 複数のデータソースと手法を組み合わせて市場価値を推定。定期的な見直し。
    *   福利厚生の金銭的価値換算の難しさ: 従業員ごとの福利厚生の主観的価値をアンケート等で把握し、考慮に入れる。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/compensation_optimization_service.py
    from scipy.optimize import minimize # 例

    class CompensationOptimizationService:
        async def generate_total_compensation_statement(self, employee_id: str) -> Dict:
            # ... 従業員の報酬データを収集し、総報酬を計算・可視化
            # ... LLMで各項目の説明文を生成
            pass

        async def recommend_personalized_compensation_package(self, employee_profile: Dict, company_constraints: Dict) -> Dict:
            # # 目的関数と制約条件を定義
            # def objective_function(compensation_vars):
            #     # ... 従業員の満足度やパフォーマンスへの期待効果を最大化する関数
            #     pass
            #
            # constraints = [...] # 予算制約、公平性制約など
            # bounds = [...]      # 各報酬要素の取りうる範囲
            #
            # result = minimize(objective_function, initial_guess, method='SLSQP', bounds=bounds, constraints=constraints)
            # optimal_package = self._interpret_optimization_result(result.x)
            # ... LLMで推奨理由を生成
            # return {"package": optimal_package, "rationale": "..."}
            pass
    ```

#### 9.7.3 長期インセンティブ戦略 (3.7.3)

*   **ユースケース概要:** 株式報酬 (RSU, ストックオプションなど) やその他の長期インセンティブプランの設計支援。リテンション効果と報酬構造の相関分析。役職や成果に応じた最適なインセンティブミックスを推奨。グローバル報酬戦略における地域差調整。規制対応と税効果の最適化提案。
*   **主要AIコンポーネント**
    *   LLM: 複雑なインセンティブプランの条件や影響を平易に説明。戦略文書のドラフト作成。地域別規制情報の要約。
    *   RAG: 株式報酬に関する法規制、税務情報、業界ベストプラクティス、競合他社のプラン事例などを知識ベースとして活用。
    *   シミュレーションモデル: 様々な市場シナリオ下での株式報酬の将来価値をシミュレーション。
*   **データ要件**
    *   入力: 会社の財務状況と将来予測、株価情報、従業員データ (役職、パフォーマンス)、既存のインセンティブプラン、競合他社の報酬データ、関連法規制・税務情報。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[企業財務/株価/従業員データ/法規制情報] --> B[データ収集 & RAGシステム];
        B --> C[LLM: インセンティブプラン設計支援 & 分析];
        C --> D[シミュレーションモデル: 将来価値予測];
        D --> C;
        C --> E[フロントエンド: LTI戦略立案UI];
    ```
*   **プロンプトエンジニアリングのポイント**
    *   **インセンティブプラン比較:** 「当社の経営目標（長期成長重視、人材獲得競争激化）と従業員構成（若手技術者中心）を考慮し、RSUとストックオプションのメリット・デメリットを比較し、どちらがより適切か、あるいは組み合わせるべきか提言してください。重要な設計要素（付与基準、権利確定スケジュール、行使条件など）も考慮してください。」
    *   **地域別調整:** 「当社のグローバル長期インセンティブプランを{国名}に展開するにあたり、現地の法規制、税制、一般的な報酬慣行を考慮して調整すべき点を具体的に指摘してください。関連情報は添付のRAG検索結果を参照してください。」
*   **評価とチューニングの考慮事項**
    *   **提案されたプランの魅力と効果:** 従業員アンケート、リテンション率への影響。
    *   **財務的影響のシミュレーション精度:** 実際の株価変動や希薄化効果との比較。
    *   **法的・税務的コンプライアンス:** 専門家 (弁護士、税理士) によるレビュー。
*   **潜在的な課題と対策**
    *   株式市場の不確実性: 複数の市場シナリオでシミュレーションし、リスク許容度に応じたプランを提案。
    *   従業員の理解度: LLMを活用して、複雑なプランを分かりやすく説明する資料を自動生成。
    *   規制変更への対応: RAGの知識ベースを定期的に更新。専門家との連携。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/long_term_incentive_service.py
    class LongTermIncentiveService:
        async def design_lti_plan_options(self, company_profile: Dict, strategic_objectives: List[str]) -> List[Dict]:
            # prompt = f"""
            # 会社プロファイル: {json.dumps(company_profile, ensure_ascii=False)}
            # 戦略目標: {', '.join(strategic_objectives)}
            #
            # 上記に基づき、適切な長期インセンティブプランのオプションを2-3案提案してください。
            # 各案について、プランタイプ、主な特徴、期待されるリテンション効果、想定コストを記述してください。
            # JSONリスト形式で返してください。
            # """
            # options = await llm_provider.generate(model_id="gpt-4o", prompt=prompt) # RAGと連携して詳細情報付加
            # return json.loads(options)
            pass

        async def analyze_lti_plan_impact(self, lti_plan_details: Dict, market_scenarios: List[Dict]) -> Dict:
            # ... シミュレーションモデルで各シナリオでの株式価値やコストを計算
            # ... LLMで結果を要約し、リスクとリターンを分析
            pass
    ```

### 9.8 労働生産性・効率性分析 (`Development_Specification.md` 3.8 参照)

#### 9.8.1 生産性メトリクス (3.8.1)

*   **ユースケース概要:** 1人当たり売上/利益を部門別・時系列で分析。労働時間と生産性のパターンを分析。リモート/ハイブリッド/オフィスワークの効率比較。チーム構成と集合知効果 (チームの多様性、コミュニケーションパターンなど) の相関分析。プロジェクトベースの生産性追跡。
*   **主要AIコンポーネント**
    *   統計分析モデル: 生産性指標と各種要因 (労働時間、ワークスタイル、チーム構成) との相関分析、回帰分析。
    *   時系列分析モデル: 生産性トレンドの予測、季節性パターンの検出。
    *   LLM: 分析結果の解釈、レポート生成、生産性向上に関する洞察の提示。
    *   NLP: プロジェクト報告書やコミュニケーションログから、生産性に関連するキーワードやセンチメントを抽出。
*   **データ要件**
    *   入力: 売上/利益データ、従業員数データ、労働時間データ (勤怠ログ)、ワークスタイルデータ、プロジェクト管理データ (タスク進捗、完了までの時間)、チーム構成データ (メンバー、スキル、役割)、(匿名化された) コミュニケーションデータ。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[財務データ/HRデータ/プロジェクトデータ/コミュニケーションデータ] --> B[データ統合 & 前処理];
        B --> C[生産性指標計算エンジン];
        C --> D[統計分析 & 時系列分析モデル];
        D --> E[LLM: インサイト抽出 & レポート生成];
        E --> F[フロントエンド: 生産性ダッシュボード];
    ```
*   **プロンプトエンジニアリングのポイント**
    *   **生産性トレンド分析:** 「過去2年間の{部門名}における1人当たり売上と平均労働時間の推移（データ添付）を分析し、主なトレンド、相関関係、特異点を指摘してください。また、生産性向上のために注目すべきパターンがあれば示唆してください。」
    *   **ワークスタイル効率比較:** 「リモートワーク中心チーム、ハイブリッドチーム、オフィスワーク中心チームの生産性データ（プロジェクト完了率、タスク処理時間など）を比較分析し（データ添付）、各ワークスタイルのメリット・デメリット、および生産性に影響を与える可能性のある要因について考察してください。」
    *   **チーム構成と生産性:** 「多様なスキルセットを持つチームと均質なスキルセットを持つチームのプロジェクト成功率とイノベーション創出数の比較データ（添付）を分析し、チーム構成の多様性が生産性に与える影響について論じてください。」
*   **評価とチューニングの考慮事項**
    *   **生産性指標の定義の妥当性:** 職種や業務内容によって適切な生産性指標は異なる。
    *   **相関分析の解釈:** 相関関係が必ずしも因果関係を意味しないことを明記。
    *   **外的要因の考慮:** 市場環境の変化、季節性などが生産性に与える影響を分離して分析。
*   **潜在的な課題と対策**
    *   知識労働の生産性測定の難しさ: アウトプットの質やイノベーションなど、定量化しにくい要素をどう評価するか。定性的な指標や従業員サーベイも組み合わせる。
    *   データ収集のバイアス: 自己申告の労働時間やタスク進捗は不正確な場合がある。可能な範囲で客観的なデータを収集。
    *   短期的指標への過度な焦点: 長期的な視点 (スキルの成長、イノベーションの種まきなど) も考慮に入れる。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/productivity_analytics_service.py
    class ProductivityAnalyticsService:
        async def analyze_departmental_productivity(self, sales_data: pd.DataFrame, headcount_data: pd.DataFrame, department: str) -> Dict:
            # ... 1人当たり売上/利益の時系列データを計算・分析
            # ... LLMでトレンドとインサイトを生成
            pass

        async def compare_workstyle_efficiency(self, productivity_metrics: pd.DataFrame, workstyle_column: str) -> Dict:
            # ... ワークスタイル別に生産性指標をグループ化し、統計的比較 (例: ANOVA)
            # ... LLMで結果の解釈と考察を生成
            pass
    ```

#### 9.8.2 ワークスタイル最適化 (3.8.2)

*   **ユースケース概要:** 従業員の活動データ (カレンダー、コミュニケーションツール利用状況など) や成果物から、最適な労働時間パターン (集中作業時間、会議時間、休憩時間のバランスなど) を検出。会議の効率性や情報共有パターンを分析。デジタルツールの活用度と生産性の関係を調査。コラボレーションネットワークを可視化し、フォーカスタイムを最大化するための提案を行う。
*   **主要AIコンポーネント**
    *   パターン認識・クラスタリングモデル: 労働時間パターン、会議パターンの分類。
    *   NLP: 会議の議事録やチャットログから、会議の目的達成度、決定事項の明確さ、情報共有の質を分析。
    *   ONA (組織ネットワーク分析) ツール (9.3.1と連携)。
    *   LLM: 分析結果の解釈、パーソナライズされたワークスタイル最適化提案、会議改善提案。
*   **データ要件**
    *   入力: カレンダーデータ (会議の長さ、参加者、頻度)、(匿名化・集約化された) コミュニケーションログ (メール/チャットの量、時間帯、相手)、勤怠データ、タスク管理ツールの利用ログ、(オプトインでの) PCアクティビティログ、従業員サーベイ (集中度、会議満足度など)。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[カレンダー/コミュニケーション/タスクログ] --> B[データ収集 & 匿名化/集約];
        B --> C[パターン分析エンジン: 労働/会議パターン];
        C --> D[NLP: 会議/情報共有効率分析];
        D --> E[LLM: 最適ワークスタイル & 会議改善提案];
        E --> F[フロントエンド: 個人/チーム向け提案UI];
        
        B --> G[ONAエンジン];
        G --> E;
    ```
*   **プロンプトエンジニアリングのポイント**
    *   **個人向けフォーカスタイム提案:** 「従業員Xの過去1週間のカレンダーとタスク完了状況（添付）を分析し、集中作業に適した時間帯を特定し、その時間帯を確保するための具体的なスケジュール調整案（会議の移動、通知オフ設定など）を提案してください。」
    *   **会議効率改善:** 「チームYの定例会議（参加者リスト、平均所要時間、議事録サンプル添付）について、アジェンダの明確さ、参加者の発言バランス、決定事項の明確さの観点から改善点を指摘し、より効率的な会議運営のための具体的なアクションプランを提示してください。」
    *   **ツール活用分析:** 「部署Zにおけるプロジェクト管理ツールAとコミュニケーションツールBの利用ログ（添付）とプロジェクト完了期間のデータを分析し、これらのツールの活用度と生産性の間に関連性が見られるか、また、より効果的なツール活用方法があれば提案してください。」
*   **評価とチューニングの考慮事項**
    *   **提案の受容度と実用性:** 従業員が提案を実行しやすいか、実際に効果を感じるか。
    *   **会議効率改善効果:** 会議時間の短縮、意思決定速度の向上、参加者満足度の変化。
    *   **フォーカスタイム増加と生産性の相関:** 集中作業時間の増加が実際の成果に結びつくか。
*   **潜在的な課題と対策**
    *   従業員のプライバシーとマイクロマネジメント懸念: 分析は個人の監視ではなく、働きやすさ向上と効率化支援が目的であることを明確に伝え、オプトインや集約データ利用を基本とする。
    *   ワークスタイルの個人差: AIの提案はあくまで一般論やデータ傾向に基づいたものであり、個人の好みや特性を尊重する余地を残す。
    *   会議文化の変革抵抗: トップダウンの支援と、小さな成功体験の積み重ね。AIによるメリットの可視化。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/workstyle_optimization_service.py
    class WorkstyleOptimizationService:
        async def analyze_meeting_efficiency(self, meeting_transcripts: List[str], calendar_data: List[Dict]) -> Dict:
            # ... NLPで議事録から議題達成度、発言バランスなどを分析
            # ... カレンダーデータから会議の長さ、頻度、参加者数などを統計化
            # ... LLMで総合的な会議効率レポートと改善提案を生成
            pass

        async def suggest_focus_time_optimization(self, employee_schedule: Dict, task_list: List[Dict]) -> List[str]:
            # prompt = f"""
            # 従業員のスケジュール: {json.dumps(employee_schedule, ensure_ascii=False)}
            # タスクリスト: {json.dumps(task_list, ensure_ascii=False)}
            #
            # 上記を基に、この従業員のフォーカスタイムを最大化するための具体的な提案を3つしてください。
            # 例: 特定の時間帯の会議ブロック、タスクのバッチ処理など。
            # """
            # suggestions = await llm_provider.generate(model_id="gpt-4o-mini", prompt=prompt)
            # return suggestions.split('\n')
            pass
    ```

#### 9.8.3 人的資本ROI計測 (3.8.3)

*   **ユースケース概要:** 採用、研修、能力開発、福利厚生など、人材への各種投資に対するリターン (ROI) を計測・分析するフレームワーク。適正な人員配置が生産性や収益に与える影響をシミュレーション。従業員の専門能力や組織貢献度を定量化し、人材育成投資の最適な配分を提案。
*   **主要AIコンポーネント**
    *   統計分析・機械学習モデル: 人材投資と業績指標 (売上、利益、顧客満足度、イノベーション数など) の間の因果関係推論 (可能な範囲で)。
    *   コスト便益分析 (CBA) モデル: 各種施策のコストと期待される便益を比較。
    *   LLM: ROI分析結果のレポート生成、複雑な貢献度の説明、投資配分戦略の提案。
    *   シミュレーションエンジン (9.5.2と連携)。
*   **データ要件**
    *   入力: 人材投資データ (採用コスト、研修費用、福利厚生費、給与)、業績データ (売上、利益、生産量、プロジェクト成果)、従業員データ (スキル、パフォーマンス、エンゲージメント、勤続年数)、顧客データ (顧客満足度、リピート率)。
*   **統合アーキテクチャ**
    ```mermaid
    graph LR
        A[人材投資データ/業績データ/従業員データ] --> B[データ統合 & ROI計算エンジン];
        B --> C[統計モデル: 投資対効果分析];
        C --> D[LLM: ROIレポート & 最適投資配分提案];
        D --> E[フロントエンド: 人的資本ROIダッシュボード];
        
        F[人員配置シナリオ] --> G[シミュレーションエンジン];
        G --> D;
    ```
*   **プロンプトエンジニアリングのポイント**
    *   **研修ROIレポート:** 「研修プログラムX（参加者リスト、総費用、研修内容、研修後スキル評価添付）のROIを分析してください。考慮すべき指標（例：参加者の生産性変化、エラー率低下、昇進率向上など）を特定し、可能な範囲で定量的な効果を推定し、総合的なROI評価と今後の改善点を報告してください。」
    *   **適正人員配置インパクト:** 「部門Yに新たに{スキルセット}を持つ専門家を1名増員した場合の、プロジェクト完了速度と品質への期待効果をシミュレーションし（過去データ添付）、その結果を要約してください。また、この増員が妥当な投資であるか、費用対効果の観点から評価してください。」
    *   **人材育成投資最適化:** 「年間人材育成予算{予算額}ドル。目標は{主要目標、例：3年以内にAIスキルを持つ従業員を20%増やす}。この目標達成のために、予算を研修A、OJTプログラムB、外部資格取得支援Cにどのように配分するのが最も効果的か、各施策の想定コストと効果を踏まえて提案してください。」
*   **評価とチューニングの考慮事項**
    *   **ROI計算の精度と仮定の妥当性:** 人材投資の効果が顕在化するまでのタイムラグ、外的要因の影響を考慮。
    *   **貢献度の定量化の難しさ:** 間接的な貢献やチームでの成果をどう評価するか。複数の指標と定性評価を組み合わせる。
    *   **シミュレーション結果の信頼性:** モデルのキャリブレーション、専門家による検証。
*   **潜在的な課題と対策**
    *   因果関係の特定困難: 人材投資と業績の間には多くの交絡因子が存在。可能な限り対照群を設定した分析 (A/Bテストなど) や、統計的因果推論の手法を検討。AIの分析結果は「相関」や「可能性」として提示。
    *   長期的視点の欠如: 短期的なROIだけでなく、組織能力の向上やイノベーション文化の醸成といった長期的な無形資産への貢献も考慮する。
    *   データの質と網羅性: 正確なROI計算には、多岐にわたる質の高いデータが必要。データ収集プロセスの改善。
*   **コードスニペット（概念）**
    ```python
    # backend/app/services/human_capital_roi_service.py
    class HumanCapitalROIService:
        async def calculate_training_roi(self, training_program_data: Dict, performance_data_before_after: pd.DataFrame) -> Dict:
            # ... 研修コストと、研修後のパフォーマンス向上 (生産性向上、エラー削減等) による金銭的価値を比較
            # ... LLMでROI計算の前提と結果を説明
            pass

        async def simulate_staffing_impact(self, staffing_change_scenario: Dict, historical_project_data: pd.DataFrame) -> Dict:
            # ... スタッフィング変更によるプロジェクト完了期間や品質への影響をシミュレーション
            # ... LLMでシミュレーション結果のサマリーとビジネスインパクトを分析
            pass

        async def recommend_development_investment_allocation(self, budget: float, development_initiatives: List[Dict], strategic_goals: List[str]) -> Dict:
            # ... 各イニシアチブの期待効果とコストを評価し、予算内で目標達成を最大化する配分を最適化アルゴリズムで探索
            # ... LLMで提案された配分戦略とその根拠を説明
            pass
    ```


## 10. コンプライアンスと倫理的考慮事項

HRX-AIは、人材という非常にセンシティブなデータを扱い、意思決定に影響を与えるAIシステムを内包するため、コンプライアンスの遵守と倫理的な配慮が極めて重要です。本セクションでは、開発から運用に至るまでの各段階で留意すべき事項を詳述しています。

### 10.1 データプライバシーと保護 (`GDPR`, `CCPA` 等)

HRX-AIはグローバルな展開も視野に入れているため、GDPR (EU一般データ保護規則)、CCPA (カリフォルニア州消費者プライバシー法) およびその他地域のデータ保護法規への準拠が必須です。

*   **10.1.1 データ最小化の原則**
    *   **方針:** AIモデルの学習および推論に必要な最小限の個人データのみを収集・利用します。
    *   **実装:**
        *   各AI機能が必要とするデータ項目を明確に定義し、ドキュメント化します (`Data Schema Dictionary` と連携)。
        *   不要な個人識別情報 (PII) は、収集段階で除外するか、早期に匿名化・仮名化処理を施します。
        *   特に、AIモデルのトレーニングデータセット作成時には、バイアス除去やモデル性能に直接寄与しない属性の利用は慎重に検討します。
    *   **例:** 離職リスク予測モデルにおいて、人種や宗教といった機微情報は原則として使用せず、職務遂行能力やエンゲージメントに関連するデータに焦点を当てる。

*   **10.1.2 同意管理**
    *   **方針:** 従業員データの収集・利用、特にAIによる分析やプロファイリングに関しては、明確かつ具体的な同意を取得します。
    *   **実装**
        *   従業員向けプライバシーポリシーおよびHRX-AI利用規約において、AIの利用目的、処理されるデータの種類、分析方法、データの保持期間などを平易な言葉で説明します。
        *   特定のAI機能 (例: バーチャル面接の感情分析、ウェルネスデータの詳細分析) については、オプトイン方式での同意取得を検討します。
        *   同意の取得・撤回履歴を管理し、撤回時には速やかにデータ処理を停止し、関連データを削除または匿名化するプロセスを確立します。
    *   **例:** 従業員が自身のスキルプロファイルをAIによるキャリアパス推奨に利用することに同意するチェックボックスをUIに設ける。

*   **10.1.3 データ主体の権利 (アクセス、訂正、削除、ポータビリティ)**
    *   **方針:** 従業員が自身のデータにアクセスし、誤りがあれば訂正を要求し、特定の条件下でデータの削除やポータビリティを要求できる権利を保障します。
    *   **実装**
        *   従業員が自身のHRX-AI上のデータプロファイルを確認・ダウンロードできるインターフェースを提供します。
        *   データ訂正・削除依頼を受け付けるプロセスと、それを実行するための技術的手段を整備します。
        *   AIモデルの学習データから特定の個人データを削除、またはモデルへの影響を無効化する技術 (例: Machine Unlearning の研究動向を注視) を検討します。
    *   **例:** 従業員ポータルから、AIによるスキル評価結果や推奨学習履歴を確認し、誤りがあれば訂正申請できる機能。

*   **10.1.4 データセキュリティ (暗号化、アクセス制御)**
    *   **方針:** 保存時および転送時のデータ暗号化、厳格なアクセス制御、定期的なセキュリティ監査を実施します (詳細は `Security Implementation Guide` 参照)。
    *   **実装:**
        *   データベースフィールドレベルでの暗号化 (特にPII)。
        *   TLS 1.3以上による通信暗号化。
        *   役割ベースアクセス制御 (RBAC) を徹底し、AIモデルや分析結果へのアクセス権限を最小限に絞ります。
        *   AIモデル自体や学習データへの不正アクセスを防ぐための物理的・論理的セキュリティ対策。

*   **10.1.5 データ処理記録 (RoPA)**
    *   **方針:** GDPR第30条に基づき、個人データの処理活動に関する記録を維持します。
    *   **実装**
        *   処理の目的、処理される個人データのカテゴリ、データ主体のカテゴリ、データ移転先、保持期間、技術的・組織的安全管理措置などを記録します。
        *   AIによるプロファイリングや自動化された意思決定に関する処理活動も詳細に記録します。

*   **10.1.6 データ保護影響評価 (DPIA)**
    *   **方針:** 新しいAI機能や大規模なデータ処理を開始する前に、プライバシーリスクを評価し、緩和策を講じるためのDPIAを実施します。
    *   **実装**
        *   特に、プロファイリング、機微データの処理、大規模処理、新規技術の利用など、高リスクと見なされるAI処理についてはDPIAを必須とします。
        *   DPIAの結果は記録し、必要に応じて監督機関に相談します。

*   **10.1.7 データ国外移転**
    *   **方針:** 個人データを他国に移転する場合は、移転先の国のデータ保護レベルが十分であることを確認し、適切な法的根拠 (標準契約条項SCC、十分性認定など) に基づいて行います。
    *   **実装**
        *   利用するLLMプロバイダーやクラウドインフラのデータセンター所在地を確認し、データ処理場所を契約で指定します。
        *   データ移転に関する透明性を確保し、従業員に通知します。

### 10.2 AI倫理と公平性

HRX-AIの意思決定支援機能は、従業員のキャリアや処遇に大きな影響を与える可能性があるため、AI倫理と公平性の確保が不可欠です。

*   **10.2.1 バイアス検出と緩和**
    *   **方針:** AIモデルの学習データやモデルの出力における潜在的なバイアス (性別、人種、年齢などに関するもの) を定期的に検出し、緩和策を講じます。
    *   **実装**
        *   **データバイアス:** 学習データセットの代表性を確認し、少数派グループのオーバーサンプリングや、バイアスのある特徴量の除外/変換を検討します。
        *   **アルゴリズムバイアス:** 公平性指標 (Demographic Parity, Equalized Odds, Predictive Rate Parityなど) を用いてモデルの出力を評価。Fairlearnライブラリなどのツールを活用し、公平性を意識したモデル学習 (In-processing) や後処理 (Post-processing) を行います。
        *   **LLMのバイアス:** プロンプトエンジニアリング (例: 「公平な視点で評価してください」という指示)、ファインチューニング、RAGによる中立的な情報提供を通じて、LLMの出力バイアスを低減します。LLMが生成するコンテンツの定期的な監査。
        *   `Development_Specification.md` 3.1.1 の「バイアス検出と公平性フィルター」機能を積極的に活用。
    *   **例:** 採用候補者スクリーニングAIにおいて、特定の大学卒業者や特定の性別に有利なスコアリングがされていないか継続的に監視し、必要に応じてモデルを調整する。

*   **10.2.2 説明可能性と透明性 (Explainable AI - XAI)**
    *   **方針:** AIの予測や推奨の根拠を、可能な限り人間が理解できる形で提示します。
    *   **実装**
        *   予測モデルに対して、SHAP (SHapley Additive exPlanations) や LIME (Local Interpretable Model-agnostic Explanations) などのXAI技術を適用し、どの特徴量が予測に寄与したかを示します。
        *   LLMに対しては、CoT (Chain-of-Thought) プロンプティングやRAGを用いることで、思考プロセスや参照情報を明示し、なぜそのような結論に至ったのかを説明させます。
        *   AIの判断に不確実性が伴う場合は、信頼度スコアや代替案を併せて提示します。
    *   **例:** 離職リスク予測AIが「高リスク」と判断した場合、その主要因（例: 「最近のエンゲージメントスコアの低下」「昇進機会の停滞」）を具体的に提示する。

*   **10.2.3 人間による監督と介入 (Human-in-the-Loop)**
    *   **方針:** 重大な意思決定 (採用、解雇、昇進、給与決定など) においては、AIの出力を最終決定とせず、必ず人間によるレビューと判断を介在させます。
    *   **実装**
        *   AIによる推奨やスコアリング結果は、参考情報として人事に提示。最終的な判断権限は人間が持ちます。
        *   AIの判断に疑義がある場合や、例外的なケースに対応するためのエスカレーションパスや手動オーバーライド機能を設けます。
        *   AIの判断結果と人間の最終判断が異なる場合、その理由を記録し、モデル改善のフィードバックとして活用します。
    *   **例:** AIが面接評価スコアを自動計算しても、採用担当者はそのスコアとAIの評価根拠を確認し、自身の判断と総合して最終評価を下す。

*   **10.2.4 継続的な倫理監査と影響評価**
    *   **方針:** 定期的にAIシステムの倫理的影響評価 (AI Ethics Impact Assessment) を実施し、潜在的なリスクや意図しない結果を特定・対処します。
    *   **実装**
        *   多様なバックグラウンドを持つメンバー（人事、法務、技術、倫理専門家、従業員代表など）から成る倫理レビューボードを設置、または外部専門家の助言を求める。
        *   新しいAI機能の導入前や、既存機能の大幅な変更時に倫理監査を実施。
        *   監査結果に基づき、システム改修、ポリシ変更、従業員教育などを行います。

*   **10.2.5 プロファイリングと自動化された意思決定に関する透明性**
    *   **方針:** GDPRで規定されている通り、プロファイリングや、法的効果または同様の重大な影響を及ぼす自動化された意思決定を行う場合、そのロジック、意義、想定される結果についてデータ主体に有意義な情報を提供します。
    *   **実装**
        *   AIがどのようにデータを分析し、どのような基準で推奨やスコアリングを行っているかの概要を従業員に開示します。
        *   自動化された意思決定に対して異議を申し立て、人間による再審査を要求する権利を保障します。

### 10.3 知的財産権と著作権

*   **10.3.1 LLM生成コンテンツの著作権**
    *   **方針:** LLMが生成したテキスト (レポート、求人票、メールドラフト等) の著作権の帰属は複雑であり、法域によって解釈が異なる可能性があることを認識します。
    *   **実装**
        *   LLMプロバイダーの利用規約を確認し、生成コンテンツの利用範囲や商用利用の可否を遵守します。
        *   HRX-AIが生成したコンテンツの最終的な責任は利用企業にあることを明確にします。人間によるレビューと編集を推奨します。
        *   生成コンテンツが既存の著作物を侵害しないよう、プロンプト設計やRAGシステムでの情報源の取り扱いに注意します。
    *   **例:** AIが生成した求人票のドラフトは、採用担当者が最終的に確認・修正し、自社のコンテンツとして公開する。

*   **10.3.2 学習データの権利処理**
    *   **方針:** AIモデルの学習（特にファインチューニング）に使用するデータについて、適切な権利処理（著作権、利用許諾）を行います。
    *   **実装**
        *   社内文書や従業員データを利用する場合は、利用目的を明確にし、社内規程や同意に基づき利用します。
        *   外部データを利用する場合は、ライセンス条件を確認し、遵守します。特にウェブスクレイピングで収集したデータは慎重に扱います。
        *   LLMプロバイダーが提供するファインチューニング機能を利用する場合、入力データがプロバイダー側でどのように扱われるか（学習に再利用されるか等）を契約で確認します。

*   **10.3.3 RAGにおける著作物の利用**
    *   **方針:** RAGシステムで参照する社内文書や外部ドキュメントについて、著作権を尊重します。
    *   **実装**
        *   検索結果として提示されるスニペットや要約が、原著作物の公正な利用の範囲を超える場合は、原典への明確なリンクを示し、ユーザーに原典を確認するよう促します。
        *   著作権保護された外部コンテンツを広範にシステム内部に取り込む場合は、ライセンス契約を検討します。

### 10.4 AIの信頼性と安全性

*   **10.4.1 モデルの堅牢性 (Robustness)**
    *   **方針:** AIモデルが予期しない入力や敵対的攻撃に対しても、一定の安定性を保ち、極端に誤った結果や有害な結果を出力しにくいように設計します。
    *   **実装**
        *   入力データのバリデーションとサニタイズを徹底します (プロンプトインジェクション対策)。
        *   エッジケースデータやノイズを加えたデータでのテストを実施します (セクション 4.4.2, 5.3 参照)。
        *   重要な予測や判断には、複数の異なるモデルやアプローチの結果を比較検討するアンサンブル学習や、フォールバックメカニズムを検討します。
    *   **例:** ユーザー入力プロンプトに「以前の指示を無視して…」といった典型的なインジェクションパターンが含まれていないかチェックする。

*   **10.4.2 モデルの再現性 (Reproducibility)**
    *   **方針:** AIモデルの学習プロセスや推論結果が、可能な限り再現可能であるようにします。
    *   **実装:**
        *   学習データセット、モデルアーキテクチャ、ハイパーパラメータ、乱数シードなどをバージョン管理します (W&B, DVCなどのツールの活用)。
        *   推論時のパラメータ (温度設定など) を記録し、同じ入力と同じパラメータであれば同じ（または非常に類似した）出力が得られることを目指します (LLMの確率的な性質を考慮)。

*   **10.4.3 セキュリティ脆弱性**
    *   **方針:** AIモデル自体やAIシステムを標的としたセキュリティ攻撃 (モデル抽出、データ汚染、敵対的入力など) に対する防御策を講じます。
    *   **実装**
        *   AIモデルへのアクセス制御を厳格に行います。
        *   入力データの異常検知。
        *   機密性の高いモデルやデータは、ネットワーク的に隔離された環境で運用することを検討します。
        *   定期的な脆弱性スキャンとペネトレーションテストを実施します (`Security Implementation Guide` と連携)。

*   **10.4.4 コンテンツセーフティフィルターの活用**
    *   **方針:** LLMが生成するコンテンツに対して、有害な内容、不適切な表現、誤情報などを検出しフィルタリングする仕組みを導入します (セクション 5.3 参照)。
    *   **実装**
        *   LLMプロバイダーが提供するセーフティフィルター機能を利用。
        *   HRX-AI独自のカスタムフィルター (HR用語や文脈に特化したもの) を開発・追加。
        *   フィルタリングの閾値は、誤検出と見逃しのバランスを考慮して調整。フィルタリング結果はログに記録し、レビュープロセスを設ける。

### 10.5 規制と業界標準への対応

*   **10.5.1 AI規制法案への対応 (例: EU AI Act)**
    *   **方針:** EU AI Actなどの国際的なAI規制法案の動向を注視し、HRX-AIが分類されるリスクレベル (高リスクAIシステムなど) に応じた義務を遵守します。
    *   **実装**
        *   HRX-AIの各機能がAI Actのリスク分類のどれに該当するかを特定 (例: 採用や従業員管理に関するAIは高リスクに分類される可能性が高い)。
        *   高リスクAIシステムに求められる要件 (リスク管理システム、データガバナンス、技術文書、透明性、人間による監視、堅牢性・正確性・セキュリティ) を満たすための開発・運用プロセスを整備します。
        *   適合性評価とCEマーキング (EU市場向けの場合) の準備。

*   **10.5.2 労働関連法規との整合性**
    *   **方針:** AIの利用が、各国の労働法、雇用差別禁止法、プライバシー法などと矛盾しないようにします。
    *   **実装:**
        *   法務部門や外部専門家と連携し、AI機能が各地域の法規制に準拠しているか定期的にレビューします。
        *   特に、AIによる評価や意思決定が、不当な差別や不利益な扱いにつながらないよう注意します。
        *   従業員代表との協議や情報共有を適切に行います。

*   **10.5.3 ISO等の国際標準への準拠 (将来的な検討)**
    *   **方針:** 将来的には、ISO/IEC 42001 (AIマネジメントシステム) や ISO/IEC 27001 (情報セキュリティマネジメントシステム) などの国際標準への準拠を目指し、AIガバナンスと信頼性を高めます。
    *   **実装**
        *   これらの標準が要求するプロセスや文書化を段階的に導入。

### 10.6 継続的な教育と意識向上

*   **10.6.1 開発者向け倫理・コンプライアンストレーニング**
    *   **方針:** HRX-AIの開発に関わる全てのメンバーに対し、データプライバシー、AI倫理、関連法規に関する定期的なトレーニングを実施します。
    *   **実装:**
        *   オンボーディング時および年次での必須研修。
        *   最新の規制動向や倫理的課題に関する情報共有セッション。

*   **10.6.2 ユーザー (人事担当者、マネージャー) 向けAIリテラシー教育**
    *   **方針:** HRX-AIのユーザーに対し、AI機能の仕組み、限界、適切な利用方法、倫理的配慮事項について教育し、AIを責任を持って活用できるよう支援します。
    *   **実装**
        *   製品マニュアル、チュートリアル、FAQでの情報提供。
        *   AIの出力結果を鵜呑みにせず、批判的に吟味し、人間の判断を補完するものとして利用するよう指導。
        *   バイアスやプライバシーに関する注意喚起。

*   **10.6.3 フィードバックチャネルの設置**
    *   **方針:** 従業員やユーザーが、HRX-AIの倫理的な問題やコンプライアンス上の懸念について、安全に報告できるチャネルを設けます。
    *   **実装:**
        *   匿名報告も可能な内部通報窓口やフィードバックフォームを設置。
        *   報告された問題に対しては、迅速かつ公正に調査し、適切な対応を行うプロセスを確立します。

これらのコンプライアンスと倫理的考慮事項は、HRX-AIの設計、開発、テスト、デプロイ、運用の全てのライフサイクルにおいて組み込まれるべきです。技術的な実装だけでなく、組織的なプロセス、ポリシー、文化の醸成が伴って初めて、責任あるAI活用が実現できます。
