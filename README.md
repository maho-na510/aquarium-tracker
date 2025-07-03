# 水族館訪問記録アプリ

日本全国の水族館を訪問した記録を管理できるWebアプリケーションです。

## 🌟 主な機能

- **インタラクティブ地図**: 日本地図上で水族館の位置を確認
- **訪問記録**: 写真付きで訪問記録を保存
- **評価・レビュー**: 各水族館の評価とレビューを投稿
- **行きたいリスト**: 未訪問の水族館をウィッシュリストに追加
- **ランキング**: 人気水族館、高評価水族館のランキング表示
- **統計ダッシュボード**: 訪問状況の可視化

## 🛠 技術スタック

### Backend
- Ruby on Rails 7.0 (API モード)
- MySQL 8.0
- Devise (認証)
- Active Storage (画像管理)
- Geocoder (位置情報)

### Frontend
- React 18
- Material-UI
- React Leaflet (地図表示)
- React Query (状態管理)
- React Hook Form (フォーム管理)
- Recharts (グラフ表示)

### Infrastructure
- Docker & Docker Compose

## 🚀 セットアップ

### 前提条件
- Docker Desktop
- Git

### 1. リポジトリのクローン
```bash
git clone <repository-url>
cd aquarium-tracker
```

### 2. プロジェクト構造の作成
```
aquarium-tracker/
├── docker-compose.yml
├── backend/
│   ├── Dockerfile
│   ├── Gemfile
│   └── (Rails アプリケーション)
└── frontend/
    ├── Dockerfile
    ├── package.json
    └── (React アプリケーション)
```

### 3. Rails アプリケーションの初期化

```bash
# backend ディレクトリでRailsアプリを作成
cd backend
rails new . --api --database=mysql --skip-git
```

### 4. 環境変数の設定

`backend/.env` ファイルを作成:
```env
DATABASE_HOST=db
DATABASE_USERNAME=rails
DATABASE_PASSWORD=password
DATABASE_NAME=aquarium_tracker_development
JWT_SECRET_KEY=your_jwt_secret_key
```

### 5. Docker環境の起動

```bash
# プロジェクトルートで実行
docker-compose up --build
```

### 6. データベースの初期化

```bash
# 別のターミナルで実行
docker-compose exec backend rails db:create
docker-compose exec backend rails db:migrate
docker-compose exec backend rails db:seed
```

## 📊 データベース設計

### 主要テーブル

#### users
- id, email, encrypted_password, name, bio
- 認証情報とプロフィール

#### aquariums
- id, name, description, prefecture, address, latitude, longitude
- 水族館の基本情報と位置情報

#### visits
- id, user_id, aquarium_id, visited_at, rating, review, memo
- 訪問記録

#### wish_lists
- id, user_id, aquarium_id, memo, priority
- 行きたいリスト

### リレーション
- User has_many Visits, WishLists
- Aquarium has_many Visits, WishLists
- Visit belongs_to User, Aquarium

## 🔧 開発

### バックエンド開発

```bash
# Rails コンソール
docker-compose exec backend rails console

# テスト実行
docker-compose exec backend bundle exec rspec

# マイグレーション
docker-compose exec backend rails db:migrate
```

### フロントエンド開発

```bash
# npmコマンド実行
docker-compose exec frontend npm install
docker-compose exec frontend npm test
```

## 📱 主要画面

1. **ダッシュボード** (`/dashboard`)
   - 訪問統計の表示
   - 最近の訪問記録
   - 地域別訪問状況グラフ

2. **地図画面** (`/map`)
   - 日本地図上の水族館表示
   - 訪問済み/未訪問/行きたいリストの色分け
   - 水族館詳細情報のポップアップ

3. **訪問記録** (`/visits`)
   - 訪問記録の一覧表示
   - 新規訪問記録の追加
   - 写真・評価・レビューの管理

4. **行きたいリスト** (`/wishlist`)
   - 未訪問水族館のウィッシュリスト
   - 優先度設定
   - メモ機能

5. **ランキング** (`/rankings`)
   - 人気水族館ランキング
   - 高評価水族館ランキング
   - 都道府県別統計

## 🎯 API エンドポイント

### 認証
- `POST /api/v1/auth/sign_in` - ログイン
- `POST /api/v1/auth/sign_up` - ユーザー登録
- `DELETE /api/v1/auth/sign_out` - ログアウト

