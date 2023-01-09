
# 備忘録（webpack）

講義で作成したものを、gitに挙げていく

npm init -y

npm install --sav-dev webpack@4.43.0 webpack-cli@3.3.11

npm install --save jquery@3.5.1 velocity-animate@1.5.2

→ -dev をつけて追加すると開発時に使うときに利用
　つけない場合は実行時に使うものとして区別する

npm install --save-dev webpack-merge@5.0.9

npm install --save-dev terser-webpack-plugin@3.0.8

ソースマップを指定すると、ブラウザ上でエラーが発生したときに発生源となるファイルを特定できる。
devtool: 'cheap-module-eval-source-map' → app.js
devtool: false → bundle.js（バンドル後のものになるため、結局どこにあるかわからない）

npm install --save-dev clean-webpack-plugin@3.0.0

npm install --save-dev html-webpack-plugin@4.3.0

npm install --save-dev webpack-dev-server@3.11.0
