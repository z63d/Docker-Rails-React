## step 1

コンテナ群をビルドする。

`$ docker-compose build`

## step2

下記コマンドも動作するが、
`docker-compose run --rm front sh -c "npm install -g create-react-app && create-react-app reactapp"`

https://zenn.dev/ryuu/articles/what-npxcommand

npx に変えた方が綺麗。
`$ docker-compose run --rm front npx create-react-app reactapp`
`$ docker-compose run --rm front npx create-react-app reactapp --template typescript`

https://zenn.dev/taku1115/articles/6c9fa97ab37e38

next.js の場合は以下なので、
`docker-compose run --rm front yarn create next-app .`

`docker-compose run --rm front`, `npx create-react-app reactapp` 分けて考えるとクリアになる。

## step3

https://qiita.com/yoshiokaCB/items/7d748d4c059438b9d1f2#docker-run-%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%AE%E8%B5%B7%E5%8B%95

https://qiita.com/hoshino/items/9545d255cc0103b3d296

`--rm` 付けた方が無駄な生成をせずに済む。

`$ docker-compose run --rm api rails new . --force --no-deps --api --database=mysql`

## step4

上記が終了次第、database.yml が生成され、このタイミングで編集できるようになった。

default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: password #追記
  host: db #追記

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myapp_production
  username: myapp
  password: <%= ENV['MYAPP_DATABASE_PASSWORD'] %>

## step5

コンテナ群を起動する

`docker-compose up --build`

## step6

以下のようなメッセージが出れば、api コンテナが起動された。

```
api_1    | * Listening on http://0.0.0.0:3000
api_1    | Use Ctrl-C to stop
```

このタイミングで順次、以下を実行する。

`docker-compose run --rm api rails db:create`

`docker-compose run --rm api rails db:migrate`

これでエラーなく動作できる。


## react バージョン
17に戻すには
`docker-compose run --rm -w /usr/src/app/reactapp front sh -c "npm i react@17.0.2 react-dom@17.0.2"`

## WebSocket error
cd reactapp
echo "WDS_SOCKET_PORT=0" >> .env
