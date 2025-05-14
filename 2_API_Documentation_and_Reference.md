
# API ドキュメント・リファレンス

## HRX-AI API 仕様書

*バージョン: 1.0.0 | 最終更新日: 2025年月14日*

このドキュメントでは、「HRX-AI」のAPI仕様と利用方法について詳細に説明します。開発者はこのドキュメントを参照して、HRX-AIの各種機能へのプログラマチックなアクセス方法を理解してください。

---

## 目次

1. [はじめに](#1-はじめに)
2. [認証と認可](#2-認証と認可)
3. [API 概要](#3-api-概要)
4. [従業員 API](#4-従業員-api)
5. [分析 API](#5-分析-api)
6. [採用 API](#6-採用-api)
7. [組織 API](#7-組織-api)
8. [AI API](#8-ai-api)
9. [統合 API](#9-統合-api)
10. [Webhook](#10-webhook)
11. [エラー処理](#11-エラー処理)
12. [レート制限](#12-レート制限)
13. [バージョニング](#13-バージョニング)
14. [API 実装例](#14-api-実装例)

---

## 1. はじめに

### 1.1 API ベースURL

すべてのAPIリクエストは以下のベースURLから始まります

```
本番環境: https://api.hrx-ai.com/v1
ステージング環境: https://api-staging.hrx-ai.com/v1
開発環境: https://api-dev.hrx-ai.com/v1
```

### 1.2 リクエストフォーマット

APIリクエストは主にJSON形式で行われます。適切なヘッダーを設定してください

```
Content-Type: application/json
Accept: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### 1.3 レスポンスフォーマット

レスポンスはJSON形式で返されます。基本構造は以下の通りです

```json
{
  "success": true,
  "data": { /* レスポンスデータ */ },
  "meta": {
    "pagination": {
      "page": 1,
      "per_page": 10,
      "total": 100,
      "total_pages": 10
    }
  }
}
```

エラーの場合：

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "エラーメッセージ",
    "details": { /* 追加情報 */ }
  }
}
```

## 2. 認証と認可

### 2.1 アクセストークンの取得

APIを利用するには、JWTアクセストークンが必要です。以下のエンドポイントで取得できます

#### リクエスト

```http
POST /auth/token
Content-Type: application/json

{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "grant_type": "client_credentials",
  "scope": "read:employees write:analytics"
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5c...",
    "token_type": "Bearer",
    "expires_in": 3600,
    "scope": "read:employees write:analytics"
  }
}
```

### 2.2 権限スコープ

APIリソースへのアクセスは、以下のスコープによって制御されます

| スコープ | 説明 |
|----------|------|
| `read:employees` | 従業員情報の読み取り |
| `write:employees` | 従業員情報の作成・更新 |
| `read:analytics` | 分析データの読み取り |
| `write:analytics` | 分析データの作成・更新 |
| `read:recruitment` | 採用情報の読み取り |
| `write:recruitment` | 採用情報の作成・更新 |
| `read:organization` | 組織情報の読み取り |
| `write:organization` | 組織情報の作成・更新 |
| `read:ai` | AI機能の利用 |
| `admin` | すべての管理者権限 |

### 2.3 役割ベースアクセス制御

アクセス制御は以下の役割に基づいて行われます

| 役割 | 説明 | デフォルトスコープ |
|------|------|-------------------|
| `viewer` | 閲覧のみ可能 | `read:*` |
| `hr_specialist` | 人事スペシャリスト | `read:*`, `write:employees`, `write:recruitment` |
| `hr_manager` | 人事マネージャー | `read:*`, `write:*` (AI除く) |
| `admin` | システム管理者 | すべてのスコープ |

## 3. API 概要

HRX-AI APIは機能領域に基づいて以下のカテゴリに分類されています

| API カテゴリ | ベースパス | 説明 |
|------------|-----------|------|
| 従業員 API | `/employees` | 従業員データの管理と操作 |
| 分析 API | `/analytics` | 人事データの高度な分析と可視化 |
| 採用 API | `/recruitment` | 採用プロセス管理と候補者評価 |
| 組織 API | `/organization` | 組織構造と部門管理 |
| AI API | `/ai` | AIアシスタントと予測機能 |
| 統合 API | `/integrations` | 外部システム連携 |

## 4. 従業員 API

従業員情報の作成、取得、更新、削除を行うAPIです。

### 4.1 従業員一覧の取得

#### リクエスト

```http
GET /employees
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ：**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `page` | integer | ページ番号 | 1 |
| `per_page` | integer | 1ページあたりのアイテム数 | 10 |
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `employment_status` | string | 雇用状態でフィルタリング | なし |
| `search` | string | 名前・メールでの検索クエリ | なし |
| `sort_by` | string | ソートフィールド（name, hire_date, etc） | name |
| `sort_order` | string | ソート順序 (asc/desc) | asc |

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "e12d45f6-7890-abcd-ef12-3456789abcde",
      "first_name": "太郎",
      "last_name": "山田",
      "email": "taro.yamada@example.com",
      "job_title": "シニアエンジニア",
      "department": {
        "id": "d12345-67890",
        "name": "開発部門"
      },
      "hire_date": "2020-04-01",
      "employment_status": "active",
      "manager_id": "e98765-43210",
      "retention_risk": 0.15,
      "performance_score": 4.2,
      "created_at": "2020-03-15T09:30:00Z",
      "updated_at": "2023-05-20T14:25:00Z"
    },
    // 他の従業員データ...
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "per_page": 10,
      "total": 56,
      "total_pages": 6
    }
  }
}
```

### 4.2 従業員詳細の取得

#### リクエスト

```http
GET /employees/{employee_id}
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "e12d45f6-7890-abcd-ef12-3456789abcde",
    "first_name": "太郎",
    "last_name": "山田",
    "email": "taro.yamada@example.com",
    "phone": "+81901234567",
    "birth_date": "1985-07-15",
    "gender": "male",
    "nationality": "日本",
    "address": {
      "postal_code": "100-0001",
      "prefecture": "東京都",
      "city": "千代田区",
      "street_address": "大手町1-1-1"
    },
    "job_title": "シニアエンジニア",
    "department": {
      "id": "d12345-67890",
      "name": "開発部門"
    },
    "hire_date": "2020-04-01",
    "employment_status": "active",
    "employment_type": "full_time",
    "manager": {
      "id": "e98765-43210",
      "first_name": "花子",
      "last_name": "鈴木",
      "job_title": "開発マネージャー"
    },
    "compensation": {
      "base_salary": 6500000,
      "currency": "JPY",
      "last_review_date": "2023-04-01"
    },
    "performance": {
      "current_score": 4.2,
      "previous_score": 4.0,
      "trend": "increasing"
    },
    "skills": [
      {
        "name": "Reactプログラミング",
        "level": 4,
        "endorsed_by": 5,
        "last_used": "2023-05-15"
      },
      {
        "name": "プロジェクト管理",
        "level": 3,
        "endorsed_by": 3,
        "last_used": "2023-04-10"
      }
    ],
    "retention_data": {
      "risk_score": 0.15,
      "engagement_level": "高",
      "last_promotion_date": "2022-04-01",
      "time_in_position": 24
    },
    "attachments": [
      {
        "id": "a12345-67890",
        "name": "職務経歴書_2023.pdf",
        "type": "resume",
        "upload_date": "2023-01-15T10:30:00Z",
        "url": "/employees/e12d45f6-7890/attachments/a12345-67890"
      }
    ],
    "created_at": "2020-03-15T09:30:00Z",
    "updated_at": "2023-05-20T14:25:00Z",
    "created_by": "admin_user_id",
    "updated_by": "hr_manager_id"
  }
}
```

### 4.3 従業員の新規作成

#### リクエスト

```http
POST /employees
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "first_name": "一郎",
  "last_name": "佐藤",
  "email": "ichiro.sato@example.com",
  "phone": "+81901234567",
  "birth_date": "1990-05-20",
  "gender": "male",
  "nationality": "日本",
  "address": {
    "postal_code": "100-0001",
    "prefecture": "東京都",
    "city": "千代田区",
    "street_address": "丸の内1-1-1"
  },
  "job_title": "マーケティングスペシャリスト",
  "department_id": "d12345-67890",
  "hire_date": "2023-07-01",
  "employment_status": "active",
  "employment_type": "full_time",
  "manager_id": "e98765-43210",
  "compensation": {
    "base_salary": 5500000,
    "currency": "JPY"
  }
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "e23e56f7-8901-bcde-fg23-4567890bcdef",
    "first_name": "一郎",
    "last_name": "佐藤",
    "email": "ichiro.sato@example.com",
    "job_title": "マーケティングスペシャリスト",
    "department": {
      "id": "d12345-67890",
      "name": "マーケティング部門"
    },
    "hire_date": "2023-07-01",
    "employment_status": "active",
    "manager_id": "e98765-43210",
    "created_at": "2023-06-15T10:30:00Z",
    "updated_at": "2023-06-15T10:30:00Z"
  }
}
```

### 4.4 従業員情報の更新

#### リクエスト

```http
PATCH /employees/{employee_id}
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "job_title": "シニアマーケティングスペシャリスト",
  "department_id": "d12345-67890",
  "manager_id": "e98765-43210",
  "compensation": {
    "base_salary": 6000000,
    "currency": "JPY",
    "last_review_date": "2023-07-01"
  }
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "e23e56f7-8901-bcde-fg23-4567890bcdef",
    "first_name": "一郎",
    "last_name": "佐藤",
    "email": "ichiro.sato@example.com",
    "job_title": "シニアマーケティングスペシャリスト",
    "department": {
      "id": "d12345-67890",
      "name": "マーケティング部門"
    },
    "hire_date": "2023-07-01",
    "employment_status": "active",
    "manager_id": "e98765-43210",
    "updated_at": "2023-07-01T09:15:00Z"
  }
}
```

### 4.5 従業員のパフォーマンス履歴

#### リクエスト

```http
GET /employees/{employee_id}/performance
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "employee_id": "e12d45f6-7890-abcd-ef12-3456789abcde",
    "current_score": 4.2,
    "records": [
      {
        "id": "p12345-67890",
        "evaluation_date": "2023-04-01",
        "overall_score": 4.2,
        "competency_scores": {
          "technical_skills": 4.5,
          "communication": 4.0,
          "teamwork": 4.3,
          "problem_solving": 4.1,
          "leadership": 3.8
        },
        "feedback": "目標を超える成果を達成し、技術的なリーダーシップを発揮しています。...",
        "evaluator": {
          "id": "e98765-43210",
          "name": "鈴木花子"
        }
      },
      {
        "id": "p23456-78901",
        "evaluation_date": "2022-10-01",
        "overall_score": 4.0,
        "competency_scores": {
          "technical_skills": 4.3,
          "communication": 3.8,
          "teamwork": 4.0,
          "problem_solving": 4.0,
          "leadership": 3.6
        },
        "feedback": "安定したパフォーマンスを維持しています。コミュニケーションスキルの向上が見られました。...",
        "evaluator": {
          "id": "e98765-43210",
          "name": "鈴木花子"
        }
      }
    ],
    "trend": {
      "direction": "increasing",
      "percentage_change": 5.0
    },
    "recommendations": [
      {
        "focus_area": "leadership",
        "suggestion": "チーム内でのメンタリング機会を増やすことで、リーダーシップスキルを強化できます。"
      }
    ]
  }
}
```

### 4.6 従業員のスキルプロファイル

#### リクエスト

```http
GET /employees/{employee_id}/skills
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "employee_id": "e12d45f6-7890-abcd-ef12-3456789abcde",
    "skills": [
      {
        "name": "Reactプログラミング",
        "category": "technical",
        "level": 4,
        "proficiency_description": "上級者：複雑なアプリケーションの設計・実装が可能",
        "endorsed_by": [
          {
            "user_id": "e34567-89012",
            "name": "田中次郎",
            "date": "2023-03-15"
          }
        ],
        "last_used": "2023-05-15",
        "certifications": [
          {
            "name": "React Developer Certification",
            "issuer": "React Training Inc.",
            "issue_date": "2022-05-10",
            "expiry_date": "2024-05-10"
          }
        ]
      },
      // 他のスキル...
    ],
    "skill_gaps": [
      {
        "skill": "TypeScript",
        "current_level": 2,
        "required_level": 3,
        "requirement_source": "現在の役職",
        "learning_resources": [
          {
            "title": "TypeScript マスターコース",
            "url": "https://learning.hrx-ai.com/courses/typescript-advanced",
            "estimated_duration": "15時間"
          }
        ]
      }
    ],
    "recommended_development": [
      {
        "skill": "プロジェクト管理",
        "reason": "キャリアパスに基づく推奨",
        "target_level": 4,
        "resources": [
          {
            "title": "アジャイルプロジェクト管理基礎",
            "url": "https://learning.hrx-ai.com/courses/agile-pm-basics",
            "estimated_duration": "10時間"
          }
        ]
      }
    ]
  }
}
```

## 5. 分析 API

組織と人材に関する高度な分析機能を提供するAPIです。

### 5.1 離職リスク分析

#### リクエスト

```http
GET /analytics/retention-risk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ：**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `risk_threshold` | float | リスクしきい値 (0.0-1.0) | 0.6 |
| `time_period` | string | 分析期間 (30d, 90d, 180d, 1y) | 90d |

#### レスポンス

```json
{
  "success": true,
  "data": {
    "summary": {
      "total_employees": 156,
      "high_risk_count": 12,
      "medium_risk_count": 24,
      "low_risk_count": 120,
      "average_risk_score": 0.28
    },
    "risk_distribution": [
      {
        "risk_range": "0.0-0.2",
        "count": 98,
        "percentage": 62.8
      },
      {
        "risk_range": "0.2-0.4",
        "count": 35,
        "percentage": 22.4
      },
      {
        "risk_range": "0.4-0.6",
        "count": 11,
        "percentage": 7.1
      },
      {
        "risk_range": "0.6-0.8",
        "count": 8,
        "percentage": 5.1
      },
      {
        "risk_range": "0.8-1.0",
        "count": 4,
        "percentage": 2.6
      }
    ],
    "high_risk_employees": [
      {
        "id": "e12d45f6-7890-abcd-ef12-3456789abcde",
        "name": "山田太郎",
        "department": "開発部門",
        "risk_score": 0.85,
        "key_factors": [
          {
            "factor": "salary_competitiveness",
            "contribution": 0.4,
            "description": "市場平均より15%低い給与水準"
          },
          {
            "factor": "work_life_balance",
            "contribution": 0.3,
            "description": "過去3ヶ月間の残業時間が20%増加"
          }
        ],
        "recommended_actions": [
          {
            "action": "compensation_review",
            "priority": "high",
            "impact_estimate": 0.25
          }
        ]
      }
    ],
    "trend": {
      "period_comparison": [
        {
          "period": "current_quarter",
          "average_risk": 0.28,
          "high_risk_percentage": 7.7
        },
        {
          "period": "previous_quarter",
          "average_risk": 0.25,
          "high_risk_percentage": 6.5
        }
      ],
      "direction": "increasing",
      "percentage_change": 12.0
    },
    "contributing_factors": [
      {
        "factor": "compensation",
        "impact_weight": 0.35,
        "departments_affected": ["開発", "セールス"]
      },
      {
        "factor": "work_life_balance",
        "impact_weight": 0.25,
        "departments_affected": ["開発", "カスタマーサポート"]
      }
    ],
    "recommendations": [
      {
        "action": "compensation_review",
        "description": "市場競争力のある給与水準への調整を検討",
        "affected_employees": 15,
        "estimated_impact": "高",
        "priority": 1
      }
    ]
  },
  "meta": {
    "analysis_date": "2023-07-01T00:00:00Z",
    "model_version": "3.2.1",
    "confidence_level": "high"
  }
}
```

### 5.2 部門パフォーマンス分析

#### リクエスト

```http
GET /analytics/department-performance
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `time_period` | string | 分析期間 (30d, 90d, 180d, 1y) | 90d |
| `metrics` | string[] | 分析する指標（カンマ区切り） | all |

#### レスポンス

```json
{
  "success": true,
  "data": {
    "department_id": "d12345-67890",
    "department_name": "開発部門",
    "time_period": "90d",
    "summary": {
      "headcount": 42,
      "headcount_change": 2,
      "average_performance": 3.8,
      "performance_trend": "stable",
      "turnover_rate": 0.05,
      "engagement_score": 4.2
    },
    "performance_distribution": [
      {
        "score_range": "1.0-2.0",
        "count": 1,
        "percentage": 2.4
      },
      {
        "score_range": "2.1-3.0",
        "count": 8,
        "percentage": 19.0
      },
      {
        "score_range": "3.1-4.0",
        "count": 22,
        "percentage": 52.4
      },
      {
        "score_range": "4.1-5.0",
        "count": 11,
        "percentage": 26.2
      }
    ],
    "skills_assessment": {
      "coverage": 0.85,
      "gap_areas": [
        {
          "skill": "クラウドインフラ管理",
          "current_coverage": 0.65,
          "required_coverage": 0.8
        }
      ],
      "strengths": [
        {
          "skill": "Reactプログラミング",
          "coverage": 0.9
        }
      ]
    },
    "productivity_metrics": {
      "projects_on_time": 0.85,
      "code_quality_score": 4.2,
      "sprint_completion_rate": 0.92
    },
    "recommendations": [
      {
        "area": "skill_development",
        "description": "クラウドインフラ管理のトレーニングプログラムを実施",
        "priority": "medium",
        "expected_impact": "業務効率10%向上、プロジェクト遅延20%削減"
      }
    ],
    "benchmarks": {
      "internal": {
        "performance": {
          "value": 3.8,
          "company_average": 3.6,
          "percentile": 65
        },
        "engagement": {
          "value": 4.2,
          "company_average": 3.9,
          "percentile": 75
        }
      },
      "industry": {
        "turnover": {
          "value": 0.05,
          "industry_average": 0.08,
          "percentile": 70
        }
      }
    }
  },
  "meta": {
    "analysis_date": "2023-07-01T00:00:00Z"
  }
}
```

### 5.3 人材獲得分析

#### リクエスト

```http
GET /analytics/recruitment
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `time_period` | string | 分析期間 (30d, 90d, 180d, 1y) | 90d |
| `position_types` | string[] | 職種タイプによるフィルタ（カンマ区切り） | all |

#### レスポンス

```json
{
  "success": true,
  "data": {
    "summary": {
      "total_positions": 15,
      "filled_positions": 12,
      "open_positions": 3,
      "average_time_to_fill": 35.2,
      "average_cost_per_hire": 450000
    },
    "recruitment_funnel": {
      "applications_received": 320,
      "initial_screenings": 120,
      "interviews_conducted": 45,
      "offers_extended": 15,
      "offers_accepted": 12,
      "conversion_rates": {
        "application_to_screening": 0.375,
        "screening_to_interview": 0.375,
        "interview_to_offer": 0.333,
        "offer_to_acceptance": 0.8
      }
    },
    "channel_effectiveness": [
      {
        "channel": "company_website",
        "applications": 85,
        "hires": 3,
        "quality_score": 4.2,
        "cost_per_hire": 320000
      },
      {
        "channel": "employee_referral",
        "applications": 25,
        "hires": 4,
        "quality_score": 4.6,
        "cost_per_hire": 120000
      },
      {
        "channel": "job_board",
        "applications": 180,
        "hires": 3,
        "quality_score": 3.8,
        "cost_per_hire": 500000
      },
      {
        "channel": "linkedin",
        "applications": 30,
        "hires": 2,
        "quality_score": 4.0,
        "cost_per_hire": 400000
      }
    ],
    "time_to_fill_analysis": {
      "overall_average": 35.2,
      "by_department": [
        {
          "department": "開発",
          "average_days": 42.3
        },
        {
          "department": "セールス",
          "average_days": 28.5
        }
      ],
      "by_position_level": [
        {
          "level": "entry",
          "average_days": 25.6
        },
        {
          "level": "mid",
          "average_days": 35.8
        },
        {
          "level": "senior",
          "average_days": 52.3
        }
      ],
      "bottlenecks": [
        {
          "stage": "technical_interview",
          "average_duration": 12.5,
          "recommendation": "面接官の増員とスケジュール最適化"
        }
      ]
    },
    "candidate_quality_metrics": {
      "average_performance_score": 3.9,
      "retention_rate_6months": 0.95,
      "retention_rate_1year": 0.85,
      "by_source": [
        {
          "source": "employee_referral",
          "performance_score": 4.3,
          "retention_1year": 0.92
        }
      ]
    },
    "market_competitiveness": {
      "offer_acceptance_rate": 0.8,
      "decline_reasons": [
        {
          "reason": "compensation",
          "percentage": 45
        },
        {
          "reason": "competing_offer",
          "percentage": 30
        }
      ],
      "salary_competitiveness": {
        "percentage_to_market": -5,
        "recommendation": "主要ポジションの給与帯10%引き上げを検討"
      }
    },
    "diversity_metrics": {
      "gender_distribution": {
        "applicants": {
          "male": 0.65,
          "female": 0.34,
          "other": 0.01
        },
        "hires": {
          "male": 0.58,
          "female": 0.42,
          "other": 0.0
        }
      },
      "recommendations": [
        {
          "area": "sourcing_channels",
          "description": "多様性を高めるための採用チャネルの拡大"
        }
      ]
    }
  },
  "meta": {
    "analysis_date": "2023-07-01T00:00:00Z"
  }
}
```

### 5.4 給与・報酬分析

#### リクエスト

```http
GET /analytics/compensation
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `job_level` | string | 役職レベルによるフィルタリング | なし |

#### レスポンス

```json
{
  "success": true,
  "data": {
    "market_benchmarks": {
      "overall_competitiveness": {
        "ratio": 0.95,
        "description": "市場平均の95%",
        "recommendation": "主要ポジションの競争力強化が必要"
      },
      "by_job_level": [
        {
          "level": "entry",
          "market_median": 3500000,
          "company_median": 3450000,
          "ratio": 0.99
        },
        {
          "level": "mid",
          "market_median": 5500000,
          "company_median": 5200000,
          "ratio": 0.95
        },
        {
          "level": "senior",
          "market_median": 8500000,
          "company_median": 7800000,
          "ratio": 0.92
        }
      ],
      "by_department": [
        {
          "department": "開発",
          "market_median": 6000000,
          "company_median": 5700000,
          "ratio": 0.95
        }
      ]
    },
    "internal_equity_analysis": {
      "compa_ratio_distribution": {
        "below_80": 5,
        "80_90": 12,
        "90_100": 43,
        "100_110": 35,
        "110_120": 15,
        "above_120": 3
      },
      "gender_equity": {
        "overall_gap": 4.5,
        "controlled_gap": 2.1,
        "significant_factors": [
          "job_level",
          "tenure"
        ],
        "recommendations": [
          {
            "description": "中堅レベルでの昇給・昇進プロセスの見直し",
            "impact": "gap 1.5%減少"
          }
        ]
      },
      "anomalies": [
        {
          "count": 8,
          "description": "同じ職務グレードで20%以上の給与差があるケース",
          "recommendation": "個別レビューと調整"
        }
      ]
    },
    "compensation_structure": {
      "pay_grades": [
        {
          "grade": "G1",
          "min": 3000000,
          "mid": 3750000,
          "max": 4500000,
          "employee_distribution": [
            {
              "segment": "min-mid",
              "count": 12
            },
            {
              "segment": "mid-max",
              "count": 8
            }
          ]
        }
      ],
      "recommendations": [
        {
          "area": "grade_boundaries",
          "description": "G3-G4の給与帯10%拡大を検討"
        }
      ]
    },
    "performance_correlation": {
      "overall_r_squared": 0.65,
      "issues": [
        {
          "description": "高パフォーマンス・低報酬 社員が15名",
          "risk_level": "high",
          "recommendation": "即時の給与レビュー"
        }
      ],
      "opportunities": [
        {
          "description": "業績連動型インセンティブ制度の拡大",
          "expected_impact": "パフォーマンスと報酬の相関を10%向上"
        }
      ]
    },
    "budget_planning": {
      "recommended_increase": 4.5,
      "recommended_allocation": [
        {
          "category": "merit_increase",
          "percentage": 3.0,
          "budget_impact": 45000000
        },
        {
          "category": "market_adjustment",
          "percentage": 1.0,
          "budget_impact": 15000000
        },
        {
          "category": "equity_adjustment",
          "percentage": 0.5,
          "budget_impact": 7500000
        }
      ],
      "roi_projection": {
        "expected_turnover_reduction": 2.5,
        "expected_engagement_increase": 5.0,
        "financial_impact": 75000000
      }
    }
  },
  "meta": {
    "analysis_date": "2023-07-01T00:00:00Z",
    "market_data_source": "HR-Benchmark Japan 2023",
    "confidentiality": "restricted"
  }
}
```

### 5.5 エンゲージメント分析

#### リクエスト

```http
GET /analytics/engagement
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `time_period` | string | 分析期間 (30d, 90d, 180d, 1y) | 90d |

#### レスポンス

```json
{
  "success": true,
  "data": {
    "summary": {
      "overall_score": 3.9,
      "previous_period": 3.7,
      "trend": "increasing",
      "percentage_change": 5.4,
      "participation_rate": 0.85
    },
    "dimension_scores": [
      {
        "dimension": "job_satisfaction",
        "score": 4.1,
        "previous_score": 3.8,
        "trend": "increasing"
      },
      {
        "dimension": "work_life_balance",
        "score": 3.5,
        "previous_score": 3.3,
        "trend": "increasing"
      },
      {
        "dimension": "leadership_trust",
        "score": 3.8,
        "previous_score": 3.7,
        "trend": "stable"
      },
      {
        "dimension": "professional_growth",
        "score": 3.6,
        "previous_score": 3.4,
        "trend": "increasing"
      },
      {
        "dimension": "team_collaboration",
        "score": 4.2,
        "previous_score": 4.1,
        "trend": "stable"
      }
    ],
    "department_breakdown": [
      {
        "department": "開発",
        "overall_score": 3.7,
        "previous_score": 3.5,
        "trend": "increasing",
        "top_strength": "team_collaboration",
        "top_concern": "work_life_balance"
      },
      {
        "department": "セールス",
        "overall_score": 4.1,
        "previous_score": 3.9,
        "trend": "increasing",
        "top_strength": "professional_growth",
        "top_concern": "work_life_balance"
      }
    ],
    "demographic_insights": {
      "tenure": [
        {
          "group": "0-1年",
          "score": 4.2,
          "previous": 4.0,
          "key_strength": "team_collaboration",
          "key_concern": "professional_growth"
        },
        {
          "group": "1-3年",
          "score": 3.8,
          "previous": 3.6,
          "key_strength": "job_satisfaction",
          "key_concern": "leadership_trust"
        },
        {
          "group": "3-5年",
          "score": 3.7,
          "previous": 3.6,
          "key_strength": "team_collaboration",
          "key_concern": "work_life_balance"
        },
        {
          "group": "5年以上",
          "score": 4.0,
          "previous": 3.8,
          "key_strength": "leadership_trust",
          "key_concern": "work_life_balance"
        }
      ]
    },
    "sentiment_analysis": {
      "positive_themes": [
        {
          "theme": "チーム協力",
          "mention_count": 87,
          "sentiment_score": 0.85,
          "sample_quotes": [
            "チームメンバー同士のサポートが素晴らしい"
          ]
        },
        {
          "theme": "仕事の意義",
          "mention_count": 65,
          "sentiment_score": 0.82,
          "sample_quotes": [
            "自分の仕事が会社の成長に貢献していると感じる"
          ]
        }
      ],
      "negative_themes": [
        {
          "theme": "ワークロード",
          "mention_count": 52,
          "sentiment_score": -0.65,
          "sample_quotes": [
            "常に締め切りに追われている感じがする"
          ]
        },
        {
          "theme": "キャリア成長",
          "mention_count": 38,
          "sentiment_score": -0.45,
          "sample_quotes": [
            "昇進の基準がはっきりしていない"
          ]
        }
      ]
    },
    "driver_analysis": {
      "key_drivers": [
        {
          "driver": "マネージャーのサポート",
          "impact_score": 0.75,
          "correlation": "strong_positive"
        },
        {
          "driver": "ワークライフバランス",
          "impact_score": 0.68,
          "correlation": "strong_positive"
        },
        {
          "driver": "キャリア開発機会",
          "impact_score": 0.62,
          "correlation": "moderate_positive"
        }
      ]
    },
    "pulse_survey_results": [
      {
        "date": "2023-06-15",
        "participation": 0.72,
        "average_mood": 3.8,
        "top_concern": "プロジェクト納期のプレッシャー"
      }
    ],
    "action_recommendations": [
      {
        "priority": "high",
        "action": "開発部門のワークロードバランス改善",
        "details": "プロジェクト間のリソース配分の最適化とバッファの追加",
        "expected_impact": "エンゲージメントスコア0.3ポイント向上",
        "target_groups": ["開発部門", "3-5年勤続社員"]
      },
      {
        "priority": "medium",
        "action": "キャリアパスの明確化",
        "details": "各職種・レベルごとの昇進基準と開発計画の明文化",
        "expected_impact": "エンゲージメントスコア0.2ポイント向上、離職リスク10%低減",
        "target_groups": ["全社員", "1-3年勤続社員"]
      }
    ]
  },
  "meta": {
    "analysis_date": "2023-07-01T00:00:00Z",
    "data_sources": ["定期エンゲージメント調査", "パルスサーベイ", "1on1フィードバック"],
    "next_scheduled_survey": "2023-10-01"
  }
}
```

## 6. 採用 API

採用プロセス管理と候補者評価を行うAPIです。

### 6.1 求人一覧取得

#### リクエスト

```http
GET /recruitment/job-postings
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `department_id` | string | 部門IDによるフィルタリング | なし |
| `status` | string | ステータスでフィルタリング (open, closed, draft) | なし |
| `page` | integer | ページ番号 | 1 |
| `per_page` | integer | 1ページあたりのアイテム数 | 10 |

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "j12345-67890",
      "title": "シニアフロントエンドエンジニア",
      "department_id": "d12345-67890",
      "department_name": "開発部門",
      "location": {
        "type": "office",
        "city": "東京",
        "country": "日本",
        "remote_allowed": true
      },
      "employment_type": "full_time",
      "experience_level": "senior",
      "salary_range": {
        "min": 6000000,
        "max": 9000000,
        "currency": "JPY",
        "is_visible": true
      },
      "status": "open",
      "posting_date": "2023-05-15",
      "closing_date": "2023-08-15",
      "applicant_count": 24,
      "shortlisted_count": 5,
      "interview_count": 3,
      "offer_count": 0,
      "created_at": "2023-05-10T10:15:00Z",
      "updated_at": "2023-06-20T14:30:00Z"
    }
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "per_page": 10,
      "total": 8,
      "total_pages": 1
    }
  }
}
```

### 6.2 求人詳細取得

#### リクエスト

```http
GET /recruitment/job-postings/{job_id}
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "j12345-67890",
    "title": "シニアフロントエンドエンジニア",
    "department_id": "d12345-67890",
    "department_name": "開発部門",
    "manager": {
      "id": "e98765-43210",
      "name": "鈴木花子"
    },
    "description": "当社の製品開発チームで、最新のWebアプリケーション開発を主導するシニアフロントエンドエンジニアを募集しています...",
    "responsibilities": [
      "モダンなフロントエンドアーキテクチャの設計と実装",
      "ジュニアエンジニアのメンタリングとコードレビュー",
      "プロダクトマネージャーと協力して新機能の要件定義と実装"
    ],
    "requirements": {
      "must_have": [
        "React、TypeScriptでの開発経験5年以上",
        "大規模Webアプリケーション開発経験",
        "CI/CDパイプラインの構築経験"
      ],
      "nice_to_have": [
        "Nextjs経験",
        "GraphQL経験",
        "モバイルアプリ開発経験"
      ]
    },
    "skills": [
      {
        "name": "React",
        "level": "expert",
        "importance": "critical"
      },
      {
        "name": "TypeScript",
        "level": "advanced",
        "importance": "critical"
      },
      {
        "name": "Testing",
        "level": "intermediate",
        "importance": "important"
      }
    ],
    "location": {
      "type": "office",
      "city": "東京",
      "country": "日本",
      "remote_allowed": true,
      "remote_restrictions": "日本国内のみ"
    },
    "employment_type": "full_time",
    "experience_level": "senior",
    "education": {
      "minimum": "bachelor",
      "preferred": "computer_science_or_related"
    },
    "salary_range": {
      "min": 6000000,
      "max": 9000000,
      "currency": "JPY",
      "is_visible": true,
      "benefits_summary": "社会保険完備、フレックスタイム制、リモートワーク可、教育手当"
    },
    "hiring_process": [
      {
        "stage": "application_review",
        "description": "書類選考",
        "estimated_time": "1週間以内"
      },
      {
        "stage": "first_interview",
        "description": "人事担当者との一次面接",
        "estimated_time": "30分"
      },
      {
        "stage": "technical_assessment",
        "description": "技術課題とコードレビュー",
        "estimated_time": "所要時間の目安：4時間"
      },
      {
        "stage": "final_interview",
        "description": "技術責任者および部門マネージャーとの最終面接",
        "estimated_time": "1時間"
      }
    ],
    "status": "open",
    "posting_date": "2023-05-15",
    "closing_date": "2023-08-15",
    "visibility": {
      "internal": true,
      "external": true,
      "job_boards": ["LinkedIn", "Indeed", "マイナビ"]
    },
    "application_statistics": {
      "views": 450,
      "applications": 24,
      "application_rate": 0.053,
      "qualified_candidates": 12,
      "shortlisted": 5,
      "interviews": 3,
      "offers": 0,
      "filled": false
    },
    "ai_optimizations": {
      "language_suggestions": [
        {
          "original": "優秀な人材を求めています",
          "suggested": "創造性を発揮し、チームに貢献いただける方を探しています",
          "reason": "より包括的で具体的な表現",
          "applied": true
        }
      ],
      "bias_analysis": {
        "gender_bias_score": 0.2,
        "age_bias_score": 0.1,
        "overall_inclusivity_score": 4.5,
        "improvements_applied": true
      },
      "effectiveness_prediction": {
        "expected_qualified_applicants": 30,
        "expected_time_to_fill": 45,
        "confidence": "high"
      }
    },
    "created_at": "2023-05-10T10:15:00Z",
    "created_by": "u12345-67890",
    "updated_at": "2023-06-20T14:30:00Z",
    "updated_by": "u23456-78901"
  }
}
```

### 6.3 候補者一覧取得

#### リクエスト

```http
GET /recruitment/job-postings/{job_id}/candidates
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `status` | string | ステータスでフィルタリング | なし |
| `sort_by` | string | ソートフィールド（match_score, application_date） | match_score |
| `sort_order` | string | ソート順序 (asc/desc) | desc |
| `page` | integer | ページ番号 | 1 |
| `per_page` | integer | 1ページあたりのアイテム数 | 10 |

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "c12345-67890",
      "job_posting_id": "j12345-67890",
      "first_name": "政夫",
      "last_name": "田中",
      "email": "masao.tanaka@example.com",
      "phone": "+81901234567",
      "current_company": "テックカンパニー株式会社",
      "current_position": "フロントエンドエンジニア",
      "location": "東京都新宿区",
      "status": "shortlisted",
      "match_score": 0.85,
      "application_date": "2023-05-20T10:30:00Z",
      "resume_url": "/secure/resumes/c12345-67890.pdf",
      "last_interaction": "2023-06-15T15:00:00Z",
      "next_steps": "技術面接を設定",
      "notes_count": 3,
      "created_at": "2023-05-20T10:30:00Z",
      "updated_at": "2023-06-15T15:00:00Z"
    }
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "per_page": 10,
      "total": 24,
      "total_pages": 3
    }
  }
}
```

### 6.4 候補者詳細取得

#### リクエスト

```http
GET /recruitment/candidates/{candidate_id}
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "c12345-67890",
    "job_posting_id": "j12345-67890",
    "job_title": "シニアフロントエンドエンジニア",
    "first_name": "政夫",
    "last_name": "田中",
    "email": "masao.tanaka@example.com",
    "phone": "+81901234567",
    "current_company": "テックカンパニー株式会社",
    "current_position": "フロントエンドエンジニア",
    "location": "東京都新宿区",
    "status": "shortlisted",
    "match_score": 0.85,
    "application_date": "2023-05-20T10:30:00Z",
    "resume_url": "/secure/resumes/c12345-67890.pdf",
    "portfolio_url": "https://tanaka-masao.portfolio.dev",
    "linkedin_url": "https://www.linkedin.com/in/masao-tanaka",
    "github_url": "https://github.com/tanaka-m",
    "education": [
      {
        "institution": "東京工科大学",
        "degree": "情報工学学士",
        "field": "コンピュータサイエンス",
        "start_date": "2012-04",
        "end_date": "2016-03"
      }
    ],
    "experience": [
      {
        "company": "テックカンパニー株式会社",
        "title": "フロントエンドエンジニア",
        "location": "東京",
        "start_date": "2020-04",
        "end_date": null,
        "is_current": true,
        "description": "モダンなWebアプリケーションの開発を担当。React、TypeScriptを使用した大規模SPA開発。"
      },
      {
        "company": "スタートアップ株式会社",
        "title": "Webデベロッパー",
        "location": "東京",
        "start_date": "2016-04",
        "end_date": "2020-03",
        "is_current": false,
        "description": "フルスタックエンジニアとして、複数のWebサービス開発に従事。"
      }
    ],
    "skills": [
      {
        "name": "React",
        "years_experience": 5,
        "level": "expert",
        "match_to_job": "critical_match"
      },
      {
        "name": "TypeScript",
        "years_experience": 4,
        "level": "advanced",
        "match_to_job": "critical_match"
      },
      {
        "name": "Next.js",
        "years_experience": 2,
        "level": "intermediate",
        "match_to_job": "nice_to_have_match"
      }
    ],
    "ai_insights": {
      "overall_summary": "React/TypeScriptに強みを持つ経験豊富なフロントエンドエンジニア。特にSPA開発の経験が豊富で、求める要件に高い適合性を示しています。",
      "strengths": [
        "フロントエンド技術への深い知識",
        "複数のプロジェクト経験"
      ],
      "potential_gaps": [
        "大規模チームでのリード経験が限定的"
      ],
      "recommended_questions": [
        "チームでの技術的な意思決定プロセスにどう関わってきましたか？",
        "大規模アプリケーションのパフォーマンス最適化の経験について教えてください"
      ]
    },
    "interview_schedule": [
      {
        "stage": "initial_screening",
        "date": "2023-05-25T10:00:00Z",
        "duration": 30,
        "format": "video",
        "interviewers": [
          {
            "id": "u23456-78901",
            "name": "佐藤HR部長"
          }
        ],
        "status": "completed",
        "feedback": {
          "overall_rating": 4.5,
          "strengths": ["コミュニケーション能力", "技術知識"],
          "concerns": ["マネジメント経験不足"],
          "summary": "コミュニケーション能力が高く、技術的な質問にも的確に応答。次のステップに進めるべき候補者。"
        }
      },
      {
        "stage": "technical_assessment",
        "date": "2023-06-05T13:00:00Z",
        "duration": 120,
        "format": "coding_test",
        "status": "completed",
        "feedback": {
          "overall_rating": 4.2,
          "code_quality": 4.5,
          "problem_solving": 4.0,
          "summary": "良く構造化されたコードで、問題を効率的に解決しています。テストカバレッジも十分でした。"
        }
      }
    ],
    "notes": [
      {
        "id": "n12345",
        "user": {
          "id": "u23456-78901",
          "name": "佐藤HR部長"
        },
        "date": "2023-05-25T11:30:00Z",
        "content": "一次面接では積極的なコミュニケーションと質問があり、好印象。特にReactの状態管理についての知識が深い。",
        "visibility": "internal"
      },
      {
        "id": "n12346",
        "user": {
          "id": "u34567-89012",
          "name": "高橋開発マネージャー"
        },
        "date": "2023-06-10T14:20:00Z",
        "content": "コーディング課題のレビューを完了。コードの品質が高く、拡張性も考慮されている。チーム開発の経験も十分。",
        "visibility": "internal"
      }
    ],
    "documents": [
      {
        "id": "d12345",
        "type": "resume",
        "name": "田中政夫_履歴書.pdf",
        "upload_date": "2023-05-20T10:30:00Z",
        "url": "/secure/documents/d12345.pdf"
      },
      {
        "id": "d12346",
        "type": "portfolio",
        "name": "ポートフォリオ.pdf",
        "upload_date": "2023-05-20T10:35:00Z",
        "url": "/secure/documents/d12346.pdf"
      }
    ],
    "communications": [
      {
        "id": "com12345",
        "type": "email",
        "direction": "outbound",
        "sender": "佐藤HR部長",
        "recipient": "masao.tanaka@example.com",
        "subject": "テックカンパニーの一次面接のご案内",
        "sent_date": "2023-05-22T09:15:00Z",
        "status": "delivered"
      },
      {
        "id": "com12346",
        "type": "email",
        "direction": "inbound",
        "sender": "masao.tanaka@example.com",
        "recipient": "hr@techcompany.co.jp",
        "subject": "Re: テックカンパニーの一次面接のご案内",
        "sent_date": "2023-05-22T10:45:00Z",
        "status": "read"
      }
    ],
    "references": [
      {
        "id": "r12345",
        "name": "佐々木一郎",
        "company": "スタートアップ株式会社",
        "position": "CTO",
        "relationship": "直属の上司",
        "email": "sasaki@startup.co.jp",
        "phone": "+81901234568",
        "status": "not_contacted"
      }
    ],
    "onboarding_status": null,
    "created_at": "2023-05-20T10:30:00Z",
    "updated_at": "2023-06-15T15:00:00Z"
  }
}
```

### 6.5 候補者のステータス更新

#### リクエスト

```http
PATCH /recruitment/candidates/{candidate_id}/status
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "status": "interview_scheduled",
  "notes": "一次面接を6月20日に設定しました。",
  "stage_transition": {
    "from": "shortlisted",
    "to": "interview",
    "reason": "書類選考通過"
  }
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "c12345-67890",
    "status": "interview_scheduled",
    "previous_status": "shortlisted",
    "updated_at": "2023-06-15T16:30:00Z",
    "updated_by": "u23456-78901",
    "next_steps": "一次面接の実施"
  }
}
```

### 6.6 面接フィードバック登録

#### リクエスト

```http
POST /recruitment/candidates/{candidate_id}/interviews
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "interview_stage": "technical",
  "date": "2023-06-20T14:00:00Z",
  "duration_minutes": 60,
  "interviewers": ["u34567-89012"],
  "format": "video",
  "feedback": {
    "overall_rating": 4.5,
    "technical_skills": 4.7,
    "communication": 4.2,
    "cultural_fit": 4.5,
    "strengths": [
      "深いReactの知識",
      "アーキテクチャ設計の経験",
      "コミュニケーション能力"
    ],
    "weaknesses": [
      "パフォーマンス最適化の経験がやや限定的"
    ],
    "notes": "全体的に非常に優れた候補者。特に技術的な深さと設計能力が印象的。",
    "recommendation": "hire",
    "next_steps": "最終面接に進めることを推奨"
  }
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "i12345-67890",
    "candidate_id": "c12345-67890",
    "job_posting_id": "j12345-67890",
    "interview_stage": "technical",
    "date": "2023-06-20T14:00:00Z",
    "duration_minutes": 60,
    "format": "video",
    "interviewers": [
      {
        "id": "u34567-89012",
        "name": "高橋開発マネージャー"
      }
    ],
    "status": "completed",
    "feedback": {
      "overall_rating": 4.5,
      "recommendation": "hire",
      "next_steps": "最終面接に進めることを推奨"
    },
    "created_at": "2023-06-20T15:30:00Z",
    "updated_at": "2023-06-20T15:30:00Z"
  }
}
```

### 6.7 オファー作成

#### リクエスト

```http
POST /recruitment/candidates/{candidate_id}/offers
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "position": "シニアフロントエンドエンジニア",
  "department_id": "d12345-67890",
  "manager_id": "e98765-43210",
  "start_date": "2023-09-01",
  "expiration_date": "2023-07-15",
  "compensation": {
    "base_salary": 7500000,
    "currency": "JPY",
    "bonus": {
      "type": "performance",
      "target_percentage": 10,
      "conditions": "業績目標達成時"
    },
    "equity": {
      "type": "stock_options",
      "amount": 1000,
      "vesting_schedule": "4年ベスティング（1年クリフ）"
    }
  },
  "benefits": [
    "健康保険",
    "厚生年金",
    "通勤交通費",
    "リモートワーク可",
    "フレックスタイム制"
  ],
  "additional_notes": "特別な技術スキルと経験を考慮した給与設定となっています。"
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "o12345-67890",
    "candidate_id": "c12345-67890",
    "job_posting_id": "j12345-67890",
    "status": "draft",
    "position": "シニアフロントエンドエンジニア",
    "department": {
      "id": "d12345-67890",
      "name": "開発部門"
    },
    "manager": {
      "id": "e98765-43210",
      "name": "鈴木花子"
    },
    "start_date": "2023-09-01",
    "expiration_date": "2023-07-15",
    "compensation": {
      "base_salary": 7500000,
      "currency": "JPY"
    },
    "offer_document_url": null,
    "created_at": "2023-06-25T10:00:00Z",
    "created_by": "u23456-78901",
    "updated_at": "2023-06-25T10:00:00Z"
  }
}
```

## 7. 組織 API

組織構造と部門管理のためのAPIです。

### 7.1 部門一覧取得

#### リクエスト

```http
GET /organization/departments
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "d12345-67890",
      "name": "開発部門",
      "code": "DEV",
      "description": "製品開発を担当する部門",
      "manager_id": "e98765-43210",
      "manager_name": "鈴木花子",
      "parent_department_id": null,
      "headcount": 42,
      "created_at": "2020-01-15T00:00:00Z",
      "updated_at": "2023-04-01T10:30:00Z"
    },
    {
      "id": "d23456-78901",
      "name": "マーケティング部門",
      "code": "MKT",
      "description": "マーケティング戦略と実行を担当する部門",
      "manager_id": "e34567-89012",
      "manager_name": "佐藤一郎",
      "parent_department_id": null,
      "headcount": 18,
      "created_at": "2020-01-15T00:00:00Z",
      "updated_at": "2023-03-01T15:45:00Z"
    }
  ],
  "meta": {
    "pagination": {
      "total": 8,
      "count": 8,
      "per_page": 10,
      "current_page": 1,
      "total_pages": 1
    }
  }
}
```

### 7.2 部門詳細取得

#### リクエスト

```http
GET /organization/departments/{department_id}
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "d12345-67890",
    "name": "開発部門",
    "code": "DEV",
    "description": "製品開発を担当する部門",
    "long_description": "Web・モバイルアプリケーション開発およびサーバーサイドシステムの開発・運用を担当する部門。顧客要件に合わせたカスタム開発も行う。",
    "manager": {
      "id": "e98765-43210",
      "name": "鈴木花子",
      "title": "開発部長",
      "email": "hanako.suzuki@example.com"
    },
    "parent_department": null,
    "sub_departments": [
      {
        "id": "d34567-89012",
        "name": "フロントエンド開発チーム",
        "code": "DEV-FE",
        "headcount": 15
      },
      {
        "id": "d45678-90123",
        "name": "バックエンド開発チーム",
        "code": "DEV-BE",
        "headcount": 18
      },
      {
        "id": "d56789-01234",
        "name": "QAチーム",
        "code": "DEV-QA",
        "headcount": 8
      }
    ],
    "headcount": {
      "total": 42,
      "by_role": [
        {
          "role": "エンジニア",
          "count": 30
        },
        {
          "role": "テクニカルリード",
          "count": 5
        },
        {
          "role": "QAスペシャリスト",
          "count": 5
        },
        {
          "role": "マネージャー",
          "count": 2
        }
      ]
    },
    "locations": [
      {
        "city": "東京",
        "country": "日本",
        "employee_count": 35
      },
      {
        "city": "大阪",
        "country": "日本",
        "employee_count": 7
      }
    ],
    "metrics": {
      "attrition_rate": 0.08,
      "average_tenure": 3.2,
      "open_positions": 3,
      "average_performance": 3.9
    },
    "budget": {
      "fiscal_year": 2023,
      "total": 420000000,
      "spent": 210000000,
      "remaining": 210000000,
      "currency": "JPY"
    },
    "created_at": "2020-01-15T00:00:00Z",
    "updated_at": "2023-04-01T10:30:00Z"
  }
}
```

### 7.3 組織図取得

#### リクエスト

```http
GET /organization/structure
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**クエリパラメータ**

