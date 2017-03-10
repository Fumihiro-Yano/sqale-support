# Herokuへの移行手順
## 移行準備
### Herokuのアカウント登録
Herokuのアカウントをお持ちでない方は[https://signup.heroku.com/](https://signup.heroku.com/)からアカウントを作成して下さい。

### データベースのバックアップ
[アプリケーションの復元方法（バックアップとリストア）](https://sqale.jp/support/manual/db-backup-restore)を参考にデータベースのバックアップをおこなって下さい。

## アプリケーションの作成
Herokuダッシュボード内の「New」から「Create new app」を選択します。

![](images/heroku-new.png)
![](images/heroku-create-new-app.png)

「App Name」を決めたら、「Create App」ボタンをクリックしてアプリケーションを作成します。

![](images/heroku-app-name.png)

## Heroku CLIの設定
アプリケーションをデプロイするために[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)の設定を行います。
`heroku login` コマンドを入力してログインをしてから、以下のコマンドを実行して下さい。

```
$ heroku git:remote -a <アプリケーション名>
```

git push でデプロイを行います。
```
$ git push heroku master
```

## データベースのリストア
### ClearDB MySQLの設定
herokuコマンドで[ClearDB MySQL](https://elements.heroku.com/addons/cleardb)にデータを移行する準備を行います。

```
$ heroku addons:create cleardb:ignite
```

データベースのURLを確認します。

```
$ heroku config | grep CLEARDB_DATABASE_URL
```

URLを確認した後に`DATABASE_URL`をセットします。

```
$ heroku config:set DATABASE_URL='mysql2://b1cc**********:6b******@us-cdbr-iron-east-04.cleardb.net/heroku_c2394**********?reconnect=true'
```

もし、` Cannot overwrite attachment values DATABASE_URL.` というメッセージが表示されて、
`DATABASE_URL`をセットできない場合は、以下のコマンドでdetachまたはheroku-postgresqlのdestroyをおこなってからセットして下さい。
```
$ heroku addons:detach DATABASE_URL
$ heroku addons:destroy heroku-postgresql -a mameblo
```

Herokuのダッシュボード内の「Personal apps」から「ClearDB MySQL」を選択するとデータベースの接続情報の確認ができます。

![](images/heroku-clear-db.png)

```
mysql --host=us-cdbr-iron-east-04.cleardb.net --user=b1cc********* --password=6b********* --reconnect heroku_c23*********** < mysql.dump
```

## アプリケーションの表示の確認
https://<アプリケーション名>.herokuapp.com/ が表示できたら移行は完了です。  
これまで「Sqale」をご利用いただき、誠にありがとうございました。
