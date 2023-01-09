
# 備忘録（rust）

```
スタック領域にのみ保存する場合は、指定した型のバイト数だけ確保され、変数も直接その値を示す。

ヒープ領域に保存する場合は、スタックに先頭アドレス、バイト数、要素数（配列であれば）を作り、それを通じて間接的に値を参照する形をとっている。
（番地からバイト数を計算するとよくわかる）

先頭アドレスを表示するバイト数はOS（64bitなら8byte）によって異なる。

#[derive(Debug)]
struct …
→ printなどで表示したときの形を自動で整理する。
（表示するときこれがないとエラーになる）
他にもdisplayもあるが、こちらだと細かい設定が必要となり面倒

構造体には impl でメソッドを追加できる（golangにもある）
構造体と同じ名前にする。
メソッド内でSelfとすると、メソッドが所属する構造体を指す
Self { key1:～,key2:～ } → インスタンスが生成されselfになる

traitsを使うと、javaの interface のようなものが作れる。
traits test_trait
これを構造体に導入する場合には、
impl test_trait for 構造体名
で使用する。 

ジェネリック変数を使う場合には名前に<T>のように付けて使用する
例）fn test<T>(){}
　　enum test<T>{}

rust でのエラー対処法

wasm-pack command not found
以下のコマンドを実行してインストールしなおす
$curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh

(base) ~/t/r/sample-wasm ❯❯❯ wasm-pack build
[INFO]: 🎯  Checking for the Wasm target...
Error: wasm32-unknown-unknown target not found in sysroot: "/Users/tsujimuramakoto/.asdf/installs/rust/1.54.0/toolchains/1.54.0-x86_64-apple-darwin"

Used rustc from the following path: "/Users/tsujimuramakoto/.asdf/shims/rustc"
It looks like Rustup is not being used. For non-Rustup setups, the wasm32-unknown-unknown target needs to be installed manually. See https://rustwasm.github.io/wasm-pack/book/prerequisites/non-rustup-setups.html on how to do this.

⇨ rsutup の設定がされていないため、設定する。
$rustup target add wasm32-unknown-unknown

・#[cfg(test)]からcargo testでコンパイルされる
・#[test]を関数につけると、それがテスト対象になる。
・match文を使うと、エラーハンドリングが可能になる。
・命令文の後ろに?をつけると、値が取得できなかった場合にNone（列挙型Optionの一部）を返す。
・トレイと境界はジェネリック型だけではなく、引数でも使用することができる。

```