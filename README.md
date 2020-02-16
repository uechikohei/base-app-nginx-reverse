# base-app
構成：ruby2.6.5 / rails6.0.2 / nginx1.16(リバースプロキシ設定) / mysql8.0.16(イメージとして使用) / docker-compose 

# 使い方

`docker-compose build`

`docker-compose up -d `

`docker-compose exec app bash`

`rails new . --force --database=mysql`

### database.ymlを変更

=>

### CRUD実装

=>

### bootstrap導入

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
