
# 備忘録（Vue）

```
要素の形に応じて、vue による操作も変わってくる。

例１）vue の変数の文字を出力する
<div id = "app1">{{ message }}</div>

var app1 = new Vue({
  el: '#app1', → 取得する要素を取得
  data: { message: " Hello World"} → message に値を代入
})

例２）変数の文字を出力する別の方法
<div id="app2">
  <div v-text="message"></div>   → message に値を代入
</div>

var app2 = new Vue({
  el: '#app2', → 取得する要素を取得
  data: { message: " Hello World"} → message に値を代入
})

例３）html の形で代入する
<div id="app3">
  <div v-html="message"></div>   → message に値を代入
</div>

var app3 = new Vue({
  el: '#app3', → 取得する要素を取得
  data: { message: " <p>Hello World</p>"} → message に html 形式でを代入
})

例４）条件によって表示を変更する
<div id="app4">
  <input type="checkbox" @click="change" checked> → チェックボックス
  <div v-show="judge">Hello!</div>   → judge の値によって出力するか決める
</div>

var app4 = new Vue({
  el: '#app4', → 取得する要素を取得
  data: { judge: true }, → 初期値を設定
  methods: { → チェックボックスの切り替えで表示されるか変更する
    change: function(e) {
      this.judge = e.target.checked
    }
  }
})

例５）変数をループさせて出力する
<div id="app5">
  <ul>
    <li v-for="color in colorList">{{ color }}</li>
  </ul>
</div>

var app5 = new Vue({
  el: '#app5',
  data: { colorList: [ 'Red', 'Green', 'Blue' ] }
})

例６）