### 水族館
- `GET /api/v1/aquariums` - 水族館一覧
- `GET /api/v1/aquariums/:id` - 水族館詳細
- `GET /api/v1/aquariums/map_data` - 地図用データ

### 訪問記録
- `GET /api/v1/visits` - 訪問記録一覧
- `POST /api/v1/visits` - 訪問記録作成
- `PUT /api/v1/visits/:id` - 訪問記録更新
- `DELETE /api/v1/visits/:id` - 訪問記録削除
- `GET /api/v1/visits/stats` - 統計情報

### 行きたいリスト
- `GET /api/v1/wish_lists` - ウィッシュリスト取得
- `POST /api/v1/wish_lists` - リストに追加
- `PUT /api/v1/wish_lists/:id` - リスト更新
- `DELETE /api/v1/wish_lists/:id` - リストから削除

### ランキング
- `GET /api/v1/rankings/popular_aquariums` - 人気ランキング
- `GET /api/v1/rankings/top_rated_aquariums` - 高評価ランキング
- `GET /api/v1/rankings/prefecture_rankings` - 都道府県別ランキング

## 🌐 本番環境デプロイ

### Docker Compose (本番用)

```yaml
# docker-compose.prod.yml
version: '3.8'
services:
  db:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_DATABASE: aquarium_tracker_production
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.prod
    environment:
      RAILS_ENV: production
      DATABASE_URL: mysql2://${DB_USER}:${DB_PASSWORD}@db/aquarium_tracker_production
      SECRET_KEY_BASE: ${SECRET_KEY_BASE}
    depends_on:
      - db

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.prod
    environment:
      REACT_APP_API_URL: ${API_URL}

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - backend
      - frontend

volumes:
  mysql_data:
```

### 環境変数設定

```bash
# .env.production
DB_ROOT_PASSWORD=secure_root_password
DB_USER=rails_user
DB_PASSWORD=secure_password
SECRET_KEY_BASE=your_secret_key_base
API_URL=https://your-domain.com
```

## 🗾 初期データセット

アプリケーションには日本全国の主要な水族館データが含まれています：

```ruby
# db/seeds.rb の例
aquariums = [
  {
    name: "沖縄美ら海水族館",
    prefecture: "沖縄県",
    address: "沖縄県国頭郡本部町石川424",
    latitude: 26.6939,
    longitude: 127.8783
  },
  {
    name: "すみだ水族館",
    prefecture: "東京都", 
    address: "東京都墨田区押上1-1-2",
    latitude: 35.7101,
    longitude: 139.8107
  },
  # ... 他の水族館データ
]
```

## 🧪 テスト

### バックエンドテスト

```bash
# RSpec実行
docker-compose exec backend bundle exec rspec

# 特定のテスト実行
docker-compose exec backend bundle exec rspec spec/models/user_spec.rb
```

### フロントエンドテスト

```bash
# Jest テスト実行
docker-compose exec frontend npm test

# カバレッジレポート
docker-compose exec frontend npm run test:coverage
```

## 📈 パフォーマンス最適化

### バックエンド
- データベースインデックスの最適化
- N+1クエリの解決（includes使用）
- Active Storage での画像最適化
- Redis キャッシュの実装

### フロントエンド
- React.memo でのコンポーネント最適化
- React Query でのデータキャッシュ
- 画像の遅延読み込み
- Bundle サイズの最適化

## 🔒 セキュリティ

- JWT トークンベース認証
- CORS 設定
- SQL インジェクション対策
- XSS 対策
- ファイルアップロードの検証

## 🤝 コントリビューション

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 ライセンス

このプロジェクトは MIT ライセンスの下で公開されています。

## 🐛 トラブルシューティング

### よくある問題

1. **Docker起動エラー**
   ```bash
   docker-compose down -v
   docker-compose up --build
   ```

2. **データベース接続エラー**
   ```bash
   docker-compose exec backend rails db:reset
   ```

3. **フロントエンド依存関係エラー**
   ```bash
   docker-compose exec frontend rm -rf node_modules
   docker-compose exec frontend npm install
   ```

## 📞 サポート

問題や質問がある場合は、以下の方法でお問い合わせください：

- GitHub Issues
- Email: support@aquarium-tracker.com

---

🐠 Happy Aquarium Tracking! 🐠