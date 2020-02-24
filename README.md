# base-app
構成：ruby2.6.5 / rails6.0.2 / nginx1.16(リバースプロキシ設定) / mysql8.0.16(イメージとして使用) / docker-compose 

# 使い方

`git clone git@github.com:uechikohei/base-app-nginx-reverse.git`

`docker-compose build`

`docker-compose run app bash`

`rails new . --force --database=mysql`

### データベースを作成
database.ymlに、コピペ↓

```
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: root
  host: db

development:
  <<: *default
  database: app_development

test:
  <<: *default
  database: app_test

production:
  <<: *default
  database: app_production
  username: root
  password: root

```

## データベースを作成

`docker-compose down`

`docker-compose build`

`docker-compose run app bash`

`rails db:create`

`rails db:migrate`

`docker-compose up`

==>> http://localhost   アクセスすると、your are on rails!!になるはずです。
　　　nginxリバースプロキシが働いており
     http://localhostからhttp://localhost/3000 に通信されているのが確認出来ます。


## モデルやビューの作成

`docker-compose up -d`

`docker-compose exec app bash`

作成
`rails generate controller home index`
　　取り消す場合
　　`rails destroy controller home index`


## bootstrap導入（yarnで）

コンテナ内に、webpackerインストール

`docker-compose run app bash`

`rails webpacker:install` 

Gemfileではなく、yarnパッケージでインストール。

`yarn add bootstrap jquery popper.js`

・ webpackerで管理するパターン
 config/webpack/environment.js　に記述する。
 
```
const { environment } = require('@rails/webpacker')

const webpack = require('webpack')

environment.plugins.append('Provide', new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery',
  Popper: ['popper.js', 'default']
}))

module.exports = environment
```


app/javascript/packs/application.jsに記述する。

```
...
import "bootstrap"
import "../src/application"
```

app/javascript配下に、`src`というディレクトリ 作成。

`src/`配下に、`appication.scss`ファイル作成。

`app/javascript/src/application.scss` に記述する。

```
@import "~bootstrap/scss/bootstrap";
```

・bootstrapを使用する
`app/views/layouts/application.html.erb`に記述。
<head></head>タグ内に、

```
<%= stylesheet_pack_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
```

### docker-compose.ymlで起動するwebpackerコンテナと、.env.dockerで開発環境でのwebpacker起動と連携を制御。

```
  webpacker:
    build:
      context: .
      dockerfile: ./docker/app/rails_Dockerfile
    env_file:
      - '.env.docker'
    command: ./bin/webpack-dev-server
    volumes:
      - .:/webpacker-example-app
    ports:
      - '3035:3035'
```

```
NODE_ENV=development
RAILS_ENV=development
WEBPACKER_DEV_SERVER_HOST=0.0.0.0
```
