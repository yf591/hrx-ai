# HRX-AI 設計図・ビジュアル資料集

ここでは、次世代スーパー人事AIエージェント「HRX-AI」の構造、開発計画、ユーザー体験、データフロー、プロセスを視覚的に表現。

## 1. 技術スタック連携図

```mermaid
flowchart TB
    subgraph "クライアント層"
        Next["Next.js 14+<br>App Router"]
        React["React 18+"]
        TS["TypeScript 5+"]
        Tailwind["Tailwind CSS 3+"]
        ShadcnUI["Shadcn/UI"]
        TanStack["TanStack Query 5"]
        Zustand["Zustand"]
        Framer["Framer Motion"]
    end
    
    subgraph "API層"
        NextAPI["Next.js API Routes"]
        FastAPI["FastAPI (Python)"]
        tRPC["tRPC"]
        Webhooks["イベントドリブン<br>Webhooks"]
    end
    
    subgraph "AI/ML層"
        LangChain["LangChain/<br>LlamaIndex"]
        LLMs["GPT-4o/Claude 3/Gemini 2.0 or 2.5"]
        HF["HuggingFace<br>Transformers"]
        ONNX["ONNX Runtime"]
        PyTorch["PyTorch/JAX"]
    end
    
    subgraph "データ層"
        Supabase["Firebase/<br>Supabase"]
        VectorDB["ベクトル<br>データベース"]
        Redis["Redis/Upstash"]
        Pandas["Pandas/Polars"]
        Pipelines["Dagster/Airflow"]
    end
    
    subgraph "インフラ層"
        Edge["Vercel/Cloudflare"]
        Deploy["Railway/Fly.io"]
        GitHub["GitHub Actions"]
        Docker["Docker/K8s"]
        IaC["Terraform/Pulumi"]
    end
    
    Next --> |SSR/CSR| React
    Next --> |API Routes| NextAPI
    React --> TS
    React --> Tailwind
    React --> ShadcnUI
    React --> TanStack
    React --> Zustand
    React --> Framer
    
    NextAPI --> FastAPI
    NextAPI --> tRPC
    NextAPI --> Webhooks
    
    FastAPI --> LangChain
    FastAPI --> Pandas
    FastAPI --> Pipelines
    
    LangChain --> LLMs
    LangChain --> HF
    LangChain --> ONNX
    PyTorch --> LLMs
    
    TanStack --> Supabase
    FastAPI --> Supabase
    FastAPI --> VectorDB
    FastAPI --> Redis
    
    Webhooks --> Supabase
    
    Next --> Edge
    FastAPI --> Deploy
    GitHub --> Edge
    GitHub --> Deploy
    Docker --> Deploy
    IaC --> Edge
    IaC --> Deploy
    
    style Next fill:#b3e0ff,stroke:#0077b6
    style FastAPI fill:#caffbf,stroke:#38b000
    style LLMs fill:#ffd6a5,stroke:#cc5500
    style Supabase fill:#ffadad,stroke:#9d0208
    style Edge fill:#bdb2ff,stroke:#5a189a
```

## 2. 開発ガントチャート

```mermaid
gantt
    title HRX-AI 3ヶ月集中開発計画
    dateFormat  YYYY-MM-DD
    axisFormat %m-%d
    tickInterval 1week
    
    section 月1: 基盤構築
    プロジェクト設定・技術スタック構築   :a1, 2025-05-15, 7d
    認証・データモデル・UIフレームワーク :a2, after a1, 7d
    コアAPI・DB・初期AI統合             :a3, after a2, 7d
    基本ダッシュボード・初期デプロイ     :a4, after a3, 7d
    
    section 月2: コア機能実装
    タレントアクイジション機能          :b1, after a4, 7d
    パフォーマンス＆開発モジュール       :b2, after b1, 7d
    組織設計＆ワークフォース計画機能     :b3, after b2, 7d
    エンプロイーエクスペリエンス＆リテンション機能 :b4, after b3, 7d
    
    section 月3: 高度化＆最適化
    ピープルアナリティクス＆意思決定ハブ :c1, after b4, 7d
    AIモデル統合完成・パフォーマンス最適化 :c2, after c1, 7d
    UIリファインメント・UXの磨き上げ    :c3, after c2, 7d
    最終テスト・バグ修正・リリース準備   :c4, after c3, 7d
    
    section マイルストーン
    技術基盤完成               :milestone, after a4, 0d
    コア機能実装完了           :milestone, after b4, 0d
    本番リリース準備完了        :milestone, after c4, 0d
```