| パラメータ | 型 | 説明 | デフォルト |
|-----------|-----|------|-----------|
| `depth` | integer | 取得する階層の深さ | 2 |
| `include_employees` | boolean | 従業員情報を含めるかどうか | false |

#### レスポンス

```json
{
  "success": true,
  "data": {
    "organization": {
      "name": "テックカンパニー株式会社",
      "ceo": {
        "id": "e12345-67890",
        "name": "山本太郎",
        "title": "CEO"
      },
      "employee_count": 156
    },
    "departments": [
      {
        "id": "d12345-67890",
        "name": "開発部門",
        "code": "DEV",
        "manager": {
          "id": "e98765-43210",
          "name": "鈴木花子",
          "title": "開発部長"
        },
        "headcount": 42,
        "sub_departments": [
          {
            "id": "d34567-89012",
            "name": "フロントエンド開発チーム",
            "code": "DEV-FE",
            "manager": {
              "id": "e23456-78901",
              "name": "山田次郎",
              "title": "フロントエンドリード"
            },
            "headcount": 15,
            "employees": [] // include_employees=true の場合は従業員一覧が含まれる
          },
          {
            "id": "d45678-90123",
            "name": "バックエンド開発チーム",
            "code": "DEV-BE",
            "manager": {
              "id": "e34567-89012",
              "name": "佐藤三郎",
              "title": "バックエンドリード"
            },
            "headcount": 18
          }
        ]
      },
      {
        "id": "d23456-78901",
        "name": "マーケティング部門",
        "code": "MKT",
        "manager": {
          "id": "e45678-90123",
          "name": "田中四郎",
          "title": "マーケティング部長"
        },
        "headcount": 18,
        "sub_departments": []
      }
    ]
  },
  "meta": {
    "generated_at": "2023-07-01T10:00:00Z",
    "structure_version": "v20230701"
  }
}
```

