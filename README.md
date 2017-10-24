Docker for Mac で Ruby on Rails アプリの開発環境

## Railsアプリ用のディレクトリを作成
```
mkdir -p MyDocker/MyRails
cd ~/MyDocker/MyRails
git clone https://github.com/nuovotaka/docker-rails.git
```
docker-rails ディレクトリ内のファイル群をRailsアプリ用のディレクトリ内にコピーする。

## コンテナにRailsアプリを作成
「docker-compose run」コマンドで「web」コンテナに「rails new」を行う。
```
$ docker-compose run --rm web rails new . --force --database=mysql --skip-bundle
```
実行後、app_nameディレクトリの中にRailsのファイル群が作成されている

## Gemfileの変更
therubyracerの行のコメントを外す

```
gem 'therubyracer', platforms: :ruby
```
Gemfileの変更を適用するためには以下のコマンドを実行
```
docker-compose build
```
## config/database.ymlの設定
```
default: &default
  adapter: mysql2
  encoding: utf8
  pool: 5
  username: root
  password: <%= ENV['MYSQL_ROOT_PASSWORD'] %>
  host: db

```

## コンテナの実行＆ブラウザの確認
最初はデータベースを作成する（次回からは必要ない）
```
docker-compose run --rm web rake db:create
```

### コンテナの実行
```
docker-compose up
```

### コンテナの確認
```
docker-compose ps
```

localhost:3000 でRailsのウェルカム画面が表示されるのがわかると思う。

## Rails アプリを作成する方法
例　メモアプリの作成
別タブで以下のコマンドを実行する
```
docker-compose run web rails g scaffold Memo title:string body:text
```

基本的にRailsコマンドの最初に「docker-compose run web」をつけてやればコンテナ上で実行され、Mac側にも反映されます。

### アプリの確認
localhost:3000/memos でアクセスするとアプリが作成されているのがわかる。

## 注意点
Gemfileを変更したら、buildでイメージ作成してから、コンテナ実行すること
```
$ docker-compose build
$ docker-compose up
```

## コンテナの停止と削除
Dockerに付随してくるkitematicで停止と削除を行う

## コンテナのイメージの削除
Dockerに付随してくるkitematicでMy Imagesからイメージの削除を行う

## 再起動
古いプロセスの削除
```
rm -rf tmp/pids/server.pid
```
そしてupする
```
docker-compose up
```