## 3. ユーザージャーニーマップ

```mermaid
journey
    title 人事マネージャーの HRX-AI ユーザージャーニー
    
    section オンボーディング
        プラットフォーム初回アクセス: 4: HR部長
        データインポート/統合: 3: HR部長, IT部門
        システム設定・カスタマイズ: 4: HR部長
        基本機能チュートリアル: 5: HR部長
        
    section 日常業務
        AIアシスタント活用: 5: HR部長
        インサイトダッシュボード閲覧: 5: HR部長, 経営層
        採用候補者スクリーニング: 4: 採用担当, HR部長
        従業員パフォーマンス評価: 4: HR部長, マネージャー
        リアルタイムエンゲージメント分析: 5: HR部長
    
    section 戦略的活用
        離職リスク分析と介入: 5: HR部長, マネージャー
        組織構造最適化シミュレーション: 4: HR部長, 経営層
        人材獲得戦略立案: 5: HR部長
        予測分析による人材計画: 5: HR部長, 経営層
        高度なAI推奨を活用した意思決定: 4: HR部長, 経営層
```

## 4. 機能マインドマップ

```mermaid
mindmap
  root((HRX-AI))
    採用最適化
      候補者スクリーニング
      予測採用分析
      面接最適化
      バイアス検出
      採用ROI計測
      チャネル効率分析
    人材開発
      パフォーマンス管理
      スキルマッピング
      成長計画
      メンタリング
      学習推奨
      キャリアシミュレーション
    組織設計
      組織分析
      人員計画
      ワークスタイル分析
      チーム最適化
      コラボレーション分析
      適正配置AI
    エンゲージメント
      リテンション予測
      センチメント分析
      ウェルネス管理
      早期介入
      ロイヤリティ強化
      コホート分析
    給与・報酬
      市場ベンチマーク
      公平性分析
      報酬最適化
      インセンティブ戦略
      総報酬ビジュアライザー
    生産性分析
      業績メトリクス
      ワークスタイル最適化
      人的資本ROI
      時間効率パターン
      チーム相乗効果
    戦略分析
      インサイトハブ
      予測シミュレーション
      What-If分析
      戦略アドバイザリー
      ビジネスKPI連携
      シナリオプランニング
    持続可能性
      DEI分析
      ESGフレームワーク
      コンプライアンス
      インパクト測定
      未来スキル予測
```

## 5. 離職リスク予測シーケンス図

```mermaid
sequenceDiagram
    actor HR as 人事マネージャー
    participant UI as HRX-AI画面
    participant API as APIレイヤー
    participant AI as AIエンジン
    participant DB as データベース
    participant N as 通知システム
    
    HR->>UI: リスク分析画面アクセス
    UI->>API: 部門データリクエスト
    API->>DB: 従業員データ取得
    DB-->>API: 従業員データ返却
    API-->>UI: 部門概要表示
    
    HR->>UI: 離職リスク分析実行
    UI->>API: 予測分析リクエスト
    API->>AI: リスク予測モデル実行
    AI->>DB: 履歴・活動データ取得
    DB-->>AI: 行動パターン・評価データ
    
    AI->>AI: マルチファクター予測実行
    AI-->>API: 予測結果・要因分析
    API-->>UI: リスクスコア・視覚化表示
    
    HR->>UI: 高リスク従業員詳細確認
    UI->>API: 従業員詳細リクエスト
    API->>DB: 詳細プロファイル取得
    DB-->>API: 完全プロファイル返却
    API->>AI: 介入推奨生成
    AI-->>API: パーソナライズド介入策
    API-->>UI: 詳細・推奨アクション表示
    
    HR->>UI: 介入アクション選択
    UI->>API: アクション記録
    API->>DB: 介入記録保存
    API->>N: マネージャーへ通知
    N-->>HR: アクション確認通知
    
    loop フォローアップ
        AI->>DB: 週次進捗確認
        AI->>N: リマインダー生成
        N-->>HR: フォローアップ通知
    end
```

## 6. AI採用プロセスフローチャート