### 7.4 部門作成

#### リクエスト

```http
POST /organization/departments
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "name": "製品管理部",
  "code": "PM",
  "description": "製品戦略とロードマップを担当する部門",
  "manager_id": "e56789-01234",
  "parent_department_id": null,
  "location": {
    "city": "東京",
    "country": "日本"
  },
  "budget": {
    "fiscal_year": 2023,
    "total": 150000000,
    "currency": "JPY"
  }
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "d67890-12345",
    "name": "製品管理部",
    "code": "PM",
    "description": "製品戦略とロードマップを担当する部門",
    "manager_id": "e56789-01234",
    "manager_name": "伊藤五郎",
    "parent_department_id": null,
    "headcount": 0,
    "created_at": "2023-07-01T15:30:00Z",
    "updated_at": "2023-07-01T15:30:00Z"
  }
}
```

### 7.5 役職階層取得

#### リクエスト

```http
GET /organization/job-levels
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "jl1",
      "code": "L1",
      "name": "アソシエイト",
      "description": "エントリーレベルの役職",
      "min_salary": 3500000,
      "mid_salary": 4000000,
      "max_salary": 4500000,
      "currency": "JPY",
      "employee_count": 35,
      "min_experience": 0,
      "max_experience": 2
    },
    {
      "id": "jl2",
      "code": "L2",
      "name": "シニアアソシエイト",
      "description": "2-4年の経験を持つ中堅レベル",
      "min_salary": 4500000,
      "mid_salary": 5250000,
      "max_salary": 6000000,
      "currency": "JPY",
      "employee_count": 42,
      "min_experience": 2,
      "max_experience": 4
    },
    {
      "id": "jl3",
      "code": "L3",
      "name": "マネージャー",
      "description": "部門やチームの管理職",
      "min_salary": 6000000,
      "mid_salary": 7500000,
      "max_salary": 9000000,
      "currency": "JPY",
      "employee_count": 18,
      "min_experience": 5,
      "max_experience": 10
    }
  ],
  "meta": {
    "effective_date": "2023-04-01",
    "next_review_date": "2024-04-01"
  }
}
```

