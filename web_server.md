
# 備忘録（webサーバー）

## webサーバー構築

```

# Ubuntuの場合

1. 後で設定を容易にするために、追加モジュールをインストール。
    ```
    sudo apt-get install resolvconf」
    ```

1. /etc/network/interfacesでIPを固定する。
    ```
    # 自分自身のアドレス
    auto lo
    iface lo inet loopback

    #外から見た時のアドレス
    auto eth0
    iface eth0 inet static

    # ＜最低限、書く必要があるもの＞
    # address：固定IPアドレス
    # netmask：(address)×(netmask)=network（ネットワークアドレス）
    # gateway：ネットワークの原点（通常は、ルーターのIPアドレス）
    # dns-nameservers：URL ⇔ IPアドレスと変換するもの（通常は、上と同じ）
    # URL=hostname.dommainname（ホスト名：PCの名前.ドメイン名：適当な名前）
    # 例）www.google.com ⇔ hostname=www、dommainname=google.com

    # ＜省略可能なもの＞
    # network：ネットワークの種類（識別番号）、ネットワーク内の最小アドレス
    # broadcast：ネットワーク内の最大アドレス
    # dns-domain：ドメイン名

    address 192.168.11.3
    netmask 255.255.255.0
    gateway 192.168.11.1
    dns-nameservers 192.168.11.1
    ```

1. ネットワークの設定を適用（以下3つのどれかを実行）
    ```
    sudo ifdown eth0 && sudo ifup eth0
    sudo /etc/init.d/networking restart
    sudo service restart
    ```

1. IPアドレスの確認（inet(IPv4)アドレスを見よ）
    ```
    ifconfig eth0
    ```

1. ネットにつながるかの確認
    ```
    ping www.google.com
    ```

 1. IPアドレスとホスト名を対応させるために、他のPCで /etc/hosts を設定
    ```
    #自分自身のIPアドレスとホスト名
    127.0.0.1 localhost

    #外から見た時のIPアドレスとホスト名
    127.0.1.1 www（IPを自動取得(DHCP)した場合）
    192.168.11.3 www（IPを固定化した場合）

    #他にリモート接続するPCのIPアドレスも、必要に応じて書く。

    #＜表記の仕方＞
    # IPアドレス IPアドレスに対応するホスト名 別名 別名 …
    # 例）192.168.11.3 www www.zero.com（別名は大抵、ホスト名.ドメイン名で書く）
    ```
```