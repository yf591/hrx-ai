
# HRX-AI開発概要書

## 1. プロジェクト概要

### 1.1 ビジョン

「HRX-AI」は、企業の人事業務を革新的に変革するHR AIエージェントです。最先端の生成AI技術と直感的なユーザーエクスペリエンスを融合させ、データドリブンな人材戦略の策定と実行を支援します。企業の人事部門を戦略的パートナーへと進化させる強力なツールを提供することを目指します。

### 1.2 主要目標

- 人事業務全体の効率を最大70%向上
- データに基づく戦略的人材意思決定を可能にする
- 従業員エンゲージメントと定着率の15-25%向上
- 採用コストの30%削減と質の向上
- 人材データの包括的な分析と予測を実現
- DEI（多様性・公平性・包摂性）指標の改善と測定

### 1.3 差別化要因

- 最新のAIスタックによる高パフォーマンスな実装
- AIとヒューマンインテリジェンスの最適な融合
- プライバシーファーストのアーキテクチャ設計
- シームレスなマルチプラットフォーム体験
- 段階的な拡張可能性を備えた設計
- エンタープライズレベルのセキュリティと規制対応

## 2. 技術スタック

### 2.1 フロントエンド

- **Next.js 14+ (App Router)**: サーバーコンポーネントとストリーミングSSRを活用
- **React 18+**: 最新のReactパターン（Server Components, Suspense, Hooks）
- **TypeScript 5+**: 型安全性と開発効率の向上
- **Tailwind CSS 3+**: 迅速なUI開発とカスタマイズ性
- **Shadcn/UI**: 再利用可能な高品質UIコンポーネント
- **TanStack Query 5**: サーバー状態管理の最適化
- **Zustand**: クライアント状態管理の軽量ソリューション
- **Framer Motion**: 洗練されたアニメーションと遷移効果

### 2.2 バックエンド

- **Python 3.11+ (FastAPI)**: 高性能な非同期APIフレームワーク
- **Firebase/Supabase（原則としてFirebaseを使用）**: 認証、リアルタイムデータベース、ストレージ
- **Stripe**: サブスクリプション課金システム
- **JWT/OIDC**: セキュアな認証トークン管理
- **Redis/Upstash**: 高速キャッシュと一時データ処理
- **tRPC**: 型安全なAPI通信

### 2.3 AI/ML コンポーネント

- **LangChain/LlamaIndex**: AIエージェント構築フレームワーク
- **- GPT-4o//Claude 3.7/Gemini 2.0**: 最新大規模言語モデル
- **Hugging Face Transformers**: カスタムモデル統合
- **ONNX Runtime**: クロスプラットフォームモデル最適化
- **PyTorch/JAX**: 高度なモデルトレーニングとチューニング
- **LLM評価フレームワーク**: AIパフォーマンス測定と改善

### 2.4 データ処理/分析

- **Pandas/Polars**: 高性能データ操作
- **Apache Arrow**: 高速データ交換フォーマット
- **D3.js**: カスタムインタラクティブ可視化
- **Observable Plot**: 宣言的データ可視化
- **Recharts/Visx**: Reactネイティブのチャートライブラリ
- **Dagster/Airflow**: データパイプライン管理

### 2.5 インフラストラクチャ/DevOps

- **Vercel/Cloudflare**: エッジコンピューティング最適化
- **Railway/Fly.io**: Pythonバックエンドデプロイ
- **GitHub Actions/Circle CI**: CI/CD自動化パイプライン
- **Docker/Kubernetes**: コンテナ化と調整
- **OpenTelemetry**: 分散トレーシングとモニタリング
- **Terraform/Pulumi**: インフラストラクチャ・アズ・コード

### 2.6 将来拡張（エンタープライズ統合）

- ゼロトラストセキュリティモデル
- クラウドアグノスティックデプロイメントオプション
- APIゲートウェイと管理システム
- オンプレミスデータ統合コネクタ
- マルチテナント対応アーキテクチャ

## 3. 機能モジュール詳細

### 3.1 AIパワードタレントアクイジション

#### 3.1.1 インテリジェント候補者スクリーニング

- 履歴書・職務経歴書の自動解析とスキルマッピング
- ジョブディスクリプションと候補者のセマンティックマッチング
- バイアス検出と公平性フィルター（拡張DEI対応）
- 候補者スコアリングと優先順位付け
- マルチモーダル評価（テキスト、音声、ビデオ分析）

#### 3.1.2 予測採用分析

- 候補者成功予測モデル
- 採用ROI予測ダッシュボード
- 未充足ポジションの影響分析
- 市場競争力分析とベンチマーキング
- 戦略的採用計画生成
- 採用チャネル別のコスト効率性分析
- 採用プロセスのファネル分析（応募→面接→内定→入社）
- 採用ソースと入社後パフォーマンスの相関
- 空席ポジションの影響コスト計算
- 募集職種別の応募傾向時系列予測

#### 3.1.3 面接最適化システム

- AIアシスト面接質問生成
- 回答評価フレームワーク
- 面接フィードバック分析
- 面接官パフォーマンス指標
- バーチャル初回面接シミュレーター

### 3.2 従業員パフォーマンス＆開発エンジン

#### 3.2.1 ダイナミックパフォーマンス管理

- 継続的フィードバックシステム
- 目標達成予測と進捗トラッキング
- 多角的評価データ統合
- パフォーマンストレンド分析
- コンテキスト認識型パフォーマンス評価

#### 3.2.2 スキル・コンピテンシーマッピング

- 組織スキルグラフ構築
- スキルギャップ自動検出
- 役割別必要スキルの動的更新
- 内部人材マーケットプレイス
- 未来指向型スキル予測

#### 3.2.3 パーソナライズド成長計画

- AIレコメンデーション学習リソース
- キャリアパス最適化シミュレーター
- メンタリングマッチングエンジン
- スキル習得進捗予測
- マイクロラーニング統合

### 3.3 組織設計＆ワークフォース計画

#### 3.3.1 組織分析エンジン

- チーム構成最適化モデル
- 組織ネットワーク分析
- コラボレーションパターン検出
- 組織健全性指標ダッシュボード
- 文化適合性マッピング

#### 3.3.2 戦略的人員計画

- 将来人材ニーズ予測
- シナリオベースの人員計画
- 能力ベースの配置最適化
- 労働市場トレンド分析
- スキルベース組織再構築プランナー

#### 3.3.3 ロケーション＆ワークスタイル分析

- リモート/ハイブリッド生産性分析
- 地域別人材データ可視化
- 勤務スタイル最適化提案
- 施設利用効率化レコメンデーション
- 従業員移動パターン最適化

### 3.4 エンプロイーエクスペリエンス＆リテンション

#### 3.4.1 予測リテンションエンジン

- マルチファクター離職リスク予測
- 早期介入アラートシステム
- 定着要因分析ダッシュボード
- 退職コスト計算と投資最適化
- 重要人材ロイヤリティプログラム
- コホート分析（採用時期別の定着率）
- 離職理由のセマンティック分類と予測モデル
- 勤続年数と離職リスクの多変量分析
- 部門/マネージャー別の離職パターン検出
- 競合他社への転職リスク予測

#### 3.4.2 エンゲージメント分析

- リアルタイムエンゲージメントパルス
- センチメント分析エンジン
- エンゲージメントドライバー検出
- カスタム介入効果測定
- 組織文化インパクト分析

#### 3.4.3 ウェルネス＆バランス