## 8. AI API

AIアシスタントと予測機能を提供するAPIです。

### 8.1 AIアシスタント質問応答

#### リクエスト

```http
POST /ai/assistant
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "query": "開発部門の離職率が上昇している原因として考えられる要素は何でしょうか？",
  "context": {
    "department_id": "d12345-67890",
    "time_period": "6m",
    "conversation_id": "conv12345" // 会話を継続する場合に指定
  }
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "response": "開発部門の離職率上昇については、以下の要因が考えられます：\n\n1. **給与競争力の低下**: 過去6ヶ月のデータによると、業界の平均給与が8%上昇したのに対し、社内の調整は5%にとどまっています。\n\n2. **ワークライフバランスの課題**: 直近の従業員サーベイでは、残業時間が前年比15%増加しており、エンゲージメントスコアが10ポイント低下しています。\n\n3. **キャリア成長の機会**: 昇進率が他部門と比較して25%低く、同部門内での異動や成長機会が限られている可能性があります。\n\n具体的なアクションとしては、市場競争力のある給与調整、プロジェクト負荷の分散、キャリアパスの明確化が効果的と思われます。詳細な分析と対策案を作成しますか？",
    "sources": [
      {
        "type": "employee_survey",
        "date": "2023-05-15",
        "relevance": 0.85
      },
      {
        "type": "compensation_analysis",
        "date": "2023-04-01",
        "relevance": 0.9
      },
      {
        "type": "exit_interviews",
        "date": "2023-02-20 - 2023-06-30",
        "relevance": 0.75
      }
    ],
    "confidence": 0.87,
    "follow_up_suggestions": [
      "開発部門のエンゲージメント向上策を教えてください",
      "競合他社と比較した給与水準の詳細を知りたい",
      "離職リスクが高い具体的な社員層について教えてください"
    ],
    "conversation_id": "conv12345",
    "timestamp": "2023-07-01T16:15:00Z"
  }
}
```

