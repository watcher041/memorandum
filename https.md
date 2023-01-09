
# 備忘録（https）


## https通信（接続確認）

```
SSL(Secure Sockets Layer) 情報を暗号化して送受信するプロトコル。これを HTTP で使う場合のプロトコルが HTTPS です。個人情報やクレジットカード情報を送受信する場合、その中身を暗号化して途中経路で読めなくする場合などに使います。利用者のレベルでは HTTP や HTTPS の違いをほとんど意識することはありません。

一方、サーバー側には SSL の鍵や証明書を使うことでこの暗号化を実現します。単にサーバーそのものを SSL 対応することに加え、この鍵や証明書を用意しておく必要があります。商用サイトであれば専門の機関から有償で発行してもらうもののですが、検証目的であれば OpenSSL を使って自分で（無償で）用意することもできます。

余談ですが、自分で用意した場合は自分の作った証明書で自分を信じこませて認証する、という意味で「オレオレ SSL」とか「オレオレ証明書」とか言われます。いずれこれらも「母さん、助けてSSL」とかって呼ばれるようになるんでしょうかね（笑）。

で、その自分で SSL を有効にするまでの手順も紹介します。まずは OpenSSL と、Apache HTTPD に SSL 機能を付けるための mod_ssl モジュールを導入します（既に導入済みかもしれません、その場合は気にせず先に進んでください）：

　　# yum install openssl
　　# yum install mod_ssl

 まずはオレオレ証明書を作ります。その前提として秘密鍵と公開鍵のペアが必要なので、順序としては (1)秘密鍵を作って、(2)公開鍵を作って、(3)証明書を作る　という流れになります。この順に説明します。なお以下ではこれらのファイルを /etc/httpd/conf/ 以下に作成する前提で進めますが、別ディレクトリでも構いません。その場合は適宜読み替えてください。

最初に /etc/httpd/conf に移って、(1)秘密鍵を server.key というファイル名で作成します：

　# cd /etc/httpd/conf
　# openssl genrsa -aes128 1024 > server.key

この例では 128 ビットの AES 方式で暗号化した 1024 ビットの秘密鍵を作っています。途中でパスフレーズ（パスワード）を聞かれるので適当なパスワードを指定し、直後に確認のため同じパスワードを入力します。これで秘密鍵が完成です。

次にこの秘密鍵ファイルとペアになる (2)公開鍵ファイルを server.csr というファイル名で作成します：

　# openssl req -new -key server.key > server.csr

２文字の国識別コードには JP を指定、都道府県、市区町村を指定、それ以外は Common Name 欄以外は空白のままで大丈夫です。
注意点として、Common Name には実際に HTTPS として運用する際にブラウザから指定するアドレスを入力することになります。 https://www.xxx.com/ といったサーバー名でアクセスするのであれば xxx.com を、https://192.168.XXX.XXX/ といった IP アドレスでアクセスするのであれば IP アドレスを指定してください。

最後にこの秘密鍵と公開鍵を使って (3)証明書ファイルを server.crt というファイル名で作成します：

　# openssl x509 -in server.csr -days 36500 -req -signkey server.key > server.crt

この場合では X.509 形式の証明書ファイルを、有効期間36500日（約100年）の指定で作成しています。これだけ指定しておけば自分の開発期間中はまあ大丈夫でしょうｗ。


これでデジタル証明書の作成までできましたが、このまま使うと Apache HTTPD の起動時にパスフレーズの入力を求められることになります。開発期間中はそれでもいいのかもしれませんが、実際の運用はサーバーの起動と同時に HTTPD サーバーも起動させたりするので、ここまでの作業が完了していればパスフレーズは不要と言えます。パスフレーズは以下のコマンドで解除することができるので、一応紹介しておきます：

　# mv server.key server.key.bak
　# openssl rsa -in server.key.bak > server.key

デジタル鍵／証明書が用意できたので、実際にこれを使って HTTPS を構築します。mod_ssl をインストールしたタイミングで /etc/httpd/conf.d/ssl.conf が作られているはずなので、このファイルを編集して今作成したこれらのファイルを指定します：
# vi /etc/httpd/conf.d/ssl.conf

  :
<VirtualHost _default_:443>
  ErrorLog logs/ssl_error_log
  TransferLog logs/ssl_access_log
  LogLevel warn
  SSLEngine on
  SSLProtocol all -SSLv2
  SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HIGH:+MEDIUM:+LOW
  +SSLCertificateFile /etc/httpd/conf/server.crt
  +SSLCertificateKeyFile /etc/httpd/conf/server.key
  <Files ~ "\.(cgi|shtml|phtml|php3?)$">
    :
  </Files>
</VirtualHost>

これで準備は整いました。では HTTP(S) サーバーを起動します：

起動後、ウェブブラウザを使ってこのサーバーにアクセスしてみます。まずは普通に http://XXX.XXX.XXX.XXX でアクセス。特に何もしていなければ Apache HTTPD のデフォルトページが表示されるはずです：

次に同じアドレスに対して https://XXX.XXX.XXX.XXX でアクセスを試みます。SSL でのアクセスになります。証明書がオレオレなので、「信頼できないよ（第三者が保証するものではないよ）」と警告されます。

ブラウザによってこの後の承認方法が異なりますが、Chrome であれば「このまま続行」ボタン、FireFox であれば「危険性を理解した上で接続するには」 - 「例外を追加」 - 「セキュリティ例外を承認」 を順に選択することで先に進むことができて、本来のトップページが HTTPS プロトコルで見ることができます。