- ストレス・バーンアウト予測
- ワークロードバランシング推奨
- ウェルネスプログラム効果測定
- チームダイナミクス最適化
- メンタルヘルス早期支援システム

### 3.5 ピープルアナリティクス＆意思決定ハブ

#### 3.5.1 エクゼクティブインサイトダッシュボード

- ビジネスKPIと人材指標の相関分析
- 人材リスク早期警告システム
- 戦略マップと人材アラインメント
- ワンクリックエグゼクティブレポート
- DEIスコアカードと進捗追跡
- 従業員数の動的推移グラフ（月次/四半期/年次）
- KPI目標値との比較タイルビュー
- 直近のリスクアラートプッシュ通知システム
- 経営陣向け意思決定シナリオ比較
- モバイルファーストのエグゼクティブビュー

#### 3.5.2 予測分析＆シミュレーション

- "What-If" シナリオモデリング
- 組織変更影響シミュレーター
- 投資対効果最適化エンジン
- トレンド予測とアラート
- 戦略的人材配置シミュレーション

#### 3.5.3 AIアシスト意思決定サポート

- インテリジェント推奨アクション
- エビデンスベース意思決定フレームワーク
- コラボレーティブ意思決定ツール
- 決定トラッキングと効果測定
- 生成AIパワードストラテジックアドバイザー

### 3.6 持続可能な人材管理

#### 3.6.1 DEI分析とアクション

- 多様性指標と目標設定
- インクルージョン調査とフィードバックループ
- 公平性ギャップ検出システム
- DEIイニシアティブROI測定
- マイクロアグレッション検出と対応推奨

#### 3.6.2 ESG人材フレームワーク

- サステナビリティスキルマッピング
- 環境フットプリント削減提案
- ソーシャルインパクトイニシアティブ支援
- ガバナンス指標と改善提案
- 規制コンプライアンス支援

### 3.7 給与・報酬分析エンジン

#### 3.7.1 報酬ベンチマーキングと公平性分析

- 給与分布と市場ベンチマーク比較ダッシュボード
- 職種・等級別の給与箱ひげ図自動生成
- 給与と業績/勤続年数の相関可視化
- 給与満足度と実際の給与水準の関係分析
- 統計的検定を用いた給与格差検証（性別、年齢層、部門別など）

#### 3.7.2 報酬最適化エンジン

- 総報酬ステートメントのインタラクティブ視覚化
- パフォーマンスベース報酬シミュレーター
- 給与調整の財務インパクト予測
- 報酬パッケージのパーソナライズ推奨
- 福利厚生の効果性と利用率分析

#### 3.7.3 長期インセンティブ戦略

- 株式報酬・長期インセンティブの設計支援
- リテンションと報酬構造の相関分析
- 役職別最適インセンティブミックス推奨
- グローバル報酬戦略の地域調整
- 規制対応と税効率の最適化提案

### 3.8 労働生産性・効率性分析

#### 3.8.1 生産性メトリクス

- 1人当たり売上/利益の部門別時系列分析
- 労働時間と生産性のパターン分析
- リモート/ハイブリッド/オフィスワーク効率比較
- チーム構成と集合知効果の相関
- プロジェクトベース生産性追跡

#### 3.8.2 ワークスタイル最適化

- 最適な労働時間パターンの検出アルゴリズム
- 会議効率と情報共有パターン分析
- デジタルツール活用度と生産性の関係
- コラボレーションネットワーク可視化
- フォーカスタイム最適化提案

#### 3.8.3 人的資本ROI計測

- 人材投資リターン計算フレームワーク
- 研修・能力開発の生産性への影響測定
- 適正人員配置シミュレーション
- 専門能力と組織貢献度の定量化
- 人材育成投資の最適配分提案

## 4. アーキテクチャ設計

### 4.1 システムアーキテクチャ概要

```
[クライアント層]
  |-- Next.js App (SSR/CSR ハイブリッド)
  |-- React コンポーネント
  |-- PWA/ネイティブ機能
  |-- クライアントサイドAI処理
  
[API層]
  |-- Next.js API Routes (エッジ関数)
  |-- FastAPI (Python バックエンド)
  |-- tRPC エンドポイント
  |-- イベントドリブンWebhooks
  
[処理層]
  |-- AI モデル処理サービス
  |-- データ分析エンジン
  |-- バッチ処理システム
  |-- リアルタイムイベント処理
  
[データ層]
  |-- Firestore
  |-- ベクトルデータベース
  |-- Redis/Upstash キャッシュ
  |-- 構造化データストレージ
```

### 4.2 AIモデル統合アーキテクチャ

- RAGパターンによる組織知識活用
- フロントエンド軽量推論 (ONNX.js, TensorFlow.js)
- バックエンド高度推論 (Python PyTorch/JAX)
- ハイブリッドアプローチ (UI応答性と高度処理の両立)
- モデルバージョニングと段階的デプロイメント
- モデル精度モニタリングとドリフト検出

### 4.3 データフロー設計

- リアルタイムアップデート (Firebase RTDB)
- バッチ処理パイプライン (定期分析処理)
- イベント駆動アーキテクチャ (Webhooks/Pub-Sub)
- キャッシュ戦略 (Redis, Client-side, CDN)
- データ変換・正規化パイプライン

### 4.4 セキュリティ設計

- JWT/OIDC認証と細粒度アクセス制御
- データ暗号化 (転送中および保存時)
- 役割ベースのアクセス管理
- プライバシーバイデザイン原則
- セキュリティイベントモニタリング
- GDPRおよび地域別データ規制対応

### 4.5 スケーラビリティ設計

- マイクロフロントエンドアーキテクチャ
- API層の水平スケーリング
- データベースシャーディング
- サーバーレスコンピューティング活用
- エッジキャッシング最適化
- グローバル分散デプロイメント準備

## 5. 開発計画

### 5.1 3ヶ月開発タイムライン

#### 月1: 基盤構築フェーズ (4週間)

- 週1: プロジェクト設定、コア技術スタック構築
- 週2: 認証システム、基本データモデル、UIフレームワーク
- 週3: コアAPI、データベースセットアップ、初期AI統合
- 週4: 基本ダッシュボード、データ可視化基盤、初期デプロイ

#### 月2: コア機能実装フェーズ (4週間)

- 週1: タレントアクイジション機能セット
- 週2: パフォーマンス＆開発モジュール
- 週3: 組織設計＆ワークフォース計画機能
- 週4: エンプロイーエクスペリエンス＆リテンション機能

#### 月3: 高度機能と最適化フェーズ (4週間)

- 週1: ピープルアナリティクス＆意思決定ハブ完成
- 週2: AIモデル統合の完成とパフォーマンス最適化
- 週3: UIリファインメント、UXの磨き上げ
- 週4: 最終テスト、バグ修正、本番リリース準備

### 5.2 アジャイル開発プロセス

- 1週間スプリントサイクル
- 日次スタンドアップ（自己管理）
- スプリントレビューと振り返り
- 継続的インテグレーション/デプロイメント
- 自動化テスト駆動開発

### 5.3 優先順位とリスク管理

- クリティカルパス機能の優先実装
- 技術的負債の継続的管理
- リスク軽減策の事前計画
- フォールバックアプローチの設定
- 段階的デプロイ戦略

## 6. 収益化戦略

### 6.1 サブスクリプションモデル (Stripe実装)

- フリーミアム基本プラン
- プロフェッショナルプラン (中小企業向け)
- エンタープライズプラン (大企業向け)
- モジュール別アドオンオプション
- 使用量ベースの課金コンポーネント（AI処理）