### 8.2 従業員リスクスコア予測

#### リクエスト

```http
POST /ai/predict/retention-risk
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "employee_id": "e12d45f6-7890-abcd-ef12-3456789abcde",
  "include_factors": true,
  "include_recommendations": true
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "employee_id": "e12d45f6-7890-abcd-ef12-3456789abcde",
    "risk_score": 0.75,
    "risk_level": "high",
    "prediction_date": "2023-07-01T00:00:00Z",
    "confidence": 0.82,
    "risk_trend": {
      "previous_score": 0.65,
      "previous_date": "2023-06-01T00:00:00Z",
      "change": 0.1,
      "trend": "increasing"
    },
    "contributing_factors": [
      {
        "factor": "compensation_competitiveness",
        "description": "市場平均より15%低い給与水準",
        "impact_weight": 0.35,
        "is_actionable": true
      },
      {
        "factor": "workload",
        "description": "過去3ヶ月の平均残業時間が部門平均より25%高い",
        "impact_weight": 0.25,
        "is_actionable": true
      },
      {
        "factor": "career_growth",
        "description": "直近2年間で役職の変更なし",
        "impact_weight": 0.20,
        "is_actionable": true
      },
      {
        "factor": "manager_relationship",
        "description": "最近のマネージャー変更後、1on1の頻度が減少",
        "impact_weight": 0.15,
        "is_actionable": true
      }
    ],
    "recommendations": [
      {
        "action": "compensation_review",
        "description": "市場競争力のある給与調整の実施",
        "expected_impact": 0.25,
        "priority": "high",
        "time_sensitivity": "immediate"
      },
      {
        "action": "workload_adjustment",
        "description": "プロジェクト負荷の再配分と追加リソースの検討",
        "expected_impact": 0.20,
        "priority": "high",
        "time_sensitivity": "immediate"
      },
      {
        "action": "career_discussion",
        "description": "キャリアパスと成長機会についての1on1ミーティングの設定",
        "expected_impact": 0.15,
        "priority": "medium",
        "time_sensitivity": "within_2_weeks"
      }
    ],
    "model_version": "v3.2.1",
    "prediction_id": "pred-12345-67890"
  }
}
```

