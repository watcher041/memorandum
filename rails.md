
# 備忘録（rails）

```
 ----------------------------------------------------------------------

　定期的に実行する処理
　
　lib/task配下にrakeクラスを作成

　例) 月1回　入室履歴を初期化

　　（entered.flag = false）

　wheneverというgemと組み合わせて実行

　rake （名前）　（taskの名前）

 ----------------------------------------------------------------------

　dpcker：どんな環境でも動く、
　
　VPS：Javaと同じ
　VMとコンテナ型（OSを分けて、それごとにアプリを動作させる）
　Rails +
　nginx：webサーバー

 ----------------------------------------------------------------------

　form_with
　=f.submit
　→ ボタンを押さないと非同期通信ができない

 ----------------------------------------------------------------------

before_delete
中間テーブルにて、紐づくものがあれば削除しない、ないなら削除

Basic認証
basic-auth

where … 複数レコードがあるとみなされる
find_by … 一つだけ抜き取る

・form_with … モデル名を渡すので、その中に{}で入った状態になる
require有り
・form_tag … モデル名なしで、いきなりただ入力する形になる
requireなし
・form_for … 自動でclassが付与されるので無効化する必要がある
ほぼ同じ（モデル名が使われる）html:{class:}
 
ActiveRecordクラス

インクリメンタルサーチ

enum：少ないレコードのテーブルを一つのカラムに入れるのに有効

active-hash gem
モデル内でハッシュを作り、テーブルのようにする（都道府県データなど）

git cherry_pick

flex-wrap

credential.yml ⇆ Master.key

AMI

gemリスト
・gem enum_help
・active_hash
・devise
・MiniMagic
・CarrierWave
・jQuery-Rails
・Haml-Rails
・pry-Rails
・Kaminari
・fog-aws
・font-awesome-sass
・ancestory
・pay-jp

