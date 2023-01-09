
# 備忘録（WSL2）


## WSL2でsnapを使用する方法
```bash
sudo apt update && sudo apt install -yqq daemonize dbus-user-session fontconfig
sudo daemonize /usr/bin/unshare --fork --pid --mount-proc /lib/systemd/systemd --system-unit=basic.target
exec sudo nsenter -t $(pidof systemd) -a su - $LOGNAME
snap version
```

## nextcloudの導入
```bash
sudo apt install apache2
sudo service apache2 start
sudo service apache2 status
```
powershell にて
```powershell
netsh.exe interface portproxy add v4tov6 listenport=80 connectaddress=::1
netsh.exe interface portproxy show v4tov6
```
```
ipv4 をリッスンする:         ipv6 に接続する:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
*               80          ::1             80
```

## WSLマシンの複製・削除方法

マシンの元データを作成
```powershell
wsl --export <ディストロ名> <エクスポート先のファイルパス>
```
```
例）> wsl.exe --export Ubuntu c:\Users\foo\Ubuntu.tar
```
マシンを作成する
```powershell
wsl --import <ディストロ名> <インストール先のフォルダパス> <インポートするファイルパス>
```
```
例) > wsl --import Ubuntu2  c:\Users\foo\wsl\Ubuntu2 c:\Users\foo\Ubuntu.tar
```

## ディストリビューションを起動する
```powershell
wsl -d <ディストロ名> -u <username>
```
```
例) > wsl -d Ubuntu2 -u foo 
```

※ 作成したディストロが不要なら、以下のコマンドで解除する
```
wslconfig /u <ディストリビューション名>
```
二つを動作させるとIPが同じ値になってしまうので、固定IPを指定する
```
/etc/sysconfig/network-scripts/ifcfg-enp3s0
```
（ネットワークアダプターの名前ごとに変わる）
```
 DEVICE="enp3s0"
 ONBOOT=yes
 NETBOOT=yes
 UUID="0ebc2621-6086-4d9a-a15d-c52cdf6ade01"
 IPV6INIT=no
-BOOTPROTO=dhcp
 HWADDR="08:00:27:bf:e6:ec"
 TYPE=Ethernet
 NAME="enp3s0"

+BOOTPROTO=static
+DNS1="8.8.8.8"
+DNS2="8.8.4.4"
+DOMAIN=mydomain.com
+IPADDR=192.168.100.11
+NETMASK=255.255.255.0
+GATEWAY=192.168.100.1
```
　→ 結局、2つのマシンでIPが同期されているため、どうしようもない
　

　systemctlが動作しない場合は、以下のコマンドを実行
　（エラー：Failed to get D-Bus connection: Operation not permitted）
```
sudo su -
mv /usr/bin/systemctl /usr/bin/systemctl.old
curl https://raw.githubusercontent.com/gdraheim/docker-systemctl-replacement/master/files/docker/systemctl.py > /usr/bin/systemctl
chmod +x /usr/bin/systemctl　
```