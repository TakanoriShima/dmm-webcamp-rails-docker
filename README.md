# 開発環境構築
docker-compose up -d

## よく使うdocker-compose コマンド
```
コンテナ作成 & コマンド起動
docker-compose up
```

```
バックグラウンドオプション
docker-compose up -d
```

```
イメージの再構築
docker-compose up -d --build
```

```
既存コンテナでコマンド実行
docker-compose exec mysql echo 'hello'
```

```
立ち上がっているコンテナ一覧表示
docker container ls -a
```

```
コンテナ作成 & コマンド実行
docker-compose run mysql echo 'hello'
```

```
コンテナ停止
docker-compose stop
```

```
コンテナ削除
docker-compose rm
```

```
コンテナ停止、削除
docker-compose down
```

```
コンテナ表示
docker-compose ps
```

```
ログ
docker-compose log
```

```
全イメージ表示
docker-compose images
```
