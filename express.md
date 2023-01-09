
# 備忘録（express）

```
// express をインストール
npm install express-generator -g

// expressコマンドでアプリのひな形を生成します
express myapp

   create : myapp
   create : myapp/package.json
   create : myapp/app.js
   ...
   create : myapp/bin
   create : myapp/bin/www

// ファイルを移動
cd myapp 

// 依存モジュールをインストールします
npm install

// サーバーを起動します
npm start

// routingの設定
// app.jsにURLの根本となるものを作成
例)/users/ 〜というURLを使う場合
var usersRouter = require('./routes/users');
app.use('/users', usersRouter);
// routes/users.js に記載
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});
→ /users にアクセスすると文字が表示される

// req のメソッド
router.get('/', (req, res) => {
	// 何かの処理
});

req.body
request bodyのkey-valueペア(body-parser middlewareが必要)

req.cookies
cookieのkey-valueペア(cookie-parser middlewareが必要)

req.params
/books/:idで/books/1の場合req.params.id => 1
url pathパラメータのkey-valueペア

req.query
/books?order=ascの場合req.query.order => asc
リクエストパラメータのkey-valueペア

req.get
HTTPヘッダーの値を取得する

req.session
セッションのkey-valueペア(express-session middlewareが必要)

// 認証機能の追加
// passportはストラテジーと呼ばれる認証処理を通して認証を行います。
sudo npm i -g passport passport-local

// app.jsに追記
// ログイン情報認証部分
// ユーザー情報
const User1 = {
  username: "user1",
  password: "123"
};

const express = require('express');
const app = express();

const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;

app.use(express.static(path.join(__dirname, 'public')));

passport.use(new LocalStrategy(
  {
    // フィールド名が、username, password以外の場合.
    usernameField: 'login-id',
    passwordField: 'password',
    session: false,
  },
  (username, password, done) => {
    // 検証用コールバック.
    if (username !== User1.username) {
      return done(null, false);
    }
    if (password !== User1.password) {
      return done(null, false);
    }
    return done(null, username);
  }
));

app.use(passport.initialize());

// htmlから入力された情報を認証機能に渡す（body-parse は必要なくなったため不要）

// ログアウト部分
app.get('/logout', (request, response) => {
  request.logout();
  response.redirect('/');
});

// 各ページごとに認証を行う
sudo npm i -g express-session

// app.jsを追加
const session = require('express-session');
app.use(session({
    secret: 'keyboard cat',
    resave: true,
    saveUninitialized: false,
  }));

app.use(passport.session());  // <- app.use(passport.initialize()); より後に追加.

ログイン処理で記述したsessionをtrueにします。

{ successRedirect: 'ok.html',
  failureRedirect: 'login.html',
  session: true,
}
Copy
シリアライズメソッドとデシリアライズメソッドを作成します。

passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((user, done) => {
  done(null, user);
});