### 8.3 候補者適合度予測

#### リクエスト

```http
POST /ai/predict/candidate-match
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "candidate_id": "c12345-67890",
  "job_posting_id": "j12345-67890"
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "candidate_id": "c12345-67890",
    "job_posting_id": "j12345-67890",
    "match_score": 0.85,
    "compatibility_level": "high",
    "technical_match": 0.90,
    "cultural_match": 0.82,
    "experience_match": 0.85,
    "skill_analysis": [
      {
        "skill": "React",
        "required_level": "expert",
        "candidate_level": "expert",
        "match_score": 1.0
      },
      {
        "skill": "TypeScript",
        "required_level": "advanced",
        "candidate_level": "advanced",
        "match_score": 1.0
      },
      {
        "skill": "Next.js",
        "required_level": "advanced",
        "candidate_level": "intermediate",
        "match_score": 0.7
      }
    ],
    "potential_growth_areas": [
      {
        "area": "Next.js開発経験",
        "importance": "medium",
        "development_difficulty": "low",
        "estimated_learning_time": "1-2週間"
      }
    ],
    "similar_successful_hires": 3,
    "interview_recommendations": [
      {
        "focus_area": "大規模プロジェクト経験",
        "suggested_questions": [
          "これまでの最も大規模なプロジェクトについて、あなたの役割とプロジェクトの規模を具体的に説明してください。",
          "複雑なアーキテクチャの設計において、どのように意思決定を行いましたか？"
        ]
      },
      {
        "focus_area": "パフォーマンス最適化",
        "suggested_questions": [
          "大規模Reactアプリケーションで対応したパフォーマンスボトルネックとその解決策について教えてください。",
          "レンダリングパフォーマンス向上のためにどのような手法を適用していますか？"
        ]
      }
    ],
    "model_version": "v2.5.0",
    "prediction_id": "pred-23456-78901"
  }
}
```