### 6.2 価格戦略

- 従業員数ベースの段階的価格設定
- 年間契約割引
- 早期導入者特別価格
- ホワイトラベルオプション
- リファラルプログラム

### 6.3 市場展開計画

- 初期ベータユーザープログラム
- 産業別垂直展開戦略
- パートナーシップとAPI統合プログラム
- コミュニティ構築とサポート体制
- インフルエンサーマーケティング戦略

### 6.4 持続可能な成長戦略

- カスタマーサクセスプログラム構築
- データ駆動型アップセル・クロスセル
- 顧客LTVの最大化施策
- グローバル市場展開ロードマップ
- SaaS指標最適化フレームワーク

## 7. 技術的実装詳細

### 7.1 AI機能実装方法

#### 7.1.1 自然言語処理パイプライン

```python
# FastAPI バックエンドでの実装例
from fastapi import FastAPI, Depends
from langchain.chat_models import ChatOpenAI
from langchain.chains import ConversationChain
from langchain.memory import ConversationBufferMemory
from pydantic import BaseModel

app = FastAPI()

class FeedbackAnalysisRequest(BaseModel):
    feedback_text: str
    employee_id: str
    context: dict = {}

# LLM処理サービス
class HRAssistantService:
    def __init__(self):
        self.llm = ChatOpenAI(model="gpt-4o")
        self.memory = ConversationBufferMemory()
        self.conversation = ConversationChain(
            llm=self.llm, 
            memory=self.memory,
            verbose=True
        )
        
    async def analyze_feedback(self, text: str, context: dict):
        prompt = f"""あなたは人事専門のAIアシスタントです。
        以下の従業員フィードバックを分析し、感情、主要な懸念事項、推奨アクションを特定してください。
        
        従業員コンテキスト:
        - 部門: {context.get('department', '不明')}
        - 役職: {context.get('title', '不明')}
        - 勤続年数: {context.get('tenure', '不明')}
        
        フィードバック:
        {text}
        
        以下の形式でJSON構造を返してください:
        ```json
        {{
            "sentiment": "ポジティブ/ニュートラル/ネガティブ",
            "sentiment_score": 0-10の数値,
            "key_concerns": ["懸念事項1", "懸念事項2"...],
            "key_positives": ["ポジティブな点1", "ポジティブな点2"...],
            "recommended_actions": ["推奨アクション1", "推奨アクション2"...],
            "priority_level": "高/中/低"
        }}
        ```
        """
        response = await self.llm.apredict(prompt)
        return response

hr_assistant = HRAssistantService()

@app.post("/api/analyze-feedback")
async def analyze_employee_feedback(request: FeedbackAnalysisRequest):
    result = await hr_assistant.analyze_feedback(
        request.feedback_text, 
        request.context
    )
    return result
```

#### 7.1.2 予測モデルインテグレーション

```typescript
// Next.js API Routeでの実装例 (app/api/predict/retention/route.ts)
import { NextRequest, NextResponse } from 'next/server';
import { getPredictionFromPython } from '@/lib/python-bridge';
import { withAuth } from '@/lib/auth/middleware';
import { recordAnalyticsEvent } from '@/lib/analytics';

export const runtime = 'edge';

async function handler(req: NextRequest) {
  if (req.method !== 'POST') {
    return NextResponse.json({ error: 'Method not allowed' }, { status: 405 });
  }
  
  try {
    const { employeeData } = await req.json();
    
    // 権限チェック
    const department = employeeData.department;
    const currentUser = req.user; // withAuthミドルウェアから
    if (!currentUser.canAccessDepartment(department)) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 403 });
    }
    
    // Python FastAPIエンドポイントにデータを送信
    const predictions = await getPredictionFromPython('/predict/retention', employeeData);
    
    // 使用履歴を記録
    await recordAnalyticsEvent('retention_prediction', {
      user: currentUser.id,
      department,
      riskedEmployees: predictions.high_risk_count
    });
    
    return NextResponse.json({
      riskScore: predictions.risk_score,
      contributingFactors: predictions.factors,
      recommendedActions: predictions.recommendations,
      confidenceLevel: predictions.confidence,
      similarCases: predictions.similar_precedents
    });
  } catch (error) {
    console.error('Prediction error:', error);
    return NextResponse.json({ error: 'Prediction failed' }, { status: 500 });
  }
}

export default withAuth(handler);
```

### 7.2 リアルタイムデータ処理フロー

#### 7.2.1 Firestoreリアルタイムアップデート（**要修正**）

```typescript
// 従業員データの変更を監視するhook
import { useEffect, useState } from 'react';
import { createClient } from '@supabase/supabase-js';
import { useAuth } from '@/lib/auth';
import { debounce } from '@/lib/utils';

const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
);

export function useEmployeeRealtime(employeeId: string) {
  const [employee, setEmployee] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);
  const { user } = useAuth();
  
  useEffect(() => {
    if (!employeeId || !user) return;
    
    // 初期データ取得
    const fetchEmployee = async () => {
      try {
        const { data, error: fetchError } = await supabase
          .from('employees')
          .select('*')
          .eq('id', employeeId)
          .single();
          
        if (fetchError) throw fetchError;
        setEmployee(data);
      } catch (err) {
        setError(err as Error);
        console.error('Error fetching employee:', err);
      } finally {
        setLoading(false);
      }
    };
    
    fetchEmployee();
    
    // リアルタイムサブスクリプション
    const subscription = supabase
      .channel(`employee-${employeeId}`)
      .on('postgres_changes', 
        { 
          event: '*', 
          schema: 'public', 
          table: 'employees',
          filter: `id=eq.${employeeId}`
        }, 
        (payload) => {
          setEmployee(payload.new);
          
          // 変更をAI分析サービスに送信（レート制限対応）
          debounce(() => {
            analyzeEmployeeChanges(payload.old, payload.new)
              .catch(console.error);
          }, 1000)();
        }
      )
      .subscribe();
      
    return () => {
      supabase.removeChannel(subscription);
    };
  }, [employeeId, user]);
  
  return { employee, loading, error };
}

// 変更分析関数
async function analyzeEmployeeChanges(oldData: any, newData: any) {
  // 主要な変更を検出
  const significantChanges = detectSignificantChanges(oldData, newData);
  if (!significantChanges.length) return;
  
  // AI分析を要求
  const response = await fetch('/api/analyze/employee-changes', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ oldData, newData, changes: significantChanges })
  });
  
  if (!response.ok) return;
  
  const insights = await response.json();
  
  // インサイトに基づいた推奨アクションがあれば通知
  if (insights.recommendations?.length > 0) {
    notifyRecommendations(insights.recommendations);
  }
}
```

#### 7.2.2 リアルタイムダッシュボード更新

