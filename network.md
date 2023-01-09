
# 備忘録（network）

 

## ネットワ－クの基本設定などを確認（Linux版）

```
ネットワークの設定に関わるディレクトリ 
(多くは, /etc/init.d , /etc/rc.d などでのスクリプトにより設定を行う)

    /etc/modules.conf : module の設定
    ネットワーク設定(IPアドレスなど)
        /etc/network/interfaces : (Debian)
        /etc/rc.d/rc.inet1 : (Plamo)
        /etc/sysconfig/network-scripts など : (RedHat, Vine)
        /etc/init.d/* 又は /etc/rc.d/init.d/* : 初期設定スクリプト 
    ホスト名参照
        /etc/hostname : 自ホスト名(Debian)
        /etc/sysconfig/network: 自ホスト名(RedHat)
        /etc/HOSTNAME : 自ホスト名(Plamo)
        /etc/hosts : IPアドレス と ホスト名の対応表(自ホスト名を含む)
        /etc/resolv.conf : DNS 参照先の設定
        /etc/nsswitch.conf : データの参照方法, ファイル, NIS などの設定 
    ネットワークサービス
        /etc/services : ネットワークサービスポート名
        /etc/inetd.conf : inetd の設定
        /etc/hosts.allow : TCP-Wrapper tcpd の接続許可設定
        /etc/hosts.deny : TCP-Wrapper tcpd の接続拒否設定 
    初期化スクリプト/コマンド
        ifconfig
        route
        ifdown eth0; ifup eth0 : 初期化スクリプト (Debian)
        /etc/init.d/networking: 設定スクリプト (debian)
        /etc/rc.d/rc.inet1: 設定スクリプト(Plamo) 

ハード的な認識, ドライバ
NIC のハードウェアを認識し, さらに, ドライバが組み込まれていることを確認. ここまでで, eth0 のようなデバイスが有効になる. (まだ通信はできない)

    ハード的に検出可能か？
    カーネルが認識していないカードでも, PCI バスから情報を引き出せる.
    cat /proc/pci, lspci -b, lspci -vv , lspci -b
    lspci -b はカーネルの認識とは独立に, PCI バスから IRQ を調べる.
    カーネルがどう認識しているか？
    cat /proc/ioports, cat /proc/interrupts

    $ cat /proc/pci
    ....略
    Bus  1, device   8, function  0:
    Ethernet controller: Intel Corp. 82801CAM (ICH3) PRO/100 VE (LOM) Ethernet Controller (rev 66).
    IRQ 11.
    Master Capable.  Latency=66.  Min Gnt=8.Max Lat=56.
    Non-prefetchable 32 bit memory at 0xd0200000 [0xd0200fff].
    I/O at 0x7000 [0x703f].

    $ cat /proc/ioports
    ...略
    7000-703f : Intel Corp. 82801CAM (ICH3) PRO/100 VE (LOM) Ethernet Controller
      7000-703f : eepro100

    $ cat /proc/interrupts 
               CPU0       
    ...略
     11:    1243459          XT-PIC  Intel ICH3, usb-uhci, usb-uhci, usb-uhci, i82365, eth0
    ...略

    ドライバ が組み込まれているか？
    dmesg でドライバの組み込みを確認.
    /var/log/syslog, /var/log/messages なども参考に.
    モジュールを, 認識できているか？
    lsmod で使用中のモジュール一覧を見る.
    /lib/modules/(カーネル version)/modules.dep にモジュール一覧がある.
    ドライバの起動オプションは正しいか？
    カーネルオプションで ether=0,0,0x30,eth0 とか
    insmod で insmod e1000 TxDescriptors=80,128 のように (man insmod, /etc/modules.conf)
    パラメータを指定することがある.
    カーネルソースの Documentation/kernel-parameters.txt, Documentation/networking/ の文書を読む.
    インターフェースが生きているか？
    /sbin/ifconfig eth0 で Device not found, unknown interface にならないこと. 

インターフェース
eth0 に IP アドレスを与える. ルーターを越えない隣接ホストには ping -nr (IPアドレス) で通信を確認できるようになる.

    インターフェースが生きているか？
    /sbin/ifconfig eth0 で Device not found, unknown interface にならないこと.
    IPアドレスなどを設定
    /sbin/ifconfig eth0 ${IPADDR} broadcast ${BROADCAST} netmask ${NETMASK}
    のエラーが出る場合,
    ハード認識かドライバに問題がある.
    インターフェースが正しく設定されているか？
    /sbin/ifconfig

    $ /sbin/ifconfig eth0
    eth0      Link encap:Ethernet  HWaddr 00:01:02:03:04:05  
              inet addr:192.168.1.100  Bcast:192.168.1.255  Mask:255.255.255.0
              UP BROADCAST NOTRAILERS RUNNING  MTU:1500  Metric:1
              RX packets:600988 errors:0 dropped:0 overruns:0 frame:0
              TX packets:446624 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:100 
              Interrupt:11 Base address:0xd000 

    パケットが流れているか？
    /sbin/ifconfig の表示の packets の項.
    隣接(ルータを越えない)マシンと通信できるか？
    ping -nr (相手のIPアドレス)
    異常が無いか
    ifconfig, cat /proc/net/dev , netstat -i も用いて, errs(エラー), colls(パケット衝突) など確認. 

ルーティング
ネットワークのパケットの送付先などを設定します. ルーターを越えたマシンとパケットをやりとりできるようになります.

    ルーティング設定.
    /sbin/route ....
    ルーティング確認.
    /sbin/route -n, cat /proc/net/route

    $ /sbin/route   
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    192.168.1.0     *               255.255.255.0   U     0      0        0 eth0
    127.0.0.0       *               255.0.0.0       U     0      0        0 lo
    default         192.168.1.1     0.0.0.0         UG    0      0        0 eth0

    ルーターを越えたマシンと通信できるか？
    ping -n (相手のIPアドレス)
    相手を IP でなく マシン名で指定するには, /etc/hosts か DNS でマシン名参照を可能にする必要がある.
    パケットの送付経路は？
    traceroute -n (相手のIPアドレス)
    この段階でルーターを越えたマシンとパケットをやりとりできる. 

参考:

    ルータ,ファイアウォール などでは更に iptables で パケットの送付/受理などに関する設定を行う. (...がここでは省略.)
    pingを耳で聞きながら作業できます.
    ping IPアドレス | gawk '{printf "\a"}' 

マシン名参照, DNS サーバ
IPアドレスでなく, マシン名を使う事ができるようになります.

    自分自身のマシン名を確認/設定
    /bin/hostname, /bin/domainname コマンドで
    名前の参照方針
    /etc/nsswitch.conf の hosts の項で大まかな参照方法をきめます.
    DNS の参照方法を指定.
    /etc/resolv.conf に
    /etc/hosts に自分自身が書かれていないと問題がおこる事があります.
    DNS 参照ができるか？
    nslookup マシン名, dig マシン名
    ここまでで, ping などに IPアドレスでなく マシン名を使う事ができる. 

接続の許可／禁止
ネットワークサービスを行うサーバを運用する場合は以下も確認する. 接続の処理の順序を考慮しながら確認してゆく.

    接続要求が(ネットワークから)到達.
    tcpdump, iptraf などで確認
    iptables によるパケットフィルタ.
    iptables -L -n などで確認
    サーバプロセスがデーモンとして待機している場合(ps aux でデーモンが見える), そこで処理される.
    各サーバの設定ファイル, ログなどを確認.
    そうでない場合, inetd が処理.
    /etc/inetd.conf で inetd を介したネットワーク接続の処理方法を決めます. ここで用いる サービス名は /etc/services に書かれています.
    例.
    次は telnet サービスの(ネットワークから自分への)接続要求に対して tcpd が(hosts.allow, hosts.deny で)確認してから, in.telnetd プログラムが対処する事を意味します.

    telnet  stream  tcp     nowait  root    /usr/sbin/tcpd  in.telnetd
    	  

    tcpd が接続アドレスの許可条件などを確認 (/etc/hosts.allow)
    tcpd が接続アドレスの禁止条件などを確認 (/etc/hosts.deny)
    (上の例では) in.telnetd が実際のサービスを行う.
    netstat でソケットの使用状況を確認できる 









Linuxの場合、システムに1つだけある「ルート」（/）ディレクトリを頂点として、 その下にあらゆるディレクトリやファイルがぶらさがる構造になっています。
/（ルート）→
bin 	      一般ユーザー権限で実行できる基本コマンドを格納
boot 	      Linux起動時に必要なファイルを格納
dev 	      デバイスファイルを格納
etc 	      設定ファイルを格納
home 	      ユーザーのホームディレクトリ
lib 	      /binや/sbin置かれた実行ファイルの共有ライブラリー
lost+found    破損したファイルなどを格納
media 	      CDやDVDなどを自動的に接続するマウントポイント
mnt 	      ファイルシステムの一時的なマウントポイント
opt 	      パッケージ管理システムがパッケージなどを格納
proc 	      カーネルとの間で情報をやり取りするための特殊ファイルを格納
root 	      rootユーザー用のホームディレクトリ
sbin 	      システム管理用の基本コマンドを格納
srv 	      サーバーなどが使うサイト固定のデータを格納
sys 	      ホットプラグ可能なデバイスの情報などを格納
tmp 	      作業用の一時的なディレクトリ
usr 	      ユーザーが共有する読み出し專用データを格納
var 	      ログファイルや作業用ファイルなどを格納 




「設定ファイル」は、/etcに有る

　Linuxの「設定ファイル」は、ほとんど「/etc」に有り、拡張子が「conf」となっているファイルが多いです。

$ sudo gedit（nano） /etc/ディレクトリー/(目的の設定ファイル名)

パスワードを入力＞「Enter」

「gedit（nano）」（＝テキストエディタ）で設定ファイルの書き換え＞「保存」
「/etc」に有る設定ファイルは、6つに分類できます。

    システムの起動関連
    システム設定全般関連
    シェルやアプリケーション関連
    ネットワーク関連
    サーバーソフト関連
    X Window System 関連

「/etc」ディレクトリに有る設定ファイルの例
ファイル名 	ファイルの役割
bashrc 	        bashシェルがログイン時に参照
crontab 	cronコマンドで定期的に実行するプログラムの日時・時刻などを設定
csh.login 	Cシェルがログイン時に参照
exports 	NFSクライアントにアクセスを許す共有ディレクトリの一覧
fstab 	        ファイルシステムのマウントポイント
ftpusers 	FTPアクセスを禁止するユーザー一覧
gateways 	動的経路を設定するroutedコマンドが参照
group 	        ユーザーグループの定義
host.conf 	名前解決の際の優先順位を定義
hosts 	        名前解決のためのホスト名とIPアドレスの対応付けを定義
hosts.allow 	xinetd経由で起動するサーバーのアクセス可能ホストを定義
hosts.deny 	xinetd経由で起動するサーバーのアクセス拒否ホストを定義
hosts.equiv 	外部からのリモートアクセスを許可するかどうかを設定
inetd.conf 	ポートを監視するinetdに対象ポートと、アクセスを発見した場合に起動するプログラムを指示
inittab 	起動時のランレベルなどを定義
issue 	        ローカルログイン時の表示メッセージ
Id.so.conf 	共有ライブラリの検索パスを定義
motd 	        コンソールログイン時の表示メッセージ
mtab 	        マウント中のファイルシステム一覧
mtools.conf 	MS-DOSのディスクにアクセスするmtoolsコマンド群の設定
networks 	ブート時に用いるホスト名とIPアドレスの対応情報
passwd 	        ユーザー名やユーザーIDなどを記録
profile 	ログイン時にbashが参照・実行する設定
protocols 	通信プロトコルの種別を定義
resolv.conf 	DNSによる名前解決のための問い合わせ先を設定
securetty 	rootユーザーがログインできるターミナルを設定
services 	ネットワークサービス名と利用ポート番号、プロトコルの対応を定義
shells 	        ログインシェルとして利用可能なシェルを記述
syslog.conf 	syslog（ログデーモン）の設定 

```