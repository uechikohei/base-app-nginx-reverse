# base-app
構成：ruby2.6.5 / rails6.0.2 / nginx1.16(リバースプロキシ設定) / mysql8.0.16(イメージとして使用) / docker-compose 

# 使い方

`git clone git@github.com:uechikohei/base-app.git`

`docker-compose build`

`docker-compose up -d `

`docker-compose exec app bash`

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
  username: db
  password: root

```
### データベースを作成2

`docker-compose up -d`

`docker-compose exec app bash`

`rails db:create`

`rails db:migrate`

==>> http://localhost   アクセスすると、your are on rails!!になるはずです。
　　　検証でみれるが、nginxリバースプロキシが働いており
     http://localhost　  からhttp://localhost/3000 に通信されているのが確認出来ます。


# 備考

### bootstrap導入（yarnで）

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
・bootstrapをrailsで使用する設定。
app/javascript/packs/application.jsに記述する。

```
...
import "bootstrap"
import "../src/application"
```

・ディレクトリ とファイル作成
app/javascript配下に、srcというディレクトリ 作成。
src/配下に、appication.scssファイル作成。

app/javascript/src/application.scss に記述する。

```
@import "~bootstrap/scss/bootstrap";
```

・bootstrapを使用する
app/views/layouts/application.html.erbに記述。
<head></head>タグ内に、

```
<%= stylesheet_pack_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
<%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
```