```typescript
// 部門メトリクスのリアルタイム更新（React Server Component + Client Component）
// app/dashboard/[departmentId]/page.tsx
import { Suspense } from 'react';
import { DepartmentMetricsProvider } from '@/components/department/MetricsProvider';
import { MetricsDashboard } from '@/components/department/MetricsDashboard';
import LoadingSkeleton from '@/components/ui/LoadingSkeleton';
import { getDepartmentSummary } from '@/lib/data/departments';
import { Metadata } from 'next';

interface PageProps {
  params: { departmentId: string };
  searchParams: { timeRange?: string };
}

export async function generateMetadata({ params }: PageProps): Promise<Metadata> {
  const departmentId = params.departmentId;
  const dept = await getDepartmentSummary(departmentId);
  
  return {
    title: `${dept.name} Dashboard | HRX-AI`,
    description: `人員計画とパフォーマンス分析：${dept.name}部門`,
  };
}

export default async function DepartmentDashboardPage({ params, searchParams }: PageProps) {
  const departmentId = params.departmentId;
  const timeRange = searchParams.timeRange || '30d';
  
  // サーバーサイドでの初期データフェッチ
  const initialData = await getDepartmentSummary(departmentId, timeRange);
  
  return (
    <DepartmentMetricsProvider initialData={initialData} departmentId={departmentId} timeRange={timeRange}>
      <div className="p-6 space-y-8">
        <h1 className="text-2xl font-bold">{initialData.name} ダッシュボード</h1>
        
        <Suspense fallback={<LoadingSkeleton type="metrics" />}>
          <MetricsDashboard />
        </Suspense>
      </div>
    </DepartmentMetricsProvider>
  );
}
```

```typescript
// components/department/MetricsDashboard.tsx (Client Component)
'use client';

import { useEffect, useState } from 'react';
import { useDepartmentMetrics } from '@/hooks/useDepartmentMetrics';
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card';
import { MetricsChart } from '@/components/charts/MetricsChart';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { RealtimeIndicator } from '@/components/ui/RealtimeIndicator';
import { Button } from '@/components/ui/button';
import { Download, Share2, AlertTriangle } from 'lucide-react';

export function MetricsDashboard() {
  const { metrics, isLoading, error, exportData } = useDepartmentMetrics();
  const [activeTab, setActiveTab] = useState('overview');
  const [hasRiskAlert, setHasRiskAlert] = useState(false);
  
  // 高リスクアラートの検出
  useEffect(() => {
    if (metrics?.riskAnalysis?.highRiskCount > 5 || 
        metrics?.performanceData?.trend === 'declining') {
      setHasRiskAlert(true);
    } else {
      setHasRiskAlert(false);
    }
  }, [metrics]);
  
  if (isLoading) {
    return <p>メトリクスを読み込み中...</p>;
  }
  
  if (error) {
    return <div className="p-4 bg-red-50 text-red-800 rounded-md">データ取得エラー: {error.message}</div>;
  }
  
  return (
    <div className="space-y-6">
      <div className="flex justify-between items-center">
        <div className="flex items-center gap-2">
          <RealtimeIndicator />
          <span className="text-sm text-gray-500">リアルタイム更新</span>
        </div>
        
        <div className="flex items-center gap-2">
          {hasRiskAlert && (
            <Button variant="outline" className="gap-1 text-amber-600 border-amber-300">
              <AlertTriangle className="h-4 w-4" />
              高リスク警告あり
            </Button>
          )}
          <Button variant="outline" size="sm" onClick={exportData}>
            <Download className="h-4 w-4 mr-1" />
            エクスポート
          </Button>
          <Button variant="outline" size="sm">
            <Share2 className="h-4 w-4 mr-1" />
            共有
          </Button>
        </div>
      </div>
      
      <Tabs value={activeTab} onValueChange={setActiveTab}>
        <TabsList>
          <TabsTrigger value="overview">概要</TabsTrigger>
          <TabsTrigger value="performance">パフォーマンス</TabsTrigger>
          <TabsTrigger value="engagement">エンゲージメント</TabsTrigger>
          <TabsTrigger value="risk">リスク分析</TabsTrigger>
        </TabsList>
        
        <TabsContent value="overview" className="mt-6">
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
            <Card>
              <CardHeader className="pb-2">
                <CardTitle className="text-sm font-medium text-gray-500">総従業員数</CardTitle>
              </CardHeader>
              <CardContent>
                <p className="text-3xl font-bold">{metrics.headcount}</p>
                <p className="text-xs text-gray-500 mt-1">
                  前月比 {metrics.headcountChange > 0 ? '+' : ''}{metrics.headcountChange}%
                </p>
              </CardContent>
            </Card>
            
            {/* 他のカード類似 */}
          </div>
          
          <div className="mt-6">
            <Card>
              <CardHeader>
                <CardTitle>主要指標トレンド</CardTitle>
              </CardHeader>
              <CardContent>
                <MetricsChart data={metrics.overviewTrends} height={350} />
              </CardContent>
            </Card>
          </div>
        </TabsContent>
        
        {/* 他のタブコンテンツ */}
      </Tabs>
    </div>
  );
}
```

### 7.3 Next.js App Routerの高度な活用

#### 7.3.1 サーバーコンポーネントとストリーミングSSR

```tsx
// app/employees/[id]/page.tsx
import { Suspense } from 'react';
import { notFound } from 'next/navigation';
import { getEmployee } from '@/lib/data/employees';
import { EmployeeHeader } from '@/components/employee/Header';
import { EmployeePerformance } from '@/components/employee/Performance';
import { EmployeeEngagement } from '@/components/employee/Engagement';
import { EmployeeSkills } from '@/components/employee/Skills';
import { EmployeeCareer } from '@/components/employee/Career';
import { EmployeeActions } from '@/components/employee/Actions';
import { EmployeeTimeline } from '@/components/employee/Timeline';
import { EmployeeRiskCard } from '@/components/employee/RiskCard';
import { AIRecommendations } from '@/components/ai/Recommendations';
import LoadingSkeleton from '@/components/ui/LoadingSkeleton';

export default async function EmployeeProfile({ params }: { params: { id: string } }) {
  // サーバーサイドでの基本データ取得
  const employee = await getEmployee(params.id);
  
  if (!employee) {
    notFound();
  }

  return (
    <div className="container mx-auto py-6 px-4 space-y-8">
      <EmployeeHeader employee={employee} />
      
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <div className="lg:col-span-2 space-y-6">
          <Suspense fallback={<LoadingSkeleton type="performance" />}>
            <EmployeePerformance employeeId={params.id} />
          </Suspense>
          
          <Suspense fallback={<LoadingSkeleton type="engagement" />}>
            <EmployeeEngagement employeeId={params.id} />
          </Suspense>
          
          <Suspense fallback={<LoadingSkeleton type="timeline" />}>
            <EmployeeTimeline employeeId={params.id} />
          </Suspense>
        </div>
        
        <div className="space-y-6">
          <EmployeeActions employee={employee} />
          
          <Suspense fallback={<LoadingSkeleton type="risk" />}>
            <EmployeeRiskCard employeeId={params.id} />
          </Suspense>
          
          <Suspense fallback={<LoadingSkeleton type="skills" />}>
            <EmployeeSkills employeeId={params.id} />
          </Suspense>
          
          <Suspense fallback={<LoadingSkeleton type="career" />}>
            <EmployeeCareer employeeId={params.id} />
          </Suspense>
          
          <Suspense fallback={<LoadingSkeleton type="ai" />}>
            {/* AIレコメンデーションは重い処理なので最後にストリーミング */}
            <AIRecommendations employeeId={params.id} employeeType={employee.type} />
          </Suspense>
        </div>
      </div>
    </div>
  );
}
```

#### 7.3.2 サーバーアクションによる安全なデータ更新

