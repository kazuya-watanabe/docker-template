# 概要

フロントにNuxtJS。バックエンドにLaravelを使用したDocker用テンプレート。

NuxtJSのサーバーサイドレンダリングは考慮しない。

Apache HTTPDがエンドポイントとなり、NuxtJSの開発サーバーやphp-fpmにディスパッチする構造になっている。

開発サーバーの代わりにApacheで配信することも可能。

MariaDB、Redis、Postfixも使用可能。

Postfixはあらゆる宛先のメールを、単一のローカルユーザーのメールボックスに集約するブラックホールとして動作。

集約したメールはDovecotを通じてIMAPで確認することが可能。

# インストール

```bash
cd <workspace-root>
docker-compose build
# ...
# ...
# ...
mkdir -p frontend
docker-compose run -it --rm -v $(pwd)/frontend:/var/www/html nuxtjs yarn create nuxt-app .
# ...
# ...
# ...
mkdir -p backend
docker-compose run -it --rm -v $(pwd)/backend:/var/www/html php-fpm composer create-project laravel/laravel .
# ...
# ...
# ...
docker-compose create
```

# フロント開発

## ディレクトリ

- <workspace-root>/frontend
    - Nuxtのプロジェクトルート
    - コンテナ`nuxtjs`がマウントしている
- <workspace-root>/frontend/dist
    - ApacheのDocumentRoot
    - コンテナ`httpd`がマウントしている
    - `httpd`起動時に.developファイルが存在する場合は`nuxtjs`にディスパッチする

## 開発サーバーを使用する

JSやアセットの配信にNuxtを使用する場合はDocumentRootに.developファイルを配置しておく。
ファイルを配置したらコンテナ`httpd`を再起動する。

```bash
cd <workspace-root>
mkdir -p ./frontend/dist
touch ./frontend/dist/.develop
docker-compose restart httpd
```

## Apacheを使用する

DocumentRootにファイルを配置する。
JSやアセットの配信にApacheを使用する場合はDocumentRootの.developファイルを削除する。
削除したらコンテナ`httpd`を再起動する。

```bash
cd <workspace-root>
docker-compose run -it --rm -v $(pwd)/frontend:/var/www/html nuxtjs yarn build .
docker-compose run -it --rm -v $(pwd)/frontend:/var/www/html nuxtjs yarn generate .
rm -f ./frontend/dist/.develop
docker-compose restart httpd
```

# バックエンド開発

## URLマッピング

デフォルトでは.phpを含むリクエストと、以下のURLをコンテナ`php-fpm`にディスパッチしている。
- http://localhost/myapp
- http://localhost/api

カスタマイズするには<workspace-root>/httpd/proxy.confを編集する。
編集したらコンテナ`httpd`を再起動する。

```bash
docker-compose restart httpd
```

