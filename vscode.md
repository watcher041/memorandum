
# 備忘録（vscode）

```
vscodeでspringを使用する方法

設定画面で「リモート[WSL:Ubuntu]」を選択して、setting.jsonに以下のように記入する。

"java.configuration.updateBuildConfiguration": "interactive",
  "java.home": "/home/ubuntu/.asdf/installs/java/adoptopenjdk-17.0.1+12",
  "java.completion.enabled": true

service データを利用した処理に利用
repository データの処理方法の記載に使用

【vscodeでgitを使用する方法】
git config --global user.name "Your Name"
git config --global user.email "youremail@domain.com"


vscodeの拡張機能

・Markdown+Math（KaTeXの機能をすべて利用するために必要）
・Japanese Language Pack for Visual Studio Code（日本語で表示するために必要）
・Remote Develeopment（WSLをvscodeで使用するために必要）
・LaTeX Workshop（vscodeでLaTeXを利用する場合に必要）
・Spring Boot Extension Pack（vscodeでspringを用いるために必要）
・Draw.io Integration（vscodeで図を描くときに利用する際に必要）
・git histroy、gitgraph（gitの履歴を一目でわかるようにするための機能）