```tsx
// app/actions/employee.ts
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
import { z } from 'zod';
import { db, auth } from '@/lib/db';
import { logActivity } from '@/lib/audit';
import { withAuth } from '@/lib/auth/server';
import { createAIAnalysis } from '@/lib/ai/employee-analysis';

// 入力バリデーションスキーマ
const employeeUpdateSchema = z.object({
  id: z.string().uuid(),
  title: z.string().min(2).max(100),
  department: z.string().uuid(),
  manager: z.string().uuid().optional(),
  salary: z.number().positive().optional(),
  performanceScore: z.number().min(0).max(10).optional(),
});

type ActionResponse = {
  success: boolean;
  error?: string;
  data?: any;
};

export const updateEmployeeData = withAuth(async (
  formData: FormData,
  { user, can }
): Promise<ActionResponse> => {
  // 権限チェック
  const departmentId = formData.get('department') as string;
  if (!await can('update:employee', { departmentId })) {
    return {
      success: false,
      error: 'この従業員データを更新する権限がありません'
    };
  }
  
  // データ抽出＆検証
  try {
    const employeeId = formData.get('employeeId') as string;
    const rawData = {
      id: employeeId,
      title: formData.get('title'),
      department: formData.get('department'),
      manager: formData.get('manager') || undefined,
      salary: formData.get('salary') ? Number(formData.get('salary')) : undefined,
      performanceScore: formData.get('performanceScore') ? 
        Number(formData.get('performanceScore')) : undefined,
    };
    
    const validatedData = employeeUpdateSchema.parse(rawData);
    
    // 従業員の現在のデータを取得
    const currentData = await db.employee.findUnique({
      where: { id: employeeId },
    });
    
    if (!currentData) {
      return { success: false, error: '従業員が見つかりません' };
    }
    
    // データ更新
    const updatedEmployee = await db.employee.update({
      where: { id: employeeId },
      data: {
        ...validatedData,
        updatedAt: new Date(),
        updatedBy: user.id,
      },
    });
    
    // AI分析を非同期で開始（大きな変更がある場合）
    if (
      currentData.performanceScore !== validatedData.performanceScore ||
      currentData.title !== validatedData.title ||
      currentData.department !== validatedData.department
    ) {
      createAIAnalysis({
        employeeId,
        changeType: 'profile_update',
        before: currentData,
        after: updatedEmployee,
      }).catch(console.error);
    }
    
    // 監査ログ
    await logActivity({
      actorId: user.id,
      action: 'employee.update',
      resourceType: 'employee',
      resourceId: employeeId,
      details: {
        changes: Object.entries(validatedData).filter(
          ([key, value]) => currentData[key] !== value
        ),
      },
    });
    
    // キャッシュを無効化して最新データを表示
    revalidatePath(`/employees/${employeeId}`);
    revalidatePath(`/departments/${validatedData.department}`);
    
    return { 
      success: true,
      data: { id: updatedEmployee.id }
    };
  } catch (error) {
    console.error('Employee update error:', error);
    return { 
      success: false, 
      error: error instanceof z.ZodError 
        ? '入力データが不正です'
        : 'データの更新中にエラーが発生しました'
    };
  }
});
```

### 7.4 高度なUI/UXパターン

#### 7.4.1 インタラクティブダッシュボード

```tsx
// components/analytics/InteractiveDashboard.tsx
'use client';

import { useState, useMemo, useCallback } from 'react';
import { 
  LineChart, BarChart, PieChart, ScatterPlot 
} from '@/components/charts';
import { FilterPanel } from '@/components/ui/FilterPanel';
import { DateRangePicker } from '@/components/ui/DateRangePicker';
import { Tabs, TabsList, TabsTrigger, TabsContent } from '@/components/ui/tabs';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';
import { Button } from '@/components/ui/button';
import { Download, RefreshCw, ChevronDown } from 'lucide-react';
import { 
  DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem 
} from '@/components/ui/dropdown-menu';
import { useDepartmentData } from '@/hooks/useDepartmentData';
import { exportToCsv, exportToPdf, shareReport } from '@/lib/export';
import { motion } from 'framer-motion';

export function InteractiveDashboard({ departmentId }: { departmentId: string }) {
  // 状態管理
  const [timeRange, setTimeRange] = useState({ start: '2024-01-01', end: '2024-12-31' });
  const [filters, setFilters] = useState<Record<string, any>>({});
  const [activeTab, setActiveTab] = useState('overview');
  const [chartType, setChartType] = useState<'line' | 'bar'>('line');
  
  // データフェッチ
  const { 
    data, 
    isLoading, 
    error, 
    refetch 
  } = useDepartmentData(departmentId, timeRange, filters);
  
  // メトリクスサマリー計算
  const metrics = useMemo(() => {
    if (!data) return null;
    
    const totalEmployees = data.employeeData?.length || 0;
    const highPerformers = data.employeeData?.filter(e => e.performanceScore > 8).length || 0;
    const atRiskCount = data.employeeData?.filter(e => e.retentionRisk > 0.7).length || 0;
    
    return {
      totalEmployees,
      highPerformers,
      atRiskCount,
      highPerformerRatio: totalEmployees ? (highPerformers / totalEmployees * 100).toFixed(1) : '0',
      atRiskRatio: totalEmployees ? (atRiskCount / totalEmployees * 100).toFixed(1) : '0',
      averagePerformance: data.avgPerformance?.toFixed(1) || '0',
    };
  }, [data]);
  
  // エクスポート処理
  const handleExport = useCallback(async (type: 'csv' | 'pdf') => {
    if (!data) return;
    
    if (type === 'csv') {
      await exportToCsv(data, `department-${departmentId}-${new Date().toISOString()}`);
    } else {
      await exportToPdf(data, `department-${departmentId}-report`);
    }
  }, [data, departmentId]);
  
  // シェア処理
  const handleShare = useCallback(async () => {
    if (!data) return;
    await shareReport({
      type: 'department',
      id: departmentId,
      filters,
      timeRange,
    });
  }, [data, departmentId, filters, timeRange]);
  
  if (error) {
    return (
      <div className="p-6 bg-red-50 border border-red-200 rounded-lg">
        <h3 className="text-red-800 font-medium">データ読み込みエラー</h3>
        <p className="text-red-600 mt-2">{error.message}</p>
        <Button onClick={() => refetch()} className="mt-4" variant="outline">
          <RefreshCw className="h-4 w-4 mr-2" /> 再試行
        </Button>
      </div>
    );
  }
  
  return (
    <motion.div 
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      className="space-y-6"
    >
      <div className="flex flex-col md:flex-row md:items-center justify-between gap-4">
        <h2 className="text-2xl font-bold">部門パフォーマンス分析</h2>
        <div className="flex flex-wrap items-center gap-2">
          <DateRangePicker 
            value={timeRange} 
            onChange={setTimeRange} 
            className="w-auto" 
          />
          
          <DropdownMenu>
            <DropdownMenuTrigger asChild>
              <Button variant="outline" size="sm">
                エクスポート <ChevronDown className="ml-2 h-4 w-4" />
              </Button>
            </DropdownMenuTrigger>
            <DropdownMenuContent align="end">
              <DropdownMenuItem onClick={() => handleExport('csv')}>
                CSVでエクスポート
              </DropdownMenuItem>
              <DropdownMenuItem onClick={() => handleExport('pdf')}>
                PDFレポート生成
              </DropdownMenuItem>
              <DropdownMenuItem onClick={handleShare}>
                レポート共有
              </DropdownMenuItem>
            </DropdownMenuContent>
          </DropdownMenu>
        </div>
      </div>
      
      {/* フィルターパネル */}
      <FilterPanel value={filters} onChange={setFilters} isLoading={isLoading} />
      
      {/* メトリクスサマリーカード */}
      {metrics && (
        <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-4">
          <MetricCard 
            title="総従業員数" 
            value={metrics.totalEmployees.toString()} 
            isLoading={isLoading}
          />
          <MetricCard 
            title="ハイパフォーマー" 
            value={`${metrics.highPerformers} (${metrics.highPerformerRatio}%)`}
            isLoading={isLoading}
          />
          <MetricCard 
            title="平均パフォーマンス" 
            value={metrics.averagePerformance}
            suffix="/10"
            isLoading={isLoading}
          />
          <MetricCard 
            title="離職リスク" 
            value={`${metrics.atRiskCount} (${metrics.atRiskRatio}%)`}
            isLoading={isLoading}
            isNegative={parseFloat(metrics.atRiskRatio) > 10}
          />
          {/* その他メトリクス */}
        </div>
      )}
      
      {/* タブ付きチャート */}
      <Card>
        <CardHeader className="pb-2">
          <div className="flex items-center justify-between">
            <CardTitle>詳細分析</CardTitle>
            <div className="flex items-center gap-2">
              <Button
                variant={chartType === 'line' ? 'default' : 'outline'}
                size="sm"
                onClick={() => setChartType('line')}
                className="h-8"
              >
                折れ線
              </Button>
              <Button
                variant={chartType === 'bar' ? 'default' : 'outline'}
                size="sm"
                onClick={() => setChartType('bar')}
                className="h-8"
              >
                棒グラフ
              </Button>
            </div>
          </div>
        </CardHeader>
        <CardContent>
          <Tabs value={activeTab} onValueChange={setActiveTab} className="mt-2">
            <TabsList className="grid grid-cols-4">
              <TabsTrigger value="overview">概要</TabsTrigger>
              <TabsTrigger value="performance">パフォーマンス</TabsTrigger>
              <TabsTrigger value="engagement">エンゲージメント</TabsTrigger>
              <TabsTrigger value="retention">定着率</TabsTrigger>
            </TabsList>
            
            <TabsContent value="overview" className="pt-4">
              {chartType === 'line' ? (
                <LineChart 
                  data={data?.overviewTrend || []} 
                  isLoading={isLoading}
                  height={350}
                />
              ) : (
                <BarChart 
                  data={data?.overviewDistribution || []} 
                  isLoading={isLoading}
                  height={350}
                />
              )}
            </TabsContent>
            
            {/* 他のタブコンテンツ */}
            <TabsContent value="performance" className="pt-4">
              {/* パフォーマンスチャート */}
            </TabsContent>
            
            <TabsContent value="engagement" className="pt-4">
              {/* エンゲージメントチャート */}
            </TabsContent>
            
            <TabsContent value="retention" className="pt-4">
              {/* 定着率チャート */}
            </TabsContent>
          </Tabs>
        </CardContent>
      </Card>
      
      {/* 下部アナリティクスグリッド */}
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mt-6">
        <Card>
          <CardHeader>
            <CardTitle>スキル分布</CardTitle>
          </CardHeader>
          <CardContent>
            {/* スキル分布チャート */}
          </CardContent>
        </Card>
        
        <Card>
          <CardHeader>
            <CardTitle>リスク要因分析</CardTitle>
          </CardHeader>
          <CardContent>
            {/* リスク分析チャート */}
          </CardContent>
        </Card>
      </div>
    </motion.div>
  );
}

// メトリクスカードコンポーネント
function MetricCard({ 
  title, 
  value, 
  suffix = '',
  isLoading = false,
  isNegative = false,
}) {
  return (
    <Card className={`${isNegative ? 'border-red-200' : ''}`}>
      <CardContent className="p-4">
        <p className="text-sm font-medium text-gray-500">{title}</p>
        <div className={`mt-2 ${isNegative ? 'text-red-600' : 'text-black'}`}>
          {isLoading ? (
            <div className="h-8 bg-gray-200 animate-pulse rounded w-16" />
          ) : (
            <p className="text-2xl font-bold">
              {value}{suffix}
            </p>
          )}
        </div>
      </CardContent>
    </Card>
  );
}
```