### 8.4 パフォーマンス予測

#### リクエスト

```http
POST /ai/predict/performance
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "employee_id": "e12d45f6-7890-abcd-ef12-3456789abcde",
  "time_period": "next_6m"
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "employee_id": "e12d45f6-7890-abcd-ef12-3456789abcde",
    "current_performance": 4.2,
    "predicted_performance": 4.5,
    "confidence": 0.78,
    "time_period": "next_6m",
    "trajectory": "increasing",
    "potential_indicators": [
      {
        "indicator": "skill_development",
        "description": "現在学習中の新技術が今後のプロジェクトで活用できる",
        "impact": 0.15
      },
      {
        "indicator": "team_dynamics",
        "description": "チーム再編成により、より適したロールに配置される予定",
        "impact": 0.12
      },
      {
        "indicator": "project_complexity",
        "description": "次期プロジェクトでは、得意とする領域で貢献度が高まる見込み",
        "impact": 0.1
      }
    ],
    "risk_factors": [
      {
        "factor": "workload_increase",
        "description": "次期プロジェクトでの負荷増加が予想される",
        "impact": -0.05,
        "mitigation": "プロジェクト計画段階での適切なリソース配分"
      }
    ],
    "development_recommendations": [
      {
        "area": "リーダーシップスキル",
        "description": "部門横断プロジェクトでのリード経験を増やす",
        "expected_impact": 0.2,
        "resources": [
          {
            "type": "training",
            "name": "テクニカルリーダーシップ研修",
            "url": "https://learning.hrxai.com/courses/tech-leadership"
          }
        ]
      },
      {
        "area": "プレゼンテーションスキル",
        "description": "社内勉強会やナレッジシェアの機会を増やす",
        "expected_impact": 0.15,
        "resources": [
          {
            "type": "workshop",
            "name": "効果的な技術プレゼンテーション講座",
            "url": "https://learning.hrxai.com/workshops/tech-presentation"
          }
        ]
      }
    ],
    "model_version": "v2.1.0",
    "prediction_id": "pred-34567-89012"
  }
}
```

### 8.5 求人記述最適化

#### リクエスト

```http
POST /ai/optimize/job-description
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "job_title": "シニアフロントエンドエンジニア",
  "original_description": "弊社では、Reactを使用したWebアプリケーション開発の経験が5年以上ある優秀なエンジニアを求めています。タスク管理能力が高く、迅速に作業を完了できる方を歓迎します。",
  "department_id": "d12345-67890",
  "optimization_goals": ["inclusivity", "engagement", "clarity"],
  "tone": "professional",
  "include_alternatives": true
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "original_text": "弊社では、Reactを使用したWebアプリケーション開発の経験が5年以上ある優秀なエンジニアを求めています。タスク管理能力が高く、迅速に作業を完了できる方を歓迎します。",
    "optimized_text": "当社の開発チームでは、Reactを活用したWebアプリケーション開発において、設計から実装までを主導できるフロントエンドエンジニアを募集しています。5年程度の実務経験があり、ユーザー中心の高品質なインターフェースを構築することに情熱をお持ちの方を歓迎します。複雑な要件を整理し、チームと協力しながらプロジェクトを成功に導く能力を重視しています。多様な視点を大切にする環境で、共に成長していきましょう。",
    "optimization_details": {
      "inclusivity_score": {
        "before": 0.65,
        "after": 0.9,
        "improvements": [
          "「優秀な」という主観的表現を具体的なスキル記述に変更",
          "経験年数を「5年程度」と柔軟な表現に修正",
          "多様性を尊重する文言を追加"
        ]
      },
      "engagement_score": {
        "before": 0.5,
        "after": 0.85,
        "improvements": [
          "具体的な役割と責任を追加",
          "passion pointを明確化",
          "チーム環境についての情報を追加"
        ]
      },
      "clarity_score": {
        "before": 0.7,
        "after": 0.9,
        "improvements": [
          "具体的な職務内容を追加",
          "求める能力をより明確に記述",
          "文章構造を改善"
        ]
      }
    },
    "alternatives": [
      {
        "text": "モダンなWeb開発技術に熱意を持ち、Reactエコシステムでの開発経験（目安：5年前後）をお持ちの方を探しています。ユーザーインターフェースの設計と実装において、品質とパフォーマンスのバランスを取りながら、チームと協力して複雑な課題を解決できるフロントエンドエンジニアを募集しています。多様なバックグラウンドや働き方を尊重する環境で、技術的な挑戦を楽しみたい方をお待ちしています。",
        "focus": "work_environment",
        "tone": "friendly"
      },
      {
        "text": "Reactを中心としたフロントエンド技術に精通し、複雑なWebアプリケーションの設計・開発経験を持つシニアエンジニアを求めています。5年相当の実務経験を通じて培われた、コンポーネント設計、状態管理、パフォーマンス最適化のスキルを活かし、ユーザー体験の向上と技術的負債の削減に貢献いただける方を歓迎します。チームメンバーの成長を支援しながら、製品の品質向上に取り組める環境です。",
        "focus": "technical_expertise",
        "tone": "professional"
      }
    ],
    "processing_id": "proc-45678-90123"
  }
}
```

## 9. 統合 API

外部システムとの連携を管理するAPIです。

### 9.1 統合一覧の取得

#### リクエスト

```http
GET /integrations
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "int-12345",
      "type": "hris",
      "provider": "workday",
      "name": "Workday HRIS Integration",
      "status": "active",
      "connected_at": "2023-01-15T10:00:00Z",
      "last_sync": "2023-07-01T03:00:00Z",
      "sync_frequency": "daily",
      "configured_modules": ["employees", "departments", "positions"]
    },
    {
      "id": "int-23456",
      "type": "ats",
      "provider": "greenhouse",
      "name": "Greenhouse ATS Integration",
      "status": "active",
      "connected_at": "2023-02-10T14:30:00Z",
      "last_sync": "2023-07-01T04:00:00Z",
      "sync_frequency": "hourly",
      "configured_modules": ["jobs", "candidates", "interviews"]
    },
    {
      "id": "int-34567",
      "type": "lms",
      "provider": "cornerstone",
      "name": "Cornerstone LMS Integration",
      "status": "inactive",
      "connected_at": "2023-03-05T09:15:00Z",
      "last_sync": "2023-06-15T05:00:00Z",
      "sync_frequency": "daily",
      "configured_modules": ["courses", "trainings", "certifications"]
    }
  ]
}
```

### 9.2 統合詳細の取得

#### リクエスト

```http
GET /integrations/{integration_id}
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "int-12345",
    "type": "hris",
    "provider": "workday",
    "name": "Workday HRIS Integration",
    "description": "Workdayとの従業員データ同期統合",
    "status": "active",
    "health": {
      "status": "healthy",
      "last_check": "2023-07-01T08:00:00Z",
      "issues": []
    },
    "connection_details": {
      "api_endpoint": "https://api.workday.com/ccx/v1/tenant123",
      "authentication_method": "oauth2",
      "created_by": "admin_user_id"
    },
    "connected_at": "2023-01-15T10:00:00Z",
    "last_sync": {
      "timestamp": "2023-07-01T03:00:00Z",
      "status": "success",
      "records_processed": 156,
      "errors": 0,
      "warnings": 2
    },
    "sync_history": [
      {
        "timestamp": "2023-07-01T03:00:00Z",
        "status": "success",
        "records_processed": 156,
        "duration_seconds": 45
      },
      {
        "timestamp": "2023-06-30T03:00:00Z",
        "status": "success",
        "records_processed": 155,
        "duration_seconds": 42
      }
    ],
    "sync_configuration": {
      "frequency": "daily",
      "schedule": "0 3 * * *", // cron 形式
      "timeout_seconds": 300,
      "retry_policy": {
        "max_retries": 3,
        "backoff_seconds": 60
      }
    },
    "data_mapping": [
      {
        "source_field": "worker_id",
        "target_field": "employee_id",
        "transformation": "direct",
        "is_required": true
      },
      {
        "source_field": "first_name",
        "target_field": "first_name",
        "transformation": "direct",
        "is_required": true
      },
      {
        "source_field": "department_reference",
        "target_field": "department_id",
        "transformation": "lookup",
        "is_required": true
      }
    ],
    "configured_modules": [
      {
        "name": "employees", 
        "status": "enabled",
        "sync_direction": "bidirectional"
      },
      {
        "name": "departments", 
        "status": "enabled",
        "sync_direction": "import_only"
      },
      {
        "name": "positions", 
        "status": "enabled",
        "sync_direction": "import_only"
      }
    ],
    "permissions": {
      "read": ["employees", "departments", "positions"],
      "write": ["employees"]
    },
    "created_at": "2023-01-15T09:30:00Z",
    "created_by": "admin_user_id",
    "updated_at": "2023-06-01T14:20:00Z",
    "updated_by": "admin_user_id"
  }
}
```

### 9.3 統合の同期実行

#### リクエスト

