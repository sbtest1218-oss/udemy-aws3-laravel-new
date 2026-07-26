FROM php:8.4-fpm-bookworm
SHELL ["/bin/bash", "-oeux", "pipefail", "-c"]

ENV COMPOSER_ALLOW_SUPERUSER=1 \
  COMPOSER_HOME=/composer \
  TZ=Asia/Tokyo

COPY --from=composer:2.10 /usr/bin/composer /usr/bin/composer

# PHP拡張とNode.jsを1レイヤーにまとめる（イメージサイズとビルド時間を削減）
# opcache: バイトコードキャッシュ。Laravelの実行速度に直結するため必須
# pcntl:   php artisan queue:work / pail が利用する
RUN apt-get update && \
  apt-get -y install --no-install-recommends \
    git unzip curl ca-certificates \
    libzip-dev libicu-dev libonig-dev \
    libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
  docker-php-ext-configure gd --with-freetype --with-jpeg && \
  docker-php-ext-install -j"$(nproc)" intl pdo_mysql zip bcmath gd opcache pcntl && \
  curl -fsSL https://deb.nodesource.com/setup_24.x | bash - && \
  apt-get install -y --no-install-recommends nodejs && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

COPY ./php.ini /usr/local/etc/php/php.ini

# アプリケーション用ユーザーを作成（UID/GIDを1000に設定）
# COMPOSER_HOME も appuser 所有にしないと composer のキャッシュが効かない
RUN groupadd -g 1000 appuser && \
    useradd -u 1000 -g appuser -m appuser && \
    mkdir -p /data /composer && \
    chown -R appuser:appuser /data /composer

USER appuser
WORKDIR /data