#### 7.4.2 AIアシスタントインターフェース

```tsx
// components/ai/AIAssistant.tsx
'use client';

import { useState, useRef, useEffect } from 'react';
import { Send, Sparkles, X, PlusCircle, ClipboardCheck, User, Bot } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Textarea } from '@/components/ui/textarea';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { DropdownMenu, DropdownMenuContent, DropdownMenuItem, DropdownMenuTrigger } from '@/components/ui/dropdown-menu';
import { Tooltip, TooltipContent, TooltipProvider, TooltipTrigger } from '@/components/ui/tooltip';
import { Badge } from '@/components/ui/badge';
import { ScrollArea } from '@/components/ui/scroll-area';
import { useAIAssistant } from '@/hooks/useAIAssistant';
import { useUser } from '@/hooks/useUser';
import { cn } from '@/lib/utils';
import { useToast } from '@/hooks/useToast';
import { motion, AnimatePresence } from 'framer-motion';
import { saveConversation, loadConversation } from '@/lib/ai/conversations';
import { usePathname } from 'next/navigation';

type Message = {
  id: string;
  role: 'system' | 'user' | 'assistant';
  content: string;
  timestamp: Date;
};

export function AIAssistant() {
  // 状態管理
  const [inputValue, setInputValue] = useState('');
  const [isExpanded, setIsExpanded] = useState(false);
  const [isTyping, setIsTyping] = useState(false);
  const [contextMode, setContextMode] = useState<'general' | 'page' | 'employee'>('general');
  const [aiChips, setAiChips] = useState<string[]>([]);
  
  // ユーティリティフック
  const { user } = useUser();
  const { toast } = useToast();
  const pathname = usePathname();
  const scrollRef = useRef<HTMLDivElement>(null);
  
  // AIアシスタントフック
  const { 
    messages, 
    isLoading, 
    sendMessage, 
    clearMessages,
    saveChatSession,
    loadChatSession
  } = useAIAssistant();
  
  // 自動スクロール処理
  useEffect(() => {
    if (scrollRef.current && messages.length > 0) {
      scrollRef.current.scrollTo({
        top: scrollRef.current.scrollHeight,
        behavior: 'smooth'
      });
    }
  }, [messages]);
  
  // 推奨プロンプトの生成
  useEffect(() => {
    const generateSuggestedPrompts = async () => {
      if (messages.length > 0 && !isLoading && !aiChips.length) {
        try {
          const response = await fetch('/api/ai/suggest-followups', {
            method: 'POST',
            body: JSON.stringify({
              messages: messages.slice(-3),
              context: { path: pathname }
            }),
            headers: { 'Content-Type': 'application/json' }
          });
          
          if (response.ok) {
            const data = await response.json();
            setAiChips(data.suggestions || []);
          }
        } catch (error) {
          console.error('Failed to get suggested prompts:', error);
        }
      } else if (messages.length === 0) {
        // 初期プロンプト提案
        setAiChips([
          "今月の離職リスクが高い従業員を教えて",
          "採用効率を上げるための提案をください",
          "チーム全体のエンゲージメントスコアの傾向を分析して"
        ]);
      }
    };
    
    generateSuggestedPrompts();
  }, [messages, isLoading, pathname]);
  
  // コンテキストに基づいたページ固有の情報取得
  useEffect(() => {
    if (pathname.includes('/employees/') && contextMode === 'page') {
      const employeeId = pathname.split('/').pop();
      if (employeeId) {
        // 従業員の基本情報を読み込み、AIコンテキストに追加
        // (実装は省略)
      }
    }
  }, [pathname, contextMode]);
  
  // メッセージ送信処理
  const handleSubmit = (e: React.FormEvent, text?: string) => {
    e.preventDefault();
    
    const messageToSend = text || inputValue.trim();
    if (!messageToSend) return;
    
    setAiChips([]);
    sendMessage(messageToSend, { contextMode, path: pathname });
    setInputValue('');
    setIsTyping(false);
  };
  
  // チップクリック処理
  const handleChipClick = (chip: string) => {
    handleSubmit(new Event('submit') as unknown as React.FormEvent, chip);
  };

  const toggleExpand = () => {
    setIsExpanded(!isExpanded);
    
    // 閉じる時にチップをリセット
    if (isExpanded) {
      setAiChips([]);
    } else {
      // 開く時に適切なチップを設定
      if (messages.length === 0) {
        setAiChips([
          "今月の離職リスクが高い従業員を教えて",
          "採用効率を上げるための提案をください",
          "チーム全体のエンゲージメントスコアの傾向を分析して"
        ]);
      }
    }
  };
  
  // 会話エクスポート処理
  const handleExport = async () => {
    const text = messages.map(m => 
      `${m.role === 'user' ? '👤' : '🤖'}: ${m.content}`
    ).join('\n\n');
    
    try {
      await navigator.clipboard.writeText(text);
      toast({
        title: "会話をコピーしました",
        description: "クリップボードに保存されました",
      });
    } catch (err) {
      toast({
        title: "コピーに失敗しました",
        description: "権限がないか、エラーが発生しました",
        variant: "destructive"
      });
    }
  };
  
  // 会話保存処理
  const handleSaveConversation = async () => {
    if (messages.length === 0) return;
    
    try {
      const id = await saveChatSession("HR Assistant Chat");
      toast({
        title: "会話を保存しました",
        description: "保存された会話は後で参照できます",
      });
    } catch (err) {
      toast({
        title: "保存に失敗しました",
        variant: "destructive"
      });
    }
  };
  
  return (
    <motion.div 
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      className={cn(
        "fixed bottom-4 right-4 bg-white rounded-lg shadow-lg transition-all duration-300 border border-gray-200 overflow-hidden z-50",
        isExpanded 
          ? "w-[95vw] md:w-[450px] lg:w-[500px] h-[600px]" 
          : "w-[200px] md:w-[250px] h-14"
      )}
    >
      {/* ヘッダー部分 */}
      <div className="flex items-center justify-between p-3 border-b bg-gradient-to-r from-blue-50 to-indigo-50">
        <div className="flex items-center space-x-2">
          <Avatar className="h-8 w-8 bg-blue-100">
            <AvatarImage src="/ai-assistant-avatar.png" />
            <AvatarFallback className="bg-blue-600 text-white">AI</AvatarFallback>
          </Avatar>
          <h3 className="font-medium text-blue-900">HR AIアシスタント</h3>
          <Badge variant={contextMode === 'general' ? 'outline' : 'default'} className="text-xs">
            {contextMode === 'general' ? '一般' : contextMode === 'page' ? 'ページコンテキスト' : '従業員データ'}
          </Badge>
        </div>
        
        <div className="flex items-center space-x-1">
          {isExpanded && (
            <>
              <TooltipProvider>
                <Tooltip>
                  <TooltipTrigger asChild>
                    <Button 
                      variant="ghost" 
                      size="icon"
                      className="h-7 w-7" 
                      onClick={handleExport}
                    >
                      <ClipboardCheck className="h-4 w-4" />
                    </Button>
                  </TooltipTrigger>
                  <TooltipContent side="top">
                    <p>会話をコピー</p>
                  </TooltipContent>
                </Tooltip>
              </TooltipProvider>
              
              <TooltipProvider>
                <Tooltip>
                  <TooltipTrigger asChild>
                    <Button 
                      variant="ghost" 
                      size="icon"
                      className="h-7 w-7" 
                      onClick={handleSaveConversation}
                    >
                      <PlusCircle className="h-4 w-4" />
                    </Button>
                  </TooltipTrigger>
                  <TooltipContent side="top">
                    <p>会話を保存</p>
                  </TooltipContent>
                </Tooltip>
              </TooltipProvider>
              
              <DropdownMenu>
                <DropdownMenuTrigger asChild>
                  <Button variant="ghost" size="icon" className="h-7 w-7">
                    <Sparkles className="h-4 w-4 text-amber-500" />
                  </Button>
                </DropdownMenuTrigger>
                <DropdownMenuContent align="end" className="w-56">
                  <DropdownMenuItem 
                    onClick={() => setContextMode('general')}
                    className={contextMode === 'general' ? 'bg-gray-100' : ''}
                  >
                    一般的なアシスタント
                  </DropdownMenuItem>
                  <DropdownMenuItem 
                    onClick={() => setContextMode('page')}
                    className={contextMode === 'page' ? 'bg-gray-100' : ''}
                  >
                    現在のページをコンテキストに
                  </DropdownMenuItem>
                  <DropdownMenuItem 
                    onClick={() => setContextMode('employee')}
                    className={contextMode === 'employee' ? 'bg-gray-100' : ''}
                  >
                    従業員データのコンテキスト
                  </DropdownMenuItem>
                  <DropdownMenuItem onClick={() => clearMessages()}>
                    会話をリセット
                  </DropdownMenuItem>
                </DropdownMenuContent>
              </DropdownMenu>
            </>
          )}
          
          <Button 
            variant="ghost" 
            size="icon" 
            onClick={toggleExpand} 
            className="h-7 w-7"
          >
            {isExpanded ? <X className="h-4 w-4" /> : <ChevronUp className="h-4 w-4" />}
          </Button>
        </div>
      </div>
      
      {/* 拡張時のみ表示するコンテンツ */}
      {isExpanded && (
        <>
          {/* メッセージ表示エリア */}
          <ScrollArea 
            className="flex-1 p-3 h-[calc(100%-120px)]" 
            ref={scrollRef}
          >
            {messages.length === 0 ? (
              <div className="flex flex-col items-center justify-center h-full text-center text-gray-500 p-6">
                <Sparkles className="h-12 w-12 mb-2 text-blue-300" />
                <h3 className="text-lg font-medium">HRアシスタント</h3>
                <p className="text-sm mt-2">
                  人事データの分析、従業員に関する質問、採用プロセスの最適化など、
                  お気軽にお問い合わせください。
                </p>
              </div>
            ) : (
              <div className="space-y-4">
                <AnimatePresence>
                  {messages.map((message) => (
                    <motion.div
                      key={message.id}
                      initial={{ opacity: 0, y: 10 }}
                      animate={{ opacity: 1, y: 0 }}
                      className={cn(
                        "flex",
                        message.role === "user" ? "justify-end" : "justify-start"
                      )}
                    >
                      <div
                        className={cn(
                          "max-w-[80%] rounded-lg px-4 py-2",
                          message.role === "user"
                            ? "bg-blue-600 text-white"
                            : "bg-gray-100 text-gray-800"
                        )}
                      >
                        <div className="flex items-center space-x-2 mb-1">
                          {message.role === "assistant" ? (
                            <Bot className="h-3 w-3 text-blue-600" />
                          ) : (
                            <User className="h-3 w-3 text-white" />
                          )}
                          <p className="text-xs opacity-70">
                            {message.role === "assistant" ? "AIアシスタント" : "あなた"}
                          </p>
                          <p className="text-xs opacity-50">
                            {new Date(message.timestamp).toLocaleTimeString()}
                          </p>
                        </div>
                        <div className="prose prose-sm max-w-none">
                          {message.content}
                        </div>
                      </div>
                    </motion.div>
                  ))}
                </AnimatePresence>
                
                {isLoading && (
                  <motion.div 
                    initial={{ opacity: 0 }}
                    animate={{ opacity: 1 }}
                    className="flex justify-start"
                  >
                    <div className="bg-gray-100 rounded-lg px-3 py-2 text-gray-800 max-w-[80%]">
                      <div className="flex space-x-1">
                        <div className="h-2 w-2 bg-gray-400 rounded-full animate-bounce [animation-delay:-0.3s]"></div>
                        <div className="h-2 w-2 bg-gray-400 rounded-full animate-bounce [animation-delay:-0.15s]"></div>
                        <div className="h-2 w-2 bg-gray-400 rounded-full animate-bounce"></div>
                      </div>
                    </div>
                  </motion.div>
                )}
              </div>
            )}
          </ScrollArea>
          
          {/* AI提案チップ */}
          {aiChips.length > 0 && !isLoading && (
            <div className="px-3 py-2 border-t border-gray-100">
              <div className="flex flex-wrap gap-2">
                {aiChips.map((chip, index) => (
                  <Button
                    key={index}
                    variant="outline"
                    size="sm"
                    className="text-xs bg-blue-50 border-blue-100 text-blue-800 hover:bg-blue-100"
                    onClick={() => handleChipClick(chip)}
                  >
                    {chip}
                  </Button>
                ))}
              </div>
            </div>
          )}
          
          {/* 入力エリア */}
          <form
            onSubmit={handleSubmit}
            className="border-t p-3 bg-white flex items-end gap-2"
          >
            <Textarea
              value={inputValue}
              onChange={(e) => {
                setInputValue(e.target.value);
                setIsTyping(e.target.value.length > 0);
              }}
              placeholder="質問や指示を入力..."
              className="resize-none min-h-[60px] max-h-[120px] flex-1"
              onKeyDown={(e) => {
                if (e.key === "Enter" && !e.shiftKey) {
                  e.preventDefault();
                  handleSubmit(e);
                }
              }}
            />
            <Button
              type="submit"
              size="icon"
              className="h-10 w-10 shrink-0"
              disabled={!isTyping || isLoading}
            >
              <Send className={cn(
                "h-4 w-4",
                isLoading ? "animate-pulse" : ""
              )} />
            </Button>
          </form>
        </>
      )}
    </motion.div>
  );
}
```