```http
POST /integrations/{integration_id}/sync
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "modules": ["employees", "departments"], // オプション、指定しない場合は全モジュール
  "full_sync": false // 増分同期かフル同期かを指定
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "integration_id": "int-12345",
    "sync_id": "sync-56789",
    "status": "in_progress",
    "modules": ["employees", "departments"],
    "full_sync": false,
    "started_at": "2023-07-01T16:45:00Z",
    "estimated_completion": "2023-07-01T16:50:00Z"
  }
}
```

### 9.4 統合の設定更新

#### リクエスト

```http
PATCH /integrations/{integration_id}
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "name": "Updated Workday Integration",
  "sync_configuration": {
    "frequency": "hourly",
    "schedule": "0 * * * *"
  },
  "configured_modules": [
    {
      "name": "employees",
      "status": "enabled",
      "sync_direction": "bidirectional"
    },
    {
      "name": "departments",
      "status": "disabled"
    }
  ]
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "int-12345",
    "name": "Updated Workday Integration",
    "sync_configuration": {
      "frequency": "hourly",
      "schedule": "0 * * * *",
      "timeout_seconds": 300,
      "retry_policy": {
        "max_retries": 3,
        "backoff_seconds": 60
      }
    },
    "configured_modules": [
      {
        "name": "employees",
        "status": "enabled",
        "sync_direction": "bidirectional"
      },
      {
        "name": "departments",
        "status": "disabled",
        "sync_direction": "import_only"
      },
      {
        "name": "positions",
        "status": "enabled",
        "sync_direction": "import_only"
      }
    ],
    "updated_at": "2023-07-01T16:55:00Z",
    "updated_by": "admin_user_id"
  }
}
```

## 10. Webhook

イベント通知を受け取るためのWebhook設定を管理するAPIです。

### 10.1 Webhook登録

#### リクエスト

```http
POST /webhooks
Content-Type: application/json
Authorization: Bearer YOUR_ACCESS_TOKEN

{
  "url": "https://your-application.com/api/hrxai-webhooks",
  "name": "従業員更新通知",
  "description": "従業員データ更新時に通知を受け取る",
  "events": [
    "employee.created",
    "employee.updated",
    "employee.status_changed"
  ],
  "status": "active",
  "secret": "your_webhook_secret_for_verification"
}
```

#### レスポンス

```json
{
  "success": true,
  "data": {
    "id": "wh-12345",
    "url": "https://your-application.com/api/hrxai-webhooks",
    "name": "従業員更新通知",
    "description": "従業員データ更新時に通知を受け取る",
    "events": [
      "employee.created",
      "employee.updated",
      "employee.status_changed"
    ],
    "status": "active",
    "created_at": "2023-07-01T17:00:00Z",
    "updated_at": "2023-07-01T17:00:00Z"
  }
}
```

### 10.2 Webhook一覧取得

#### リクエスト

```http
GET /webhooks
Authorization: Bearer YOUR_ACCESS_TOKEN
```

#### レスポンス

```json
{
  "success": true,
  "data": [
    {
      "id": "wh-12345",
      "url": "https://your-application.com/api/hrxai-webhooks",
      "name": "従業員更新通知",
      "status": "active",
      "events": [
        "employee.created",
        "employee.updated",
        "employee.status_changed"
      ],
      "created_at": "2023-07-01T17:00:00Z",
      "updated_at": "2023-07-01T17:00:00Z"
    },
    {
      "id": "wh-23456",
      "url": "https://your-application.com/api/hrxai-recruiting",
      "name": "採用活動通知",
      "status": "active",
      "events": [
        "candidate.created",
        "candidate.status_changed",
        "job_posting.created"
      ],
      "created_at": "2023-06-15T14:30:00Z",
      "updated_at": "2023-06-15T14:30:00Z"
    }
  ]
}
```

### 10.3 Webhook配信形式

HRX-AIからWebhookで送信されるペイロードの形式は以下の通りです：

```json
{
  "event": "employee.updated",
  "api_version": "v1",
  "created_at": "2023-07-01T17:30:00Z",
  "data": {
    "id": "e12d45f6-7890-abcd-ef12-3456789abcde",
    "first_name": "太郎",
    "last_name": "山田",
    // イベントに関連するその他のデータ
  },
  "metadata": {
    "user_id": "u12345", // 変更を行ったユーザーID（該当する場合）
    "changes": ["job_title", "department_id"], // 変更されたフィールド
    "resource_url": "/v1/employees/e12d45f6-7890-abcd-ef12-3456789abcde"
  }
}
```

## 11. エラー処理

HRX-AI APIは、エラーが発生した場合に以下のような形式でレスポンスを返します：

### エラーレスポンス形式

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "エラーの説明メッセージ",
    "details": {
      "field": "問題のあるフィールド名",
      "reason": "詳細な理由",
      "suggestion": "対処方法の提案"
    },
    "request_id": "req-abcdef-123456"
  }
}
```

### 共通エラーコード

| HTTPステータスコード | エラーコード | 説明 |
|-------------------|-------------|------|
| 400 | `invalid_request` | リクエストの形式が不正 |
| 401 | `unauthorized` | 認証が必要 |
| 403 | `forbidden` | 権限がない |
| 404 | `not_found` | リソースが見つからない |
| 409 | `conflict` | リソースの競合が発生 |
| 422 | `validation_error` | バリデーションエラー |
| 429 | `rate_limit_exceeded` | レート制限を超過 |
| 500 | `internal_server_error` | サーバー内部エラー |
| 503 | `service_unavailable` | サービス利用不可 |

### バリデーションエラーの例

```json
{
  "success": false,
  "error": {
    "code": "validation_error",
    "message": "入力データのバリデーションエラーが発生しました",
    "details": {
      "fields": [
        {
          "field": "email",
          "error": "有効なメールアドレス形式ではありません",
          "value": "invalid-email"
        },
        {
          "field": "hire_date",
          "error": "日付は現在より過去である必要があります",
          "value": "2025-01-01"
        }
      ]
    },
    "request_id": "req-abcdef-123456"
  }
}
```

## 12. レート制限

HRX-AI APIはレート制限を適用しています。制限を超えるとステータスコード429が返されます。

### ヘッダー

レート制限に関する情報は以下のレスポンスヘッダーで提供されます：

```
X-RateLimit-Limit: 100           # 期間あたりの最大リクエスト数
X-RateLimit-Remaining: 95        # 現在の期間で残りのリクエスト数
X-RateLimit-Reset: 1625097600    # 制限がリセットされる時間（Unix時間）
```

### プラン別制限

| APIプラン | 1分あたりの上限 | 1時間あたりの上限 | 1日あたりの上限 |
|----------|--------------|----------------|--------------|
| Basic    | 60           | 1,000          | 10,000       |
| Pro      | 120          | 5,000          | 50,000       |
| Enterprise | 300        | 15,000         | 無制限        |

## 13. バージョニング

HRX-AI APIは明示的なバージョニングを使用しています。

### バージョン指定

APIバージョンは以下の方法で指定できます

1. URLパス: `https://api.hrx-ai.com/v1/employees`
2. リクエストヘッダー: `Accept: application/json; version=1`

### APIライフサイクル

- 新しいAPIバージョンはリリースから最低12ヶ月間はサポートされます
- 廃止予定のAPIには`X-API-Deprecated: true`ヘッダーが含まれます
- 廃止の6ヶ月前に通知が行われます

## 14. API 実装例

### JavaScriptでのAPI呼び出し例

```javascript
// 従業員リスト取得
async function getEmployees() {
  try {
    const response = await fetch('https://api.hrx-ai.com/v1/employees', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${YOUR_ACCESS_TOKEN}`
      }
    });
    
    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(`API Error: ${errorData.error.message}`);
    }
    
    const data = await response.json();
    return data.data; // 従業員リストを返す
  } catch (error) {
    console.error('従業員データの取得に失敗しました:', error);
    throw error;
  }
}

// 新規従業員の登録
async function createEmployee(employeeData) {
  try {
    const response = await fetch('https://api.hrx-ai.com/v1/employees', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${YOUR_ACCESS_TOKEN}`
      },
      body: JSON.stringify(employeeData)
    });
    
    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(`API Error: ${errorData.error.message}`);
    }
    
    const data = await response.json();
    return data.data; // 作成された従業員データを返す
  } catch (error) {
    console.error('従業員の作成に失敗しました:', error);
    throw error;
  }
}
```

### Pythonでの実装例

```python
import requests

API_BASE_URL = "https://api.hrx-ai.com/v1"
ACCESS_TOKEN = "YOUR_ACCESS_TOKEN"

# ヘッダー設定
headers = {
    "Content-Type": "application/json",
    "Authorization": f"Bearer {ACCESS_TOKEN}"
}

# 離職リスク分析の取得
def get_retention_risk_analysis(department_id=None):
    url = f"{API_BASE_URL}/analytics/retention-risk"
    
    # クエリパラメータの設定
    params = {}
    if department_id:
        params["department_id"] = department_id
    
    response = requests.get(url, headers=headers, params=params)
    
    if response.status_code == 200:
        return response.json()["data"]
    else:
        error_data = response.json()
        raise Exception(f"APIエラー: {error_data['error']['message']}")

# AIアシスタントへの質問
def ask_ai_assistant(query, context=None):
    url = f"{API_BASE_URL}/ai/assistant"
    
    payload = {
        "query": query
    }
    
    if context:
        payload["context"] = context
    
    response = requests.post(url, headers=headers, json=payload)
    
    if response.status_code == 200:
        return response.json()["data"]
    else:
        error_data = response.json()
        raise Exception(f"AIアシスタントエラー: {error_data['error']['message']}")
```

---

このAPIドキュメントはHRX-AI開発チームによって提供され、定期的に更新されます。最新のAPIリファレンスは[APIダッシュボード](https://api.hrx-ai.com/docs)で常に確認できます。