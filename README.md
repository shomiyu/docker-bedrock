# WordPress Loacl env with Docker/Bedrock

This base repository is [docker-nginx-php-mysql](https://github.com/nanoninja/docker-nginx-php-mysql).

このリポジトリは下記の環境で展開します。

```
- Docker
- Nginx
- PHP
- Composer
- MySQL
- PHPMyAdmin
- Bedrock
```

## 依存関係

起動に以下を必要とします。

```
- Composer 2.X 以上
- Docker
- docker-compose
```

## 使用イメージ

使用している docker イメージは下記です。

- [Nginx](https://hub.docker.com/_/nginx/)
- [MySQL](https://hub.docker.com/_/mysql/)
- [PHP-FPM](https://hub.docker.com/r/nanoninja/php-fpm/)
- [Composer](https://hub.docker.com/_/composer/)
- [PHPMyAdmin](https://hub.docker.com/r/phpmyadmin/phpmyadmin/)
- [Generate Certificate](https://hub.docker.com/r/jacoelho/generate-certificate/)

## 使用ポート

使用しているポートは下記です。

| Server     | Port |
| ---------- | ---- |
| MySQL      | 8989 |
| PHPMyAdmin | 8080 |
| Nginx      | 8000 |
| Nginx SSL  | 3000 |

## セットアップ

### 依存関係の確認

#### 1. Composer

バージョンが 2 系以上であることを確認してください。1 系だとデータベースの接続に失敗します。

```sh
composer --version
// Composer version 2.0.13 2021-04-27 13:11:08
```

バージョンが古い場合は下記のコマンドで最新にアップデートします。

```sh
composer self-update
```

##### Composer を 1 系のままで使用したい場合

1 系のままで使用したい場合は site ディレクトリを削除して、下記のコマンドで Bedrock の環境を再生成します。

```sh
composer create-project roots/bedrock site
```

インストールが完了すると`/site/.env`が生成されているので、後述する[環境変数の設定](#環境変数の作成)を行います。

#### 2. Docker

Docker を起動し、インストールされていることを確認します。

```sh
docker -v
// Docker version 19.03.4, build 9013bf5
```

#### 3. docker-compose

docker-compose がインストールされていることを確認します。

```sh
docker-compose -v
// Docker version 19.03.4, build 9013bf5
```

### アプリインストール

下記のコマンドに従ってリポジトリをクローンします。

```sh
git clone https://github.com/shomiyu/docker-bedrock.git
```

クローンができたらディレクトリに移動します。

```sh
cd docker-bedrock
```

### 環境変数の作成

`.env`ファイルを作成します。
※`MYSQL_HOST`は`docker-compose.yml`の`mysqldb` > `container_name`に合わせる必要があります。

```sh
#!/usr/bin/env bash

# See https://docs.docker.com/compose/environment-variables/#the-env-file

# Nginx
NGINX_HOST=localhost

# PHP

# See https://hub.docker.com/r/nanoninja/php-fpm/tags/
PHP_VERSION=latest

# MySQL
MYSQL_VERSION=5.7.22
MYSQL_HOST=mysqldb
MYSQL_DATABASE=test
MYSQL_ROOT_USER=root
MYSQL_ROOT_PASSWORD=root
MYSQL_USER=dev
MYSQL_PASSWORD=dev
```

`/site/.env`を作成します。
※`DB_HOST`は`docker-compose.yml`の`mysqldb` > `container_name`に合わせる必要があります。

`AUTH_KEY`以降の値は[https://roots.io/salts.html](https://roots.io/salts.html)にアクセスして生成し、書き換えます。

```sh
DB_NAME=test
DB_USER=dev
DB_PASSWORD=dev

# Optional variables
DB_HOST=mysqldb
# DB_PREFIX=wp_

WP_ENV=development
WP_HOME=http://localhost:8000
WP_SITEURL=${WP_HOME}/wp

# Generate your keys here: https://roots.io/salts.html
AUTH_KEY='generateme'
SECURE_AUTH_KEY='generateme'
LOGGED_IN_KEY='generateme'
NONCE_KEY='generateme'
AUTH_SALT='generateme'
SECURE_AUTH_SALT='generateme'
LOGGED_IN_SALT='generateme'
NONCE_SALT='generateme'
```

### アプリケーション立ち上げ

以下のコマンドでプロジェクトを立ち上げます。
※`docker-compose.yml`のある階層で実行する必要があります。

```sh
docker-compose up
```

もしくは

```sh
docker-compose up -d
```

以下の URL でアクセスできます。

- http://localhost:8000

WordPress のインストール画面が表示されれば OK です。

### アプリケーションのストップ

下記のコマンドでアプリを停止します。

```sh
docker-compose down -v
```

## ２サイト目以降の場合

このリポジトリを使用して２サイト目以降の環境を作成する場合は、コンテナの重複を防ぐために `docker-compose.yml`の`myadmin` > `container_name` と `mysqldb` > `container_name`を変更する必要があります。

## WordPress での運用

Bedrock に従って WordPress の運用には下記の注意点があります。

- プラグインのインストールは Composer から行う