```mermaid
flowchart TD
    Start([採用開始]) --> JD[ジョブディスクリプション作成]
    JD --> AIOptimize{AI最適化エンジン}
    AIOptimize -->|最適化版| Publish[求人公開]
    AIOptimize -->|改善提案| Revise[内容修正]
    Revise --> AIOptimize
    
    Publish --> Receive[応募受付]
    Receive --> AIScreen{AI自動スクリーニング}
    
    AIScreen -->|高マッチ| HighMatch[A評価候補者]
    AIScreen -->|中マッチ| MidMatch[B評価候補者]
    AIScreen -->|低マッチ| LowMatch[C評価候補者]
    
    HighMatch --> Interview1[一次面接]
    MidMatch --> HRReview{人事レビュー}
    LowMatch --> AutoReject[自動お断りメール]
    
    HRReview -->|進行| Interview1
    HRReview -->|却下| AutoReject
    
    Interview1 --> AIFeedback[AI面接分析]
    AIFeedback --> Score{評価判定}
    
    Score -->|高評価| Interview2[二次面接・最終面接]
    Score -->|要検討| Panel[評価会議]
    Score -->|不適合| Reject[お断り連絡]
    
    Panel -->|進行| Interview2
    Panel -->|却下| Reject
    
    Interview2 --> Final{最終判断}
    Final -->|採用| Offer[オファー提示]
    Final -->|却下| Reject
    
    Offer --> Response{候補者回答}
    Response -->|承諾| Onboarding[オンボーディング]
    Response -->|辞退| Analysis[辞退分析]
    
    Analysis --> Improve[採用プロセス改善]
    Onboarding --> Success([採用完了])
    Reject --> Feedback[フィードバック収集]
    Feedback --> Improve
    
    class AIOptimize,AIScreen,AIFeedback highlight
    classDef highlight fill:#ffd6a5,stroke:#cc5500
```

## 7. システムアーキテクチャ図

```mermaid
flowchart TB
    subgraph "Users"
        HR["HR担当者"]
        Manager["マネージャー"]
        Exec["経営層"]
    end
    
    subgraph "Frontend"
        Web["Webアプリ<br>(Next.js)"]
        Mobile["モバイル<br>(PWA)"]
    end
    
    subgraph "API Gateway"
        NextAPI["Next.js API<br>Routes"]
        EdgeFn["エッジ関数"]
    end
    
    subgraph "Backend Services"
        Auth["認証サービス"]
        FastAPI["FastAPI<br>バックエンド"]
        Analytics["分析エンジン"]
        Notify["通知サービス"]
    end
    
    subgraph "AI Layer"
        LLMs["大規模言語<br>モデル"]
        RAG["RAG エンジン"]
        Pred["予測モデル"]
    end
    
    subgraph "Data Layer"
        DB["Supabase/Firebase"]
        VectorDB["ベクター<br>データベース"]
        Cache["Redis<br>キャッシュ"]
        Storage["ストレージ"]
    end
    
    subgraph "Infrastructure"
        Vercel["Vercel"]
        Railway["Railway"]
        CDN["CDN"]
        Monitoring["モニタリング"]
    end
    
    HR --> Web
    HR --> Mobile
    Manager --> Web
    Manager --> Mobile
    Exec --> Web
    
    Web --> NextAPI
    Mobile --> NextAPI
    NextAPI --> EdgeFn
    
    EdgeFn --> Auth
    EdgeFn --> FastAPI
    EdgeFn --> Analytics
    
    Auth --> DB
    FastAPI --> DB
    FastAPI --> VectorDB
    FastAPI --> Cache
    Analytics --> DB
    Analytics --> VectorDB
    
    FastAPI --> LLMs
    FastAPI --> RAG
    FastAPI --> Pred
    Analytics --> Pred
    
    LLMs --> VectorDB
    RAG --> VectorDB
    Pred --> DB
    
    FastAPI --> Notify
    Notify --> HR
    Notify --> Manager
    
    Web --> Vercel
    NextAPI --> Vercel
    EdgeFn --> CDN
    FastAPI --> Railway
    Analytics --> Railway
    
    Vercel --> Monitoring
    Railway --> Monitoring
    
    style Web fill:#b3e0ff,stroke:#0077b6
    style FastAPI fill:#caffbf,stroke:#38b000
    style LLMs fill:#ffd6a5,stroke:#cc5500
    style DB fill:#ffadad,stroke:#9d0208
    style Vercel fill:#bdb2ff,stroke:#5a189a
```

