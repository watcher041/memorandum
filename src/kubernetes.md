
# 備忘録（kubernetes）

```

【 用語について 】
イメージとしては、nodeという括りがあり、中にコンテナで構成された（アプリのようなもの）Podがある。

# yaml ファイルの基本構造
apiVersion: 
kind:
⇨ nodeの種類を示したもの（Pod、Serviceなど）
metadata:
⇨ kubectl describeなどで表示される情報を記載する　
spec:
⇨ 実際のシステムの内容を示す


【 mac で kubernetes が動かない場合 】

1. pkiと.kubeの中身を削除
rm -rf ~/Library/Group\ Containers/group.com.docker/pki/
rm -rf ~/.kube

2. Reset Kubernetes cluster
Troubleshoot -> Reset Kubernetes cluster をクリック

3. Restart
Docker Desktop for Macを再起動したら起動した。


【 kubernetesの構築（Ubuntu）】

# apt install apt-transport-https curl
DockerのGPGキーを取得します。

# curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
kubernetesのリポジトリを追加します。

# apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
kubernetes関連パッケージをインストールします。

# apt install kubeadm kubelet kubectl kubernetes-cni
kubeletが正常に動作するためにはswapがonになっているとだめなので、offにします。

# swapoff -a

managerに接続してのマスターの初期化を行います。
（init以降はkubectlにおいてネットワーク構築時に必要）

$ kubeadm init --pod-network-cidr=10.244.0.0/16

デフォルトの設定を行います。設定ファイルが読み込まれるようになりコマンドからAPIに接続できるようになります。

$ sudo cp /etc/kubernetes/admin.conf $HOME/
$ sudo chown $(id -u):$(id -g) $HOME/admin.conf
$ echo "export KUBECONFIG=$HOME/admin.conf" >> ~/.bashrc

現象
Kubernetesをセットアップする間は特にエラーはなく、kube-systemのpodも正常に稼動している。
しかし、podをdeployするとpending状態となり起動しない。
kubectl describe poを実行すると「0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.」とのメッセージ。
原因
上記のメッセージは「NodeのTaintの条件に対応した状態ではない（= not tolerate）Podをデプロイしようとしたのでデプロイできません」という意味。
Master Nodeにデプロイできるようにするにはtaintを外す処理が必要。
※Taintsの概要についてはKubernetesのtaintsとtolerationsについてを参照。とてもわかりやすい！

対応方法
「kubectl get node」コマンドでMaster Nodeの名前を確認する
$ kubectl get node
NAME      STATUS    ROLES     AGE       VERSION
myhost    Ready     master    20h       v1.11.1
$
「kubectl describe node <node-name>」コマンドを実行しMaster Nodeの「Taints: 」に設定された値を確認する
$ kubectl describe node myhost
Taintsが設定されている場合は以下のように表示される
下記はdescribeコマンドの出力結果の抜粋
CreationTimestamp:  Thu, 18 Oct 2018 13:55:57 +0900
Taints:             node-role.kubernetes.io/master:NoSchedule
設定を外すために「kubectl taint nodes <node-name> <value-of-tains>-」を実行する
tainsの値の後ろに-をつける
# kubectl taint nodes myhost node-role.kubernetes.io/master:NoSchedule-
node/myhost untainted
#
Tainsの設定値を再度describeコマンドで確認する
Taintsが設定されていない場合は<none>と表示される
CreationTimestamp:  Thu, 18 Oct 2018 13:55:57 +0900
Taints:             <none>

【 asdf でkubernetsを構築 】

asdf でminikubeとkubectlをインストール
minikube start --vm-driver=docker
→ minikubeでkubernetsが構築される
→ これをkubectlで操作する