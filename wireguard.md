
# 備忘録（wireguard）

```
wireguardによるVPN接続の設定

※ firewall はUDP 51820のみ（IPは任意でないとサーバーに接続できない）許可しておく

サーバー側のセットアップ
最初にカーネルパラメータの設定だけしておく。お外との通信のためにはパケットのフォワードを許可する必要があるため。

/etc/sysctl.conf にて net.ipv4.ip_forward=1 のコメントアウトを外す

# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
設定反映

# sysctl -p
インストール

# apt install wireguard
秘密鍵、公開鍵の作成

サーバーとクライアント同士は公開鍵認証によって暗号化される。専用のコマンドで乱数を生成する。

# 秘密鍵の生成
$ wg genkey > server_private.key
# 秘密鍵から公開鍵を生成
$ wg pubkey > server_public.key < server_private.key
クライアント側の鍵も生成しておく

# 秘密鍵の生成
$ wg genkey > client_private.key
# 秘密鍵から公開鍵を生成
$ wg pubkey > client_public.key < client_private.key
なおつくったファイル自体はいらないのでcatだけしてメモっておく

設定を書く。wireguardでは通常インターフェース名wg0で通信するので /etc/wireguard/wg0.conf を作成して以下

ens3の部分はサーバーがデフォルトゲートウェイへ接続できるNICを書くので適宜変更する。

[Peer]項目に書く公開鍵はクライアント側なので注意

[Interface]
PrivateKey = 作成したサーバーの秘密鍵(server_private.key)
Address = 10.0.0.1
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o ens3 -j MASQUERADE

[Peer]
PublicKey = 作成したクライアントの公開鍵(client_public.key)
AllowedIPs= 10.0.0.2/32
クライアント側のセットアップ
同様にwireguardをインストールしておく

クライアントがMac OSの場合
App Storeかbrewでクライアントをインストールできる。App Storeの方はGUIだが野良ではインストール不可

今回はbrewでインストールした

brew install wireguard-tools
すると wg というコマンドが叩けるようになるはず（sudo必要

 クライアントがiOS / Androidの場合
アプリが公式でリリースされているので「wireguard」でググってインストール

設定を手入力でもできるがダルいのでQRコード読み込みがおすすめ

サーバーでQRコードを生成できるコマンドをインストール

apt install qrencode
後述する設定ファイルを適当な場所で作って標準入力で読み込ませる

qrencode -t ansiutf8 < client.conf
表示されたQRコードをスマホのwireguardアプリで読み込ませればおｋ

で、肝心の設定 Ubuntuの場合は /etc/wireguard/wg0.conf Macの場合は /usr/local/etc/wireguard/wg0.conf に以下を記述

[Interface]
PrivateKey = 作成したクライアントの秘密鍵(client_private.key)
Address = 10.0.0.2

[Peer]
PublicKey = 作成したサーバーの公開鍵(server_public.key)
Endpoint = サーバーのグローバルIP:51820
AllowedIPs = 10.0.0.0/24 
PersistentKeepAlive = 30
⇨ 24としない
と、10.0.0.1以外のリクエストを受け付けない、応答しない
起動
サーバー側で自動起動有効化と起動

$ sudo systemctl enable wg-quick@wg0
$ sudo apt install linux-headers-$(uname -r)
$ sudo systemctl start wg-quick@wg0
うまく起動できていれば ip a したときにwg0がいるはず

# ip a
(略)
7: wg0: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1420 qdisc noqueue state UNKNOWN group default qlen 1000
    link/none
    inet 10.0.0.1/32 scope global wg0
       valid_lft forever preferred_lft forever
クライアント側も起動してみる

sudo wg-quick up wg0
そうすると無事にVPN接続ができるようになっているはず

ポイントはクライアント設定の AllowedIPs で、これを0.0.0.0/0だとすべての通信がVPN経由に、10.0.0.0/24だとそのIP帯のみがVPN経由になる IP帯は AllowedIPs = 10.0.0.0/24, 192.168.1.0/24 のように , で複数記述できる

