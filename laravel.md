
# 備忘録（Laravel）

```
【アプリ作成時にメモリが不足した場合】
curl -sS https://getcomposer.org/installer | php
php composer.phar create-project "laravel/laravel" sample-app

【アプリの作成方法】
まず、laravelを始めるためにphpをインストールをする必要があるのだが、バージョン管理を行うものもあると後々バージョンが異なることによるエラーを抑えられるので、まずはphpのバージョン管理を行うもののインストールを行う。

その後で、phpをインストールしたのちにcomposer、laravelをインストール

その後、laravelアプリを新しく作成し、まずトップページを表示するところから始める。

Web.php → HomeController.php → index.blase.php

マイグレーションする際にデータベースのエンコードがutf8mb4だとエラーが発生
（データ量がテーブルを作成できる最大値を超えるため）

"Index column size too large. The maximum column size is 767 bytes"

# /etc/my.cnf
innodb_file_format=Barracuda
innodb_file_format_max=Barracuda
innodb_file_per_table=1
innodb_large_prefix=1

このようにすると、文字の規格が変更してデータ量が多くなってもエラーは発生しない
（再起動も忘れないようにすること）

$ sudo mysql.server restart

# database.yml

●修正前
        'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'strict' => true,
            'engine' => 'null',

●修正後
        'mysql' => [
            'driver' => 'mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'database' => env('DB_DATABASE', 'forge'),
            'username' => env('DB_USERNAME', 'forge'),
            'password' => env('DB_PASSWORD', ''),
            'charset' => 'utf8mb4',
            'collation' => 'utf8mb4_unicode_ci',
            'prefix' => '',
            'strict' => true,
            'engine' => 'InnoDB ROW_FORMAT=DYNAMIC',　★ここを修正

以上の変更を行ってから、データベースを作成する。
（仮に先に作成したテーブルがあれば、以下のコマンドを作成）
　php artisan migrate:fresh

・php artisan make:migration create_test_table
・php artisan migrate
・php artisan tinker
　→ データーベースに触れて操作ができる
　（DB::table("test")->get()　testテーブル内の情報を取得　）
　（DB::table('test')->insert(['text' => 'Insert'])　testテーブルに情報を保存　）
　（DB::table('test')->where('id', 1)->update(['text' => 'Update'])）

・管理画面を作成する際には、laravelのバージョンによって変更する方法が異なる。
 composer require laravel/ui
 php artisan ui vue --auth
 php artisan migrate
 npm install
 npm run dev

・モデルとマイグレーションファイルを作成（これで、コントローラーと中の関数が自動的に生成される）
php artisan make:model Tweet -m -c -r

・マイグレーションファイルを作成したら、マイグレーションを行う
php artisan migrate

・コントローラーを作成
php artisan make:controller Auth/TimelineController

※ コントローラー内でバリデーション（条件判断して値を抜き出す）
　→ railsで言うpermitとvalidatesを組み合わせた感じ

・ログイン画面の作成
  composer require laravel/ui --dev
  php artisan ui vue --auth
（viewの形式も整理された状態になる）

・pug のインストール（マークアップ言語）
　composer require bkwld/laravel-pug

※ web.php の中でコントローラー名が見つかれないというエラーが発生した場合・・・

app/Providers/RouteServiceProvider.php

```
protected $namespace = 'App\Http\Controllers'; //追加
public function boot()
    {
        $this->configureRateLimiting();

        $this->routes(function () {
            Route::middleware('web')
                ->namespace($this->namespace) //追加
                ->group(base_path('routes/web.php'));

            Route::prefix('api')
                ->middleware('api')
                ->namespace($this->namespace) //追加
                ->group(base_path('routes/api.php'));
        });
    }
```