
# 備忘録（dokcer）

## 用語について

 run = create + execute、exied = stop + exec
 commit：コンテナからのイメージを作成する

本番、ローカル環境全体　→ 船

アプリ自体、データベースなど　→ コンテナ

これらで一つの動作環境が整ったアプリができる。 → 海を渡れる。

「Docker for windows でエラーが発生した場合」

settings → Docker Engine →  "buildkit": false に変更

---

## Dockerをユーザーが実行できるようにする方法
```
$ grep -i docker /etc/group
docker:x:990:

$ sudo gpasswd -a username docker
Adding user username to group docker

$ grep -i docker /etc/group
docker:x:990:username
```
ログアウトすると、変更が有効になる。。

再ログイン後、docker login でdocker にログインする

kubernetes で8080~が出る場合

---

## さくらVPSでDockerを作動
```
・sshで繋げる際のパスワードを設定
　sudo passed rancher

・rancherOSのisoをsftp経由でVPSにダウンロードする
　sftp ユーザ名@ホスト名
　sftp> cd /iso
　sftp> put ubuntu-16.04.3-server-amd64.iso  
・rancherOSをVPSにインストール（docker）
・rancherOSのインストールにはまずまっさらな状態で
　→ sudo fdisk -l （インストール先のディスクを確認）
　→ もし先にインストールしたものがあれば、削除
　　sudo fdisk 対象のディスク
　→ 何もない状態になった場合には、
 　　  sudo ros install -d /dev/vda --append "rancher.password=password"
```
---

## docker で centos を起動する方法
```
docker run -p 80:80 -h "centos_manager" -d --privileged --name hinemos_container centos:7 /sbin/init
```
→ これをしないと、systemctl が動かない
→ ホストネームを「centos_manager」に指定

---

## hinemos導入スクリプト（setup.sh）
```
#! /bin/bash

MANAGER_NAME=hinemos-6.2-manager-6.2.2-1.el7.x86_64.rpm
WEB_NAME=hinemos-6.2-web-6.2.2-1.el7.x86_64.rpm

yum update -y
yum install -y java-1.8.0-openjdk vim-common unzip net-tools wget 

if [ ! -f /root/downloads ] 
then
  mkdir /root/downloads
fi
cd /root/downloads
if [ ! -f  ${MANAGER_NAME} ]
then
  wget https://github.com/hinemos/hinemos/releases/download/v6.2.2/${MANAGER_NAME}
  rpm -ivh ${MANAGER_NAME}
fi
if [ ! -f ${WEB_NAME} ]
then
    wget https://github.com/hinemos/hinemos/releases/download/v6.2.2/${WEB_NAME}
    rpm -ivh ${WEB_NAME}
fi

systemctl start hinemos_manager
systemctl start hinemos_web

netstat -aon | grep 8080
```

---

## docker を完全に削除する方法
```
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce  
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.socksudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
```

コンテナを作成する方法
```
docker build . -t apl
```

コンテナを起動するためコマンド
```
docker run -it -v $PWD:/home apl
```

## macでdokcerを使用する方法（podman利用）
```
brew install podman
podman --version
podman machine init
podman machine start
podman machine ls
brew uninstall --cask docker
brew install docker
podman system connection list 

上記のコマンドを元に以下を編集
~/.ssh/config

Host podman
  HostName localhost
  Port 49560
  User root
  NoHostAuthenticationForLocalhost yes
  IdentityFile ~/.ssh/podman-machine-default

ssh podman

繋がらない場合はpodman machineの動作を確認して起動する。
podman machine ls
podman machine start

~/.zshrc に以下を追記
  export DOCKER_HOST=ssh://podman


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

```