## 8. データフロー図

```mermaid
flowchart LR
    subgraph "データソース"
        HRIS["HR情報システム"]
        ATS["採用管理システム"]
        Survey["サーベイデータ"]
        Communication["コミュニケーションツール"]
        External["外部データソース"]
    end
    
    subgraph "データ処理"
        Ingest["データ取込"]
        Transform["ETL処理"]
        Normalize["データ正規化"]
        Enrich["データエンリッチメント"]
    end
    
    subgraph "AIエンジン"
        Models["予測モデル群"]
        RAG["検索拡張生成"]
        NLP["自然言語処理"]
        Analytics["高度分析"]
    end
    
    subgraph "データストア"
        Main["メインDB"]
        Vector["ベクトルDB"]
        Cache["キャッシュ層"]
        Warehouse["データウェアハウス"]
    end
    
    subgraph "アプリケーション層"
        API["APIサービス"]
        RealTime["リアルタイム処理"]
        Batch["バッチ処理"]
    end
    
    subgraph "エンドポイント"
        UI["ユーザーインターフェース"]
        Reports["レポート"]
        Alerts["通知・アラート"]
        Integrations["外部連携"]
    end
    
    HRIS --> Ingest
    ATS --> Ingest
    Survey --> Ingest
    Communication --> Ingest
    External --> Ingest
    
    Ingest --> Transform
    Transform --> Normalize
    Normalize --> Enrich
    
    Enrich --> Main
    Enrich --> Vector
    
    Main --> API
    Main --> Batch
    Main --> Warehouse
    Vector --> RAG
    
    Main --> Models
    Vector --> Models
    Models --> Analytics
    
    RAG --> NLP
    NLP --> API
    Analytics --> API
    Analytics --> Batch
    
    API --> RealTime
    RealTime --> Cache
    Cache --> UI
    
    API --> UI
    API --> Reports
    Batch --> Reports
    RealTime --> Alerts
    API --> Integrations
    
    style Models fill:#ffd6a5,stroke:#cc5500
    style API fill:#caffbf,stroke:#38b000
    style UI fill:#b3e0ff,stroke:#0077b6
    style Main fill:#ffadad,stroke:#9d0208
```

## 9. エンプロイーライフサイクルマップ

```mermaid
graph TD
    subgraph "HRX-AI 従業員ライフサイクルサポート"
        Start([採用前]) --> Recruit[採用]
        Recruit --> Onboard[オンボーディング]
        Onboard --> Develop[成長・育成]
        Develop --> Retain[定着・エンゲージメント]
        Retain --> Transition[異動・昇進]
        Transition --> Develop
        Transition --> Offboard[退職]
        Offboard --> Alumni[アラムナイ]
        
        Recruit -.->|AI候補者マッチング| R1[インテリジェント<br>スクリーニング]
        Recruit -.->|面接最適化| R2[面接体験向上]
        Recruit -.->|予測分析| R3[成功予測]
        
        Onboard -.->|パーソナライズド<br>オンボーディング| O1[適応支援]
        Onboard -.->|早期フィードバック| O2[調整・最適化]
        
        Develop -.->|スキルギャップ分析| D1[能力開発]
        Develop -.->|キャリアパス| D2[成長計画]
        Develop -.->|メンタリング| D3[関係構築]
        
        Retain -.->|エンゲージメント<br>パルス| E1[リアルタイム<br>モニタリング]
        Retain -.->|離職リスク予測| E2[早期介入]
        Retain -.->|ウェルネス| E3[健全性維持]
        
        Transition -.->|内部異動最適化| T1[組織最適配置]
        Transition -.->|リーダーシップ準備| T2[昇進準備]
        
        Offboard -.->|知識移行| O3[ナレッジ保持]
        Offboard -.->|退職分析| O4[原因理解]
        
        Alumni -.->|関係維持| A1[再雇用機会]
        Alumni -.->|フィードバック活用| A2[継続的改善]
    end
    
    classDef aiNode fill:#ffd6a5,stroke:#cc5500;
    class R1,R2,R3,O1,O2,D1,D2,D3,E1,E2,E3,T1,T2,O3,O4,A1,A2 aiNode;
```

