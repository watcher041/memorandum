
# 備忘録（MySQL）

```
0.MySQL（MariaDB）のインストール
　$sudo apt install default-mysql-server default-mysql-client

1.MySQLを一旦止める
　$sudo service mysql stop

2.セーフモードでMySQLを起動
　$sudo mysqld_safe --skip-grant-tables

3.別のターミナルからMySQLを起動
　$mysql

4.rootユーザーのパスワードを無効にする
　mysql> UPDATE mysql.user SET authentication_string=NULL WHERE user='root';

5.セーフモードを終了させるためkillする
　$sudo pkill -f mysqluse mysql;

6.MySQLを通常通り起動する
　$sudo service mysql start

7.MySQLをパスワード認証に変更する
　$ sudo mysql
　mysql> USE mysql;
　mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
　mysql> FLUSH PRIVILEGES;
mysql> exit;