## 8. テスト・品質保証計画

### 8.1 テスト戦略

#### 8.1.1 フロントエンドテスト

- **ユニットテスト**: コンポーネント単位での機能検証 (Jest/React Testing Library)
- **コンポーネントテスト**: 独立したUIコンポーネントの挙動検証 (Storybook/Chromatic)
- **統合テスト**: 複数コンポーネント連携の検証 (Cypress/Playwright)
- **エンドツーエンドテスト**: ユーザーフローの完全検証 (Playwright)

#### 8.1.2 バックエンドテスト

- **ユニットテスト**: 関数・メソッド単位のテスト (PyTest)
- **API統合テスト**: エンドポイント挙動検証 (SuperTest)
- **パフォーマンステスト**: 負荷テスト (k6/Locust)
- **セキュリティテスト**: 脆弱性スキャン (OWASP ZAP)

#### 8.1.3 AIモデルテスト

- **精度評価**: 予測モデルの精度検証
- **バイアステスト**: 公平性評価フレームワーク
- **ストレステスト**: エッジケース対応能力
- **A/Bテスト**: モデルバージョン比較フレームワーク

### 8.2 継続的インテグレーション/デリバリー

- GitHub Actions自動化ワークフロー
- テスト自動化パイプライン
- デプロイ前品質ゲート
- ブランチ保護ルールと承認フロー

