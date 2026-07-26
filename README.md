# Laravel TDD CICD

![Run Tests](https://github.com/YOUR_USERNAME/YOUR_REPO/workflows/Run%20Tests/badge.svg)

## 概要
TDDで開発したTodoアプリです。
CI/CDでテストを自動化しています。

# Laravel Docker Environment

- Udemyのフルスタックエンジニアになるためのスキルの学び方を学習するためのレポジトリです
- PHP 8.4 + nginx + MySQL を使用した Laravel プロジェクト用の Docker 環境です。

## 構成

- **Laravel**: 13.x（PHP 8.3 以上が必須）
- **PHP**: 8.4-fpm (Composer 2.10 / Node.js 24 / OPcache 含む)
- **Web Server**: nginx 1.30-alpine
- **Database**: MySQL 8.4 (LTS)
- **Port**: 8086 (HTTP), 3386 (MySQL), 5173 (Vite)
  - MySQL と Vite は `127.0.0.1` にのみバインドしており、外部からは接続できません
  - 本番では `.env` に `WEB_PORT=80` を指定して起動します

## 使い方

Laravel アプリは `src/` にすでに入っています。`composer create-project` は不要です。

### 1. 環境変数の設定（Docker 用）

```bash
cp .env.example .env
```

### 2. 開発環境の起動

```bash
docker compose up -d
```

### 3. Laravel のセットアップ

`vendor/` と `.env` は Git 管理外のため、初回だけ以下を実行します。

```bash
docker compose exec app bash

composer install
cp .env.example .env
php artisan key:generate
php artisan migrate

exit
```

### 4. アクセス

- アプリケーション: http://localhost:8086
- ヘルスチェック: http://localhost:8086/up

### 5. テストの実行

```bash
docker compose exec app php artisan test
```

## よく使うコマンド

```bash
# コンテナの状況確認
docker-compose ps

# ログの確認
docker-compose logs -f

# PHPコンテナに入る
docker-compose exec app bash

# Composer実行
docker-compose exec app composer install

# Artisanコマンド実行
docker-compose exec app php artisan migrate

# 環境の停止
docker-compose down

# 環境の完全削除（データベースも削除）
docker-compose down -v
```

## 本番環境

本番環境では以下を想定：

- Aurora MySQL（ローカルの MySQL は使用しないケースを想定）

```bash
# 本番環境での起動
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

## ディレクトリ構成

```
.
├── infra/
│   ├── mysql/          # MySQL設定
│   ├── nginx/          # nginx設定
│   └── php/            # PHP設定
├── src/                # Laravelプロジェクトを配置
├── docker-compose.yml  # 開発環境設定
├── docker-compose.prod.yml # 本番環境設定
└── .env.example        # 環境変数テンプレート
```

## 注意事項

- `src/` ディレクトリは空の状態です
- 本番環境では CloudFront と ACM を使用することを推奨
- データベースの永続化は `db-store` ボリュームで行われます

## tailwind CSS ver4 を使用する場合

vite.config.js を下記のコードで 5173 ポートを Docker のコンテナの外からもアクセスできるように開ける

```js
import { defineConfig } from "vite";
import laravel from "laravel-vite-plugin";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [
    laravel({
      input: ["resources/css/app.css", "resources/js/app.js"],
      refresh: true,
    }),
    tailwindcss(),
  ],
  server: {
    host: "0.0.0.0",
    port: 5173,
    strictPort: true,
    hmr: {
      host: "localhost",
      port: 5173,
    },
  },
});
```
