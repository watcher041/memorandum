
# 備忘録（JavaScript）

```
・script タグ内だと以下の window の部分は省略できる（例）
　window.document.write()
　window.document.getElement()
　window.document.title
　window.alert
　window.prompt

・同期と非同期で読み込む方法が異なる

「同期処理」
const fs = require("fs")
const buf = fs.readFileSync("test.txt")
console.log(buf.toString())

「非同期処理」
const fs = require("fs")
fs.readFile("test.txt",function(err,buf){
  console.log(buf.toString()
})

・callback関数：関数の引数に与える関数のこと（値ではなくそのままの形でぶっこ感じ）、関数を実行するとcallback関数以外の引数を用いてデータを作成してcallback関数に引数を渡すことが基本となっている。

【Vanilla JSの動かし方】
codesandbox（webサイト）からサンプルコードを取得
サンプルコードを配置したら、package.jsonのある場所で以下のコマンドを実行する

npm install
npm uninstall cssnano
npm i -D cssnano@4.1.11
npm run start