### 8.3 品質基準

- コードカバレッジ目標: 80%以上
- パフォーマンス基準: LCP 2.5秒以下
- アクセシビリティ: WCAG 2.1 AAレベル準拠
- セキュリティ: OWASP Top 10対応

## 9. ローンチ後の展開計画

### 9.1 継続的改善サイクル

- ユーザーフィードバック収集システム
- A/Bテスト実施フレームワーク
- 使用データに基づく機能最適化
- 定期的な機能リリース計画

### 9.2 拡張ロードマップ

#### フェーズ1: 基盤強化 (リリース後1-3ヶ月)

- パフォーマンス最適化
- エンタープライズ統合拡充
- バグ修正とUX改善
- 初期ユーザーフィードバックに基づく調整

#### フェーズ2: 機能拡張 (4-6ヶ月)

- マルチモーダル分析（音声/動画）
- AIアシスタント高度化
- 予測モデルの精度向上
- カスタムレポート機能

#### フェーズ3: エコシステム拡大 (7-12ヶ月)

- サードパーティ統合API
- プラグイン開発フレームワーク
- 業界特化ソリューション
- グローバル多言語対応

### 9.3 ユーザーコミュニティ構築

- ユーザーグループ形成
- ナレッジベース拡充
- 定期的なウェビナー開催
- アーリーアクセスプログラム

## 10. プロジェクト成功指標

### 10.1 ビジネスKPI

- 顧客獲得コスト (CAC)
- 顧客生涯価値 (LTV)
- 月間定期収益 (MRR)
- ネットプロモータースコア (NPS)
- 解約率

### 10.2 プロダクトKPI

- デイリー/マンスリーアクティブユーザー
- 機能採用率
- セッション持続時間
- タスク完了率
- AI使用量と効果

### 10.3 エンジニアリングKPI

- デプロイ頻度
- 平均修復時間 (MTTR)
- バグ発生率
- 技術的負債指標
- API応答時間

### 11. 開発環境・コード品質管理

#### 11.1 開発環境セットアップ
- 開発環境構築手順（Docker開発環境）
- 必要な環境変数・シークレット管理
- ローカル開発サーバー起動手順
- データベースシード・マイグレーション手順

#### 11.2 コーディング規約
- ESLint/Prettier設定（フロントエンド）
- Python Linting設定（バックエンド）
- コミットメッセージ規約
- プルリクエストテンプレート

#### 11.3 ブランチ戦略・リリースフロー
- Git Flow実装方法
- 機能ブランチ命名規則
- マージ戦略とコードレビュー基準
- リリースタグ付け規約

### 12. 運用・非機能要件

#### 12.1 パフォーマンス要件
- 応答時間目標（95パーセンタイル）
- 同時接続ユーザー数対応
- キャッシュ戦略詳細
- モニタリングアラートしきい値

#### 12.2 可用性・障害対応計画
- 目標復旧時間（RTO）と目標復旧ポイント（RPO）
- 障害検知・エスカレーションフロー
- ログ収集・分析戦略
- バックアップ・リストア手順

#### 12.3 データ管理・プライバシー対応
- データ保持ポリシー
- 個人情報の匿名化・仮名化処理
- サブプロセッサー管理
- データポータビリティ実装

## 13. まとめ

次世代スーパー人事AIエージェント「HRX-AI」は、人事部門を戦略的パートナーへと変革する革新的ソリューションです。最先端AI技術と直感的UXを融合し、3ヶ月の集中開発期間で全機能を実装します。モジュラー設計と拡張可能なアーキテクチャにより、人事業務効率を最大70%向上させ、従業員エンゲージメントと定着率を15-25%改善させます。データドリブンな意思決定を実現し、企業の人材戦略を次のレベルへと高めることが可能になります。

このプロジェクトは、技術革新と人事管理の統合を通じて、企業の最も重要な資産である「人材」の管理方法に革命をもたらします。
