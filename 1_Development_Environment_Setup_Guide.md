# 開発環境セットアップガイド (Firebase版)

## HRX-AI 開発環境構築手順

*バージョン: 1.0.0 | 最終更新日: 2025年5月14日*

このガイドは、HRX-AIプロジェクトの開発環境を構築するための包括的な手順を提供します。これらの手順に従うことで、チーム全体で一貫した開発体験を確保できます。

## 目次

1. [システム要件](#1-システム要件)
2. [開発ツールのインストール](#2-開発ツールのインストール)
3. [リポジトリのセットアップ](#3-リポジトリのセットアップ)
4. [環境設定](#4-環境設定)
5. [フロントエンド環境構築](#5-フロントエンド環境構築)
6. [バックエンド環境構築](#6-バックエンド環境構築)
7. [データベースのセットアップ](#7-データベースのセットアップ)
8. [AI/ML環境の構成](#8-aiml環境構成)
9. [開発サーバーの実行](#9-開発サーバーの実行)
10. [開発ワークフロー](#10-開発ワークフロー)
11. [トラブルシューティング](#11-トラブルシューティング)

## 1. システム要件

### 最小ハードウェア要件
- **CPU**: 4コア以上（AI モデル推論には8コア以上を推奨）
- **メモリ**: 16GB 以上（32GB 推奨）
- **ディスク**: 20GB 以上の空き容量（SSD 推奨）
- **GPU**: オプション（ローカルでのAIモデルトレーニング/推論に推奨）

### 対応OS
- **macOS**: 12.0（Monterey）以降
- **Linux**: Ubuntu 20.04 以降または同等のディストリビューション
- **Windows**: Windows 10/11 + WSL2（最適な互換性のため）

## 2. 開発ツールのインストール

### 必須ツール
- **Git** (2.30 以上)
- **Docker** (24.0 以上) および Docker Compose (2.20 以上)
- **Node.js** (18.17 以上 LTS)
- **Python** (3.11 以上)
- **VS Code**（推奨）と推奨拡張機能

### OSごとのインストール手順

#### macOS
```bash
# Homebrew がインストールされていない場合はインストール
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 必要なツールをインストール
brew install git node@18 python@3.11 docker docker-compose

# NVMのインストール（Node.jsのバージョン管理用、推奨）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
nvm install 18
nvm use 18

# Visual Studio Codeのインストール
brew install --cask visual-studio-code
```

#### Ubuntu/Debian Linux
```bash
# パッケージリストの更新
sudo apt update

# Gitのインストール
sudo apt install -y git

# Node.js 18.xのインストール
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Python 3.11のインストール
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install -y python3.11 python3.11-venv python3.11-dev

# Dockerのインストール
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# Docker Composeのインストール
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Visual Studio Codeのインストール
sudo snap install code --classic
```

#### WSL2を使用したWindows
1. [Microsoftの公式ガイド](https://docs.microsoft.com/ja-jp/windows/wsl/install)に従ってWSL2をインストール
2. Microsoft StoreからUbuntu 20.04をインストール
3. WSL2環境内で上記のUbuntu/Debian Linuxの手順に従う
4. WindowsにVS Codeをインストールし、「Remote - WSL」拡張機能を追加

### 推奨VS Code拡張機能
以下の拡張機能をインストールして最適な開発体験を実現します

```bash
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension ms-python.python
code --install-extension ms-python.vscode-pylance
code --install-extension ms-azuretools.vscode-docker
code --install-extension bradlc.vscode-tailwindcss
code --install-extension mikestead.dotenv
code --install-extension github.copilot
code --install-extension ms-vscode.vscode-typescript-next
code --install-extension prisma.prisma
```

## 3. リポジトリのセットアップ

### リポジトリのクローン
```bash
git clone https://github.com/yf591/hrx-ai.git
cd hrx-ai
```

### プロジェクト構成
リポジトリはモノレポ構造を採用しています
```
hrx-ai/
├── frontend/         # Next.jsアプリケーション
├── backend/          # FastAPIアプリケーション
├── ai-models/        # AIモデルのトレーニングと推論コード
├── docs/             # ドキュメント
└── scripts/          # 開発・ビルドスクリプト
```

### Git設定
コード品質のためのGitフックを設定

```bash
# Huskyのインストール（Gitフック用）
npm install -g husky
npx husky install
npx husky add .husky/pre-commit "npm run lint && npm run test:ci"
```

## 4. 環境設定

### 環境変数ファイルの作成
提供されているテンプレートを基に環境ファイルを作成します

```bash
# フロントエンド環境
cp frontend/.env.example frontend/.env.local

# バックエンド環境
cp backend/.env.example backend/.env

# 開発環境
cp .env.example .env
```

### 環境変数の設定
作成した`.env`ファイルをローカル開発設定で編集します

**frontend/.env.local**:
```
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_FIREBASE_API_KEY=あなたのfirebase_api_key
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=あなたのプロジェクト.firebaseapp.com
NEXT_PUBLIC_FIREBASE_PROJECT_ID=あなたのプロジェクトID
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=あなたのプロジェクト.appspot.com
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=あなたのmessaging_sender_id
NEXT_PUBLIC_FIREBASE_APP_ID=あなたのfirebase_app_id
NEXT_PUBLIC_AI_ASSISTANT_ENABLED=true
```

**backend/.env**:
```
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/hrxai
OPENAI_API_KEY=あなたのopenai_key
JWT_SECRET=開発用のjwt_secret
FIREBASE_SERVICE_ACCOUNT_KEY_PATH=./firebase-service-account.json
LOG_LEVEL=debug
ENVIRONMENT=development
```

### APIキーの設定
外部サービス（OpenAI、Firebaseなど）のAPIキーを取得して環境変数に追加します

1. [OpenAI APIキー](https://platform.openai.com/)の取得
2. [Firebase](https://console.firebase.google.com/)でプロジェクトを作成し、以下を実施:
   - Webアプリを追加し、設定値を取得
   - Firebase Admin SDK用のサービスアカウントキーをJSON形式でダウンロード
   - JSONファイルをbackendディレクトリに`firebase-service-account.json`として保存
3. 必要に応じて他のサービスを設定

## 5. フロントエンド環境構築

### 依存関係のインストール
```bash
cd frontend
npm install
npm install firebase react-firebase-hooks
```

### コード品質ツールの設定
```bash
# ESLintとPrettierのセットアップ
npm run lint:init
```

### ディレクトリ構造
```
frontend/
├── app/             # Next.js App Routerのページとレイアウト
├── components/      # 再利用可能なReactコンポーネント
├── hooks/           # カスタムReactフック
├── lib/             # ユーティリティ関数と共有ロジック
│   └── firebase.ts  # Firebase初期化とユーティリティ
├── public/          # 静的アセット
└── styles/          # グローバルスタイルとTailwind設定
```

### Tailwind CSS設定
プロジェクトではカスタム設定のTailwindを使用しています

```bash
# Tailwindタイプの生成（オプションですが便利）
npm run tailwind:types
```

## 6. バックエンド環境構築

### Python仮想環境の作成
```bash
cd backend
python -m venv venv

# 仮想環境のアクティベート
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate
```

### Pythonパッケージのインストール
```bash
pip install -r requirements.txt
pip install -r requirements-dev.txt  # 開発用依存関係
pip install firebase-admin  # Firebase Admin SDKをインストール
```

### ディレクトリ構造
```
backend/
├── app/             # メインアプリケーションパッケージ
│   ├── api/         # APIエンドポイント
│   ├── core/        # コア機能
│   ├── db/          # データベースモデル
│   ├── firebase/    # Firebase連携機能
│   └── services/    # ビジネスロジックサービス
├── tests/           # ユニットとインテグレーションテスト
├── firebase-service-account.json  # Firebaseサービスアカウントキー
└── scripts/         # バックエンド固有のスクリプト
```

### pre-commitフックの設定
```bash
pre-commit install
```

## 7. データベースのセットアップ

### DockerによるローカルPostgreSQL
```bash
# PostgreSQLとpgAdminコンテナを起動
docker compose up -d postgres pgadmin

# 開発用データベースの作成
docker exec -it hrxai-postgres createdb -U postgres hrxai
```

### Firebaseエミュレーターのセットアップ
```bash
# Firebase CLIのインストール
npm install -g firebase-tools

# Firebaseにログイン
firebase login

# Firebaseエミュレーターの起動
firebase emulators:start
```

### データベースマイグレーション
```bash
cd backend
alembic upgrade head
```

### 開発用サンプルデータ
```bash
python scripts/seed_data.py
```

### ベクトルデータベースのセットアップ
```bash
docker compose up -d weaviate
```

## 8. AI/ML環境構成

### LangChain環境のセットアップ
```bash
cd ai-models
python -m venv venv
source venv/bin/activate  # Windowsの場合: venv\Scripts\activate
pip install -r requirements.txt
```

### モデルアクセスの設定
モデルプロバイダー設定で`ai-models/.env`を編集します

```
OPENAI_API_KEY=あなたのopenai_key
OPENAI_ORG_ID=あなたの組織ID  # 該当する場合
ANTHROPIC_API_KEY=あなたのanthropic_key  # Claudeを使用する場合
HUGGINGFACE_API_KEY=あなたのhuggingface_key  # HFモデルを使用する場合
```

### 埋め込みモデルのセットアップ
```bash
# オフライン使用用の埋め込みモデルをダウンロード（オプション）
python scripts/download_embedding_models.py
```

## 9. 開発サーバーの実行

### Docker Composeの使用（推奨）
```bash
# 全サービスの起動
docker compose up

# 特定のサービスの起動
docker compose up frontend backend
```

### 手動起動

#### フロントエンド開発サーバー
```bash
cd frontend
npm run dev
```
フロントエンドは http://localhost:3000 で利用可能になります

#### バックエンド開発サーバー
```bash
cd backend
uvicorn app.main:app --reload --port 8000
```
バックエンドは http://localhost:8000 で利用可能になります

#### APIドキュメント
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

#### Firebaseエミュレーター
Firebaseエミュレーターの管理UIは http://localhost:4000 で利用可能になります

## 10. 開発ワークフロー

### ブランチ戦略
トランクベース開発アプローチを採用し、フィーチャーブランチを使用します：

1. メインからフィーチャーブランチを作成 
   ```bash
   git checkout -b feature/機能名
   ```
2. 変更を加え、頻繁にコミットする
3. メインから最新の変更を取得し、コンフリクトを解決
   ```bash
   git fetch origin
   git rebase origin/main
   ```
4. ブランチをプッシュしてプルリクエストを作成
5. CIチェックが通過し、コードレビューの承認を得る
6. メインにマージ

### コードスタイル
- **フロントエンド**: プロジェクト設定に従ってESLintとPrettierを使用
- **バックエンド**: Python用にFlake8、Black、isortを使用

コードスタイルチェックの実行
```bash
# フロントエンド
cd frontend
npm run lint

# バックエンド
cd backend
flake8 app tests
black app tests --check
isort app tests --check-only
```

### テスト

#### フロントエンドテスト
```bash
cd frontend
npm run test       # Jestテストの実行
npm run test:watch # 監視モード
npm run e2e        # Playwrightエンドツーエンドテストの実行
```

#### バックエンドテスト
```bash
cd backend
pytest             # 全テストを実行
pytest tests/api/  # APIテストのみ実行
pytest -xvs        # 詳細モード
```

## 11. トラブルシューティング

### よくある問題

#### Dockerの権限問題
Linuxでのdocker権限問題に遭遇した場合
```bash
sudo usermod -aG docker $USER
newgrp docker
```

#### Node.jsバージョンの不一致
Node.jsバージョンの問題が発生した場合
```bash
nvm install 18
nvm use 18
```

#### FastAPIサーバーが起動しない
環境変数とデータベース接続が機能していることを確認します
```bash
cd backend
python scripts/check_db_connection.py
```

#### Firebaseエミュレーターの問題
Firebaseエミュレーターに問題がある場合
```bash
firebase emulators:stop
firebase emulators:start --only auth,firestore,storage
```

#### OpenAI API問題
AI機能が失敗する場合、APIキーを確認してデバッグモードを設定します
```
OPENAI_API_KEY=あなたのキー
AI_DEBUG_MODE=true
```

### サポートの取得方法
- `/docs`ディレクトリ内の内部ドキュメントを確認
- 類似問題のGitHub Issuesを確認
- Slackの#hrxai-devチャンネルで開発チームに連絡

---

## 追加リソース

- [Next.jsドキュメント](https://nextjs.org/docs)
- [FastAPIドキュメント](https://fastapi.tiangolo.com/)
- [LangChainドキュメント](https://python.langchain.com/docs/)
- [Firebase ドキュメント](https://firebase.google.com/docs)

---

*この設定ガイドはHRX-AI開発チームによって管理されています。*
