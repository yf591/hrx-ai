
# 3. Data Schema Dictionary

このData Schema Dictionaryは、HRX-AIのデータ基盤を定義し、開発チームが一貫したデータモデルを理解・実装するための参照資料です。データモデルは、新しい機能要件や規制要件に応じて進化させていく必要があります。

## 目次
1. [概要](#1-概要)
2. [データモデル図](#2-データモデル図)
3. [テーブル定義](#3-テーブル定義)
   - [3.1 Organization (組織)](#31-organization-組織)
   - [3.2 Department (部門)](#32-department-部門)
   - [3.3 Employee (従業員)](#33-employee-従業員)
   - [3.4 PerformanceRecord (パフォーマンス記録)](#34-performancerecord-パフォーマンス記録)
   - [3.5 SkillProfile (スキルプロファイル)](#35-skillprofile-スキルプロファイル)
   - [3.6 EmployeeEngagement (従業員エンゲージメント)](#36-employeeengagement-従業員エンゲージメント)
   - [3.7 RecruitmentCycle (採用サイクル)](#37-recruitmentcycle-採用サイクル)
   - [3.8 Applicant (応募者)](#38-applicant-応募者)
   - [3.9 CompensationRecord (報酬記録)](#39-compensationrecord-報酬記録)
   - [3.10 LearningActivity (学習活動)](#310-learningactivity-学習活動)
4. [インデックス戦略](#4-インデックス戦略)
5. [テーブル関連図](#5-テーブル関連図)
6. [データライフサイクル管理](#6-データライフサイクル管理)
7. [データ暗号化戦略](#7-データ暗号化戦略)
8. [変更履歴管理](#8-変更履歴管理)
9. [アクセス制御ポリシー](#9-アクセス制御ポリシー)

## 1. 概要

このドキュメントでは、HRX-AIのデータベース設計について詳細に説明します。HRX-AIは、企業の人事業務を革新的に変革するHR AIエージェントであり、そのデータモデルは企業組織構造、従業員情報、パフォーマンス評価、スキル管理、採用活動などの人事データを包括的に管理できるように設計しています。

データベースは主にFirebaseを使用し、一部のAI関連データや高速アクセスが必要なデータについてはベクトルデータベースやRedis/Upstashを使用します。本ドキュメントでは、主にリレーショナルデータベース部分の設計を説明します。

## 2. データモデル図

以下はHRX-AIの主要データモデルを表すER図です

```
Organization ||--o{ Department : contains
Department ||--o{ Employee : employs
Employee ||--o{ PerformanceRecord : has
Employee ||--o{ SkillProfile : has
Employee ||--o{ EmployeeEngagement : measures
Employee ||--o{ CompensationRecord : receives
Employee ||--o{ LearningActivity : completes
RecruitmentCycle ||--o{ Applicant : receives
Employee ||--o| Employee : reports_to
```

## 3. テーブル定義

### 3.1 Organization (組織)

組織テーブルは、HRX-AIを利用する企業・組織の基本情報を格納します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 組織の一意識別子 |
| name | varchar(255) | NOT NULL | 組織名 |
| industry | varchar(100) | NOT NULL | 業界（IT、製造業、金融など） |
| established | date | | 設立日 |
| employee_count | integer | | 従業員総数 |
| metadata | jsonb | | 追加メタデータ（柔軟な拡張用） |
| contact_email | varchar(255) | | 主要連絡先メールアドレス |
| contact_phone | varchar(50) | | 主要連絡先電話番号 |
| address | text | | 本社住所 |
| website | varchar(255) | | 公式Webサイト |
| logo_url | varchar(512) | | ロゴ画像URL |
| subscription_tier | varchar(50) | DEFAULT 'free' | サブスクリプションプラン（free/pro/enterprise） |
| subscription_status | varchar(50) | DEFAULT 'active' | サブスクリプションステータス |
| settings | jsonb | | 組織レベルの設定 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| org_name_idx | name | B-tree | 組織名検索の最適化 |
| org_industry_idx | industry | B-tree | 業界別検索の最適化 |
| org_subscription_idx | subscription_tier, subscription_status | B-tree | サブスクリプション関連クエリの最適化 |

#### アクセス制御

- 読み取り: 管理者、システム管理者
- 更新: システム管理者のみ
- 作成/削除: システム管理者のみ

#### データライフサイクル

- 保持期間: 契約終了後5年
- アーカイブポリシー: 契約終了後1年でアーカイブ
- 削除ポリシー: 法的要件に基づく削除のみ許可

### 3.2 Department (部門)

部門テーブルは、組織内の部門・部署情報を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 部門の一意識別子 |
| organization_id | uuid | NOT NULL, FOREIGN KEY | 所属組織ID（Organizationテーブル参照） |
| name | varchar(100) | NOT NULL | 部門名 |
| description | text | | 部門の説明 |
| manager_id | uuid | FOREIGN KEY | 部門マネージャーID（Employeeテーブル参照） |
| department_metrics | jsonb | | 部門KPIや指標データ |
| parent_department_id | uuid | FOREIGN KEY | 親部門ID（自己参照） |
| cost_center | varchar(50) | | コストセンターコード |
| budget | decimal(15,2) | | 部門予算額 |
| location | varchar(255) | | 部門所在地 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |
| is_active | boolean | DEFAULT true | アクティブステータス |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| dept_org_idx | organization_id | B-tree | 組織別検索の最適化 |
| dept_manager_idx | manager_id | B-tree | マネージャーによる検索の最適化 |
| dept_parent_idx | parent_department_id | B-tree | 親部門による検索の最適化 |
| dept_name_idx | name | B-tree | 部門名検索の最適化 |

#### アクセス制御

- 読み取り: 組織管理者、部門マネージャー、HR管理者、関連従業員（限定ビュー）
- 更新: 組織管理者、指定されたHR管理者
- 作成/削除: 組織管理者のみ

#### データライフサイクル

- 保持期間: 組織データ保持期間に準拠
- アーカイブポリシー: 非アクティブ化から2年でアーカイブ
- 監査履歴: すべての変更は監査ログに記録

### 3.3 Employee (従業員)

従業員テーブルは、組織に所属する従業員の基本情報を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 従業員の一意識別子 |
| department_id | uuid | NOT NULL, FOREIGN KEY | 所属部門ID |
| first_name | varchar(100) | NOT NULL | 名 |
| last_name | varchar(100) | NOT NULL | 姓 |
| email | varchar(255) | NOT NULL, UNIQUE | メールアドレス |
| hire_date | date | NOT NULL | 入社日 |
| job_title | varchar(100) | NOT NULL | 役職・職位 |
| manager_id | uuid | FOREIGN KEY | 上司・マネージャーID（自己参照） |
| salary | decimal(15,2) | | 基本給 |
| employment_status | varchar(50) | NOT NULL, DEFAULT 'active' | 雇用ステータス（active/sabbatical/terminated） |
| retention_risk | float | | 離職リスクスコア（0.0〜1.0） |
| employee_id | varchar(50) | | 従業員番号（社内ID） |
| phone_number | varchar(50) | | 電話番号 |
| date_of_birth | date | | 生年月日 |
| address | text | | 住所 |
| emergency_contact | jsonb | | 緊急連絡先情報 |
| employment_type | varchar(50) | DEFAULT 'full-time' | 雇用形態（full-time/part-time/contractor） |
| termination_date | date | | 退職日 |
| termination_reason | varchar(255) | | 退職理由 |
| photo_url | varchar(512) | | プロフィール写真URL |
| social_profiles | jsonb | | ソーシャルプロフィールリンク |
| preferred_name | varchar(100) | | 通称・希望呼称 |
| pronouns | varchar(50) | | 代名詞の希望 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| emp_dept_idx | department_id | B-tree | 部門別検索の最適化 |
| emp_email_idx | email | B-tree | メールアドレス検索の最適化 |
| emp_manager_idx | manager_id | B-tree | マネージャー別検索の最適化 |
| emp_status_idx | employment_status | B-tree | 雇用ステータス別検索の最適化 |
| emp_risk_idx | retention_risk | B-tree | リスクスコアによるソート・検索の最適化 |
| emp_name_idx | last_name, first_name | B-tree | 氏名検索の最適化 |
| emp_hire_idx | hire_date | B-tree | 入社日検索の最適化 |

#### アクセス制御

- 読み取り（基本情報）: 本人、直属マネージャー、部門マネージャー、HR管理者
- 読み取り（機密情報）: HR管理者、システム管理者のみ
- 更新（基本情報）: 本人、HR管理者
- 更新（給与・リスクスコア）: HR管理者、システム管理者のみ
- 作成/削除: HR管理者、システム管理者のみ

#### データライフサイクル

- 保持期間: 退職後7年（法的要件に基づく）
- 匿名化ポリシー: 退職後2年で個人特定情報を匿名化
- アーカイブポリシー: 退職後3年でアーカイブ
- プライバシー措置: GDPR/CCPAに基づく削除要求に対応

### 3.4 PerformanceRecord (パフォーマンス記録)

パフォーマンスレコードテーブルは、従業員の評価記録を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 評価記録の一意識別子 |
| employee_id | uuid | NOT NULL, FOREIGN KEY | 評価対象従業員ID |
| evaluation_date | date | NOT NULL | 評価実施日 |
| overall_score | float | NOT NULL | 総合評価スコア（0.0〜10.0） |
| competency_scores | jsonb | | 能力別評価スコア |
| feedback | text | | フィードバックコメント |
| evaluator_id | uuid | NOT NULL, FOREIGN KEY | 評価者ID |
| evaluation_period_start | date | | 評価期間開始日 |
| evaluation_period_end | date | | 評価期間終了日 |
| evaluation_type | varchar(50) | DEFAULT 'regular' | 評価タイプ（regular/probation/project） |
| goals_achievement | jsonb | | 目標達成状況 |
| strengths | text | | 強み |
| improvement_areas | text | | 改善領域 |
| development_plan | text | | 能力開発計画 |
| acknowledgement_date | date | | 従業員確認日 |
| status | varchar(50) | DEFAULT 'draft' | ステータス（draft/in_review/final） |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| perf_emp_idx | employee_id | B-tree | 従業員別検索の最適化 |
| perf_eval_idx | evaluator_id | B-tree | 評価者別検索の最適化 |
| perf_date_idx | evaluation_date | B-tree | 日付別検索の最適化 |
| perf_score_idx | overall_score | B-tree | スコア検索の最適化 |
| perf_status_idx | status | B-tree | ステータス別検索の最適化 |
| perf_period_idx | evaluation_period_start, evaluation_period_end | B-tree | 期間検索の最適化 |

#### アクセス制御

- 読み取り: 本人、直属マネージャー、部門マネージャー、HR管理者
- 更新: 評価者（ドラフト段階のみ）、HR管理者
- 作成: 直属マネージャー、部門マネージャー、HR管理者
- 削除: HR管理者のみ（監査ログ記録必須）

#### データライフサイクル

- 保持期間: 従業員在籍中および退職後5年
- アーカイブポリシー: 従業員退職後2年でアーカイブ
- 削除ポリシー: 削除は許可されず、アーカイブのみ
- 履歴: すべてのバージョンを保持

### 3.5 SkillProfile (スキルプロファイル)

スキルプロファイルテーブルは、従業員のスキルと習熟度を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | スキルプロファイルの一意識別子 |
| employee_id | uuid | NOT NULL, FOREIGN KEY | 従業員ID |
| skill_name | varchar(100) | NOT NULL | スキル名称 |
| proficiency_level | integer | NOT NULL | 習熟度レベル（1-5） |
| last_updated | date | NOT NULL, DEFAULT CURRENT_DATE | 最終更新日 |
| is_certified | boolean | DEFAULT false | 認定資格の有無 |
| expiry_date | date | | 有効期限（資格の場合） |
| certification_details | jsonb | | 認定資格の詳細情報 |
| self_assessed | boolean | DEFAULT true | 自己評価かどうか |
| verified_by | uuid | | 検証者ID |
| verification_date | date | | 検証日 |
| skill_category | varchar(100) | | スキルカテゴリ |
| years_of_experience | float | | 経験年数 |
| interest_level | integer | | 関心レベル（1-5） |
| notes | text | | 備考 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| skill_emp_idx | employee_id | B-tree | 従業員別検索の最適化 |
| skill_name_idx | skill_name | B-tree | スキル名検索の最適化 |
| skill_level_idx | proficiency_level | B-tree | 習熟度検索の最適化 |
| skill_cat_idx | skill_category | B-tree | カテゴリ別検索の最適化 |
| skill_cert_idx | is_certified | B-tree | 認定資格検索の最適化 |

#### アクセス制御

- 読み取り: 本人、直属マネージャー、部門マネージャー、HR管理者
- 更新: 本人（自己評価部分）、直属マネージャー（検証部分）、HR管理者
- 作成: 本人、HR管理者
- 削除: HR管理者のみ

#### データライフサイクル

- 保持期間: 従業員在籍中および退職後3年
- アーカイブポリシー: 従業員退職後または3年更新なしでアーカイブ
- 履歴: スキル習熟度の変化履歴を別途保管

### 3.6 EmployeeEngagement (従業員エンゲージメント)

従業員エンゲージメントテーブルは、従業員のエンゲージメント調査結果を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | エンゲージメント記録の一意識別子 |
| employee_id | uuid | NOT NULL, FOREIGN KEY | 従業員ID |
| survey_date | date | NOT NULL | 調査実施日 |
| engagement_score | float | | エンゲージメントスコア（0.0〜10.0） |
| satisfaction_score | float | | 満足度スコア（0.0〜10.0） |
| survey_responses | jsonb | | 調査回答詳細 |
| sentiment_analysis | jsonb | | センチメント分析結果 |
| survey_type | varchar(50) | DEFAULT 'regular' | 調査タイプ（regular/pulse/exit） |
| key_drivers | jsonb | | 主要因子分析 |
| comments | text | | コメント・フィードバック |
| survey_id | uuid | | 関連調査ID |
| response_time | integer | | 回答所要時間（秒） |
| anonymized | boolean | DEFAULT true | 匿名化フラグ |
| response_complete | boolean | DEFAULT true | 回答完了フラグ |
| follow_up_required | boolean | DEFAULT false | フォローアップ必要フラグ |
| follow_up_notes | text | | フォローアップ備考 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| eng_emp_idx | employee_id | B-tree | 従業員別検索の最適化 |
| eng_date_idx | survey_date | B-tree | 日付別検索の最適化 |
| eng_score_idx | engagement_score | B-tree | スコア検索の最適化 |
| eng_type_idx | survey_type | B-tree | 調査タイプ別検索の最適化 |
| eng_follow_idx | follow_up_required | B-tree | フォローアップ必要分の検索最適化 |

#### アクセス制御

- 読み取り（個人データ）: 本人のみ（匿名でない場合）
- 読み取り（集計データ）: 直属マネージャー、部門マネージャー、HR管理者
- 更新: システムのみ（回答送信時）
- 作成: システムのみ（調査実施時）
- 削除: HR管理者のみ（緊急時のみ）

#### データライフサイクル

- 保持期間: 3年
- 匿名化ポリシー: 退職時に完全匿名化
- アーカイブポリシー: 3年経過後アーカイブ
- GDPR対応: 個人特定可能な自由回答は保存期間を短縮

### 3.7 RecruitmentCycle (採用サイクル)

採用サイクルテーブルは、採用活動と求人情報を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 採用サイクルの一意識別子 |
| department_id | uuid | NOT NULL, FOREIGN KEY | 採用部門ID |
| position_title | varchar(100) | NOT NULL | 職位名 |
| opening_date | date | NOT NULL | 募集開始日 |
| status | varchar(50) | NOT NULL, DEFAULT 'open' | ステータス（open/closed/on_hold） |
| applicant_count | integer | DEFAULT 0 | 応募者数 |
| time_to_fill | float | | 採用所要日数 |
| recruitment_cost | decimal(10,2) | | 採用コスト |
| job_description | text | | 職務内容 |
| requirements | text | | 応募要件 |
| preferred_qualifications | text | | 優遇条件 |
| salary_range_min | decimal(15,2) | | 給与下限 |
| salary_range_max | decimal(15,2) | | 給与上限 |
| hiring_manager_id | uuid | NOT NULL, FOREIGN KEY | 採用マネージャーID |
| recruiter_id | uuid | FOREIGN KEY | 採用担当者ID |
| target_hire_date | date | | 目標採用日 |
| job_posting_urls | jsonb | | 求人掲載URL |
| screening_questions | jsonb | | スクリーニング質問 |
| evaluation_criteria | jsonb | | 評価基準 |
| remote_option | varchar(50) | DEFAULT 'office' | リモートワークオプション（office/hybrid/remote） |
| employment_type | varchar(50) | NOT NULL | 雇用形態（full_time/part_time/contract） |
| priority_level | varchar(20) | DEFAULT 'medium' | 優先度（high/medium/low） |
| closed_date | date | | 募集終了日 |
| closure_reason | varchar(100) | | 募集終了理由 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| recruit_dept_idx | department_id | B-tree | 部門別検索の最適化 |
| recruit_status_idx | status | B-tree | ステータス別検索の最適化 |
| recruit_manager_idx | hiring_manager_id | B-tree | 採用マネージャー別検索の最適化 |
| recruit_date_idx | opening_date | B-tree | 日付別検索の最適化 |
| recruit_position_idx | position_title | B-tree | 職位名検索の最適化 |
| recruit_type_idx | employment_type | B-tree | 雇用形態別検索の最適化 |

#### アクセス制御

- 読み取り: 採用マネージャー、採用担当者、部門マネージャー、HR管理者
- 更新: 採用担当者、HR管理者
- 作成: 部門マネージャー、HR管理者
- 削除: HR管理者のみ

#### データライフサイクル

- 保持期間: 完了後5年
- アーカイブポリシー: 完了後2年でアーカイブ
- 監査要件: 採用決定の根拠と選考記録を保持

### 3.8 Applicant (応募者)

応募者テーブルは、採用サイクルへの応募者情報を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 応募者の一意識別子 |
| recruitment_cycle_id | uuid | NOT NULL, FOREIGN KEY | 関連採用サイクルID |
| first_name | varchar(100) | NOT NULL | 名 |
| last_name | varchar(100) | NOT NULL | 姓 |
| email | varchar(255) | NOT NULL | メールアドレス |
| match_score | float | | マッチングスコア（0.0〜1.0） |
| status | varchar(50) | NOT NULL, DEFAULT 'applied' | ステータス（applied/screening/interview/offer/rejected/hired） |
| skills_assessment | jsonb | | スキル評価データ |
| interview_feedback | jsonb | | 面接フィードバック |
| phone_number | varchar(50) | | 電話番号 |
| resume_url | varchar(512) | | 履歴書URL |
| cover_letter_url | varchar(512) | | カバーレターURL |
| portfolio_url | varchar(512) | | ポートフォリオURL |
| current_position | varchar(100) | | 現職 |
| current_company | varchar(100) | | 現所属企業 |
| years_of_experience | float | | 経験年数 |
| education | jsonb | | 学歴 |
| referral_source | varchar(100) | | 応募経路 |
| referrer_id | uuid | | 紹介者ID（従業員の場合） |
| expected_salary | decimal(15,2) | | 希望給与 |
| availability_date | date | | 入社可能日 |
| interview_dates | jsonb | | 面接日時 |
| interview_stages_completed | jsonb | | 完了した面接段階 |
| notes | text | | 備考 |
| rejection_reason | varchar(255) | | 不採用理由 |
| rejection_date | date | | 不採用通知日 |
| offer_details | jsonb | | オファー詳細 |
| offer_date | date | | オファー提示日 |
| offer_response | varchar(50) | | オファー回答（accepted/declined/pending） |
| offer_response_date | date | | オファー回答日 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| app_cycle_idx | recruitment_cycle_id | B-tree | 採用サイクル別検索の最適化 |
| app_status_idx | status | B-tree | ステータス別検索の最適化 |
| app_email_idx | email | B-tree | メールアドレス検索の最適化 |
| app_match_idx | match_score | B-tree | マッチスコア検索の最適化 |
| app_name_idx | last_name, first_name | B-tree | 氏名検索の最適化 |

#### アクセス制御

- 読み取り: 採用マネージャー、採用担当者、面接官（割り当てられた応募者のみ）
- 更新: 採用担当者、システム（AI評価時）
- 作成: 採用担当者、システム（応募フォーム経由）
- 削除: HR管理者のみ（GDPR削除要求対応時）

#### データライフサイクル

- 保持期間: 採用サイクル終了後2年
- 不採用データ: 6ヶ月後に匿名化（GDPR対応）
- 採用データ: 従業員レコードに転送
- 匿名化: 統計・分析用に匿名化データを長期保存

### 3.9 CompensationRecord (報酬記録)

報酬記録テーブルは、従業員の給与・報酬情報を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 報酬記録の一意識別子 |
| employee_id | uuid | NOT NULL, FOREIGN KEY | 従業員ID |
| base_salary | decimal(15,2) | NOT NULL | 基本給 |
| bonus | decimal(15,2) | | ボーナス額 |
| benefits | jsonb | | 福利厚生内容 |
| effective_date | date | NOT NULL | 適用開始日 |
| compensation_type | varchar(50) | DEFAULT 'regular' | 報酬タイプ（regular/promotion/annual_review/adjustment） |
| currency | varchar(3) | DEFAULT 'JPY' | 通貨コード |
| salary_review_date | date | | 次回給与見直し日 |
| previous_salary | decimal(15,2) | | 前回基本給 |
| change_percentage | float | | 変更率 |
| change_reason | text | | 変更理由 |
| approver_id | uuid | | 承認者ID |
| approval_date | date | | 承認日 |
| stock_options | jsonb | | ストックオプション詳細 |
| commission_structure | jsonb | | コミッション構造 |
| total_compensation | decimal(15,2) | | 総報酬額 |
| tax_withholdings | jsonb | | 税金控除情報 |
| retirement_contributions | jsonb | | 退職金積立情報 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| comp_emp_idx | employee_id | B-tree | 従業員別検索の最適化 |
| comp_date_idx | effective_date | B-tree | 日付別検索の最適化 |
| comp_type_idx | compensation_type | B-tree | タイプ別検索の最適化 |
| comp_salary_idx | base_salary | B-tree | 給与別検索の最適化 |
| comp_approver_idx | approver_id | B-tree | 承認者別検索の最適化 |

#### アクセス制御

- 読み取り: 本人（自身の記録のみ）、HR給与担当者、財務部門管理者（集計のみ）
- 更新: HR給与担当者のみ
- 作成: HR給与担当者、システム（昇給プロセス自動化時）
- 削除: システム管理者のみ（特別なケースのみ）

#### データライフサイクル

- 保持期間: 従業員退職後10年（税務・監査要件）
- 暗号化: すべての給与データは保存時暗号化
- アクセスログ: すべてのアクセスを監査ログに記録
- アーカイブ: 5年以上経過したデータは別ストレージにアーカイブ

### 3.10 LearningActivity (学習活動)

学習活動テーブルは、従業員のトレーニング・学習記録を管理します。

#### フィールド定義

| フィールド名 | データ型 | 制約 | 説明 |
|------------|--------|------|------|
| id | uuid | PRIMARY KEY | 学習活動の一意識別子 |
| employee_id | uuid | NOT NULL, FOREIGN KEY | 従業員ID |
| activity_name | varchar(255) | NOT NULL | 活動名称 |
| activity_type | varchar(50) | NOT NULL | 活動種類（course/certification/workshop/conference/self_study） |
| completion_date | date | | 完了日 |
| completion_score | float | | 完了スコア・評価 |
| skills_acquired | jsonb | | 習得したスキル |
| duration_hours | integer | | 所要時間（時間） |
| provider | varchar(255) | | 提供元・教育機関 |
| certificate_url | varchar(512) | | 証明書URL |
| expiry_date | date | | 有効期限（資格の場合） |
| cost | decimal(10,2) | | 費用 |
| reimbursed | boolean | DEFAULT false | 会社負担フラグ |
| required | boolean | DEFAULT false | 必須研修フラグ |
| approval_status | varchar(50) | DEFAULT 'approved' | 承認ステータス（pending/approved/rejected） |
| approver_id | uuid | | 承認者ID |
| feedback | text | | フィードバック |
| effectiveness_rating | integer | | 有効性評価（1-5） |
| notes | text | | 備考 |
| created_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード作成日時 |
| updated_at | timestamp | DEFAULT CURRENT_TIMESTAMP | レコード更新日時 |
| created_by | uuid | | 作成者ID |
| updated_by | uuid | | 更新者ID |

#### インデックス

| インデックス名 | フィールド | タイプ | 説明 |
|--------------|----------|------|------|
| learn_emp_idx | employee_id | B-tree | 従業員別検索の最適化 |
| learn_date_idx | completion_date | B-tree | 日付別検索の最適化 |
| learn_type_idx | activity_type | B-tree | タイプ別検索の最適化 |
| learn_required_idx | required | B-tree | 必須研修検索の最適化 |
| learn_approval_idx | approval_status | B-tree | 承認ステータス別検索の最適化 |

#### アクセス制御

- 読み取り: 本人、直属マネージャー、人材開発担当者、HR管理者
- 更新: 本人（自己申告部分）、人材開発担当者、HR管理者
- 作成: 本人、人材開発担当者、システム（自動記録）
- 削除: HR管理者のみ

#### データライフサイクル

- 保持期間: 従業員在籍中および退職後5年
- アーカイブポリシー: 5年経過後アーカイブ
- 必須研修記録: コンプライアンス要件に基づき長期保存

## 4. インデックス戦略

HRX-AIのデータベース設計では、以下のインデックス戦略を採用しています

1. **頻繁なフィルタリングフィールド**: 各テーブルの主要検索・フィルタリングフィールドにはB-treeインデックスを設定
2. **複合インデックス**: 頻繁に一緒に検索されるフィールド（例：従業員の姓名）には複合インデックスを設定
3. **外部キー**: すべての外部キーにはインデックスを適用し、結合クエリのパフォーマンスを最適化
4. **テキスト検索**: 大量テキストを検索するフィールドにはGINインデックス（PostgreSQL環境）を検討
5. **パーティショニング**: 大規模組織の場合、組織IDまたは部門IDによるテーブルパーティショニングを検討

インデックスの追加・削除は、実際の使用パターンに基づいて定期的に最適化します。

## 5. テーブル関連図

主要なテーブル関連は以下の通りです

1. Organization（1） → Department（多）: 組織は複数の部門を持つ
2. Department（1） → Employee（多）: 部門には複数の従業員が所属する
3. Employee（1） → Employee（多）: 従業員は管理者・被管理者の階層関係を持つ
4. Employee（1） → PerformanceRecord（多）: 従業員は複数の評価記録を持つ
5. Employee（1） → SkillProfile（多）: 従業員は複数のスキルプロファイルを持つ
6. Employee（1） → EmployeeEngagement（多）: 従業員は複数のエンゲージメント記録を持つ
7. Employee（1） → CompensationRecord（多）: 従業員は複数の報酬記録を持つ
8. Employee（1） → LearningActivity（多）: 従業員は複数の学習活動を記録する
9. Department（1） → RecruitmentCycle（多）: 部門は複数の採用サイクルを持つ
10. RecruitmentCycle（1） → Applicant（多）: 採用サイクルには複数の応募者がいる

## 6. データライフサイクル管理

HRX-AIのデータライフサイクル管理ポリシーは以下の原則に従います

1. **保持ポリシー**
   - 従業員データ: 退職後最低7年（法的要件に基づく）
   - 応募者データ: 採用サイクル終了後2年（不採用者は6ヶ月後に匿名化）
   - パフォーマンス記録: 従業員在籍中および退職後5年
   - 財務関連データ（報酬等）: 従業員退職後10年（税務・監査要件）

1. **アーカイブポリシー**
   - 活発に使用されないデータは、コスト効率とパフォーマンスのため低コストストレージへ移行
   - アーカイブデータへのアクセスは監査・法的要件に基づく場合に制限

1. **匿名化ポリシー**
   - 個人データは必要以上に長期保持しない
   - 長期分析用データは匿名化または仮名化
   - GDPR/CCPAに基づく削除要求に対応できる設計

1. **自動削除プロセス**
   - データ保持期間満了時の自動アーカイブ・削除ワークフロー
   - 削除前の通知と承認プロセス
   - すべての削除操作は監査ログに記録

## 7. データ暗号化戦略

重要な個人データとビジネス情報を保護するため、以下の暗号化戦略を実装します

1. **保存時暗号化**
   - 個人識別情報（PII）はAES-256暗号化
   - 給与・報酬データはフィールドレベルの暗号化
   - 医療関連データは特別な暗号化キーで保護

1. **転送時暗号化**
   - すべてのAPI通信にTLS 1.3を使用
   - バックアップデータ転送時の完全暗号化

1. **アクセス制御暗号化**
   - 役割ベースのアクセス制御（RBAC）
   - 特に機密性の高いデータには多要素認証要求

1. **鍵管理**
   - 定期的な暗号鍵のローテーション
   - HSM（Hardware Security Module）を使用した鍵保護
   - 分散鍵管理アーキテクチャ

1. **監査と検出**
   - 暗号化されたデータへのすべてのアクセスを記録
   - 異常アクセスパターンの自動検出

## 8. 変更履歴管理

データの整合性と監査証跡を確保するため、以下の変更履歴管理を実装します：

1. **テーブル変更履歴**
   - 各テーブルには`created_at`、`updated_at`、`created_by`、`updated_by`フィールド
   - 重要テーブルに対する変更は専用履歴テーブルに記録

1. **監査ログ**
   - すべての機密データアクセスと変更は監査ログに記録
   - ログには誰が、いつ、何を、なぜ変更したかを記録
   - ログは改ざん防止措置を実装

1. **バージョニング**
   - 重要ドキュメント（パフォーマンス評価など）には完全なバージョン履歴
   - 以前のバージョンへのロールバック機能

1. **変更承認ワークフロー**
   - 重要データ変更には承認ワークフロー
   - 変更理由のドキュメント化

## 9. アクセス制御ポリシー

データセキュリティとプライバシーを確保するため、以下のアクセス制御ポリシーを実装します

1. **役割ベースのアクセス制御（RBAC）**
   - システム管理者: すべてのテーブルへの完全アクセス
   - HR管理者: すべての人事データへのアクセス、機密データの変更権限
   - 部門マネージャー: 自部門の従業員データへの限定アクセス
   - 直属マネージャー: 直属部下のデータへの限定アクセス
   - 従業員: 自身のデータへの読取アクセス、一部更新権限
   - 採用担当者: 採用関連データのみにアクセス

1. **データ分離**
   - 組織間のデータ分離（マルチテナント環境）
   - 部門間のデータ分離（必要に応じて）

1. **最小権限の原則**
   - ユーザーは職務に必要な最小限のアクセス権限のみ付与
   - 臨時的な権限昇格には承認プロセスと期限設定

1. **アクセス監査**
   - すべての特権アクセスは記録・監視
   - 定期的なアクセス権限レビュー
   - 異常なアクセスパターンの検出・通知

これらのポリシーは組織の要件とコンプライアンス要件に基づいて調整可能です。
