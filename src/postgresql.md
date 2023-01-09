
# 備忘録（PostgresSQL）

```
1. postgresql をインストール
　$sudo apt update
　$sudo apt install postgresql postgresql-client-common
　（これでないとpsqlが使えない）

2. データーベースを作成してみる。
　$amber db create
　→ Connection unsuccessful: postgres://postgres:password@localhost:5432/postgres

3.  postgres にパスワード"password"を設定する 
　$sudo psql -U postgres
　→psql: error: could not connect to server: FATAL:  Peer authentication failed for user "postgres"

Peer認証：ログインしているユーザーのユーザー名とパスワード、postgresql上のユーザー名とパスワードが合致していないと入れないようになっている。

4. /etc/postgresql/バージョン番号/main/pg_hba.conf をpeer からmd5に変更
　local   all             postgres                                peer
　local   all             all                                            peer
（もし、このようにしてパスワードを求められたら、一旦trustにしてパスワードを変更してからmd5にする）

5. サービスの再起動
　$ sudo service postgresql restart

6. 再度、psqlを起動
　$sudo psql -U postgres
　postgres=# alter role postgres with password 'password';

7. 以下のコマンドを実行
　$amber db create migrate
　$shards install
　$amber watch

【 ERROR in ./src/assets/stylesheets/main.scss が出た場合 】

ERROR in ./src/assets/stylesheets/main.scss
Module build failed (from ./node_modules/mini-css-extract-plugin/dist/loader.js):
ModuleBuildError: Module build failed (from ./node_modules/sass-loader/dist/cjs.js):
Error: Node Sass does not yet support your current environment: Linux 64-bit with Unsupported runtime (88)
For more information on which environments are supported please see:

→　linux は最新の node に対応していないため発生?（v14系までが対応）

【 以下のエラーが発生した場合  】
psql: error: could not connect to server: could not connect to server: そのようなファイルやディレクトリはありません
	Is the server running locally and accepting
	connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?

→以下のコマンドを入力すると、downとなっていることが分かる。
$pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
12  main    5432 down   postgres /var/lib/postgresql/12/main /var/log/postgresql/postgresql-12-main.log

$ sudo pg_ctlcluster 12 main start
Job for postgresql@12-main.service failed because the service did not take the steps required by its unit configuration.
See "systemctl status postgresql@12-main.service" and "journalctl -xe" for details.

$ systemctl status postgresql@12-main.service
11月 01 08:02:32 watcher postgresql@12-main[76795]: 2020-11-01 08:02:32.603 JST [76812] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
11月 01 08:02:32 watcher postgresql@12-main[76795]: 2020-11-01 08:02:32.740 JST [76812] LOG:  end-of-line before authentication method
11月 01 08:02:32 watcher postgresql@12-main[76795]: 2020-11-01 08:02:32.740 JST [76812] CONTEXT:  line 92 of configuration file "/etc/postgresql/12/main/pg_hba>
11月 01 08:02:32 watcher postgresql@12-main[76795]: 2020-11-01 08:02:32.740 JST [76812] FATAL:  could not load pg_hba.conf
11月 01 08:02:32 watcher postgresql@12-main[76795]: 2020-11-01 08:02:32.743 JST [76812] LOG:  database system is shut down
11月 01 08:02:32 watcher postgresql@12-main[76795]: pg_ctl: could not start server
11月 01 08:02:32 watcher postgresql@12-main[76795]: Examine the log output.
11月 01 08:02:32 watcher systemd[1]: postgresql@12-main.service: Can't open PID file /run/postgresql/12-main.pid (yet?) after start: Operation not permitted
11月 01 08:02:32 watcher systemd[1]: postgresql@12-main.service: Failed with result 'protocol'.
11月 01 08:02:32 watcher systemd[1]: Failed to start PostgreSQL Cluster 12-main.

→ pg_hba.conf でいじった部分が原因