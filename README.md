# Marionette.jsを使ったサンプル

## 環境設定

Grunt + Bower + yeomanのgeneratorを使ってスケルトンを生成する。

手順は[genetarot-marionette](https://github.com/mrichard/generator-marionette)のInstallセクションに従う。

### MongoDBをインストールする

すでにインストール済み。Ubuntuではmongodbパッケージをインストールすれば一式がインストールされる。

### Node.jsとnpmをインストールする

すでにインストール済み。

### phantomJSをインストールする

Ubuntu のパッケージでもインストール可能だが、12.04 LTSではバージョンが古過ぎる。

phantomJSの公式サイト http://phantomjs.org/ からダウンロードしてインストールする。公式サイトの[ダウンロードページ](http://phantomjs.org/download.html)から、Linux用バイナリパッケージ(32ビット用と64ビット用がある)をダウンロードする。

ここでは[32ビット用バイナリパッケージ](https://phantomjs.googlecode.com/files/phantomjs-1.9.7-linux-i686.tar.bz2)をダウンロードした。アーカイブを展開するとbinサブディレクトリの中にバイナリファイルphantomjsが入っているので、/usr/local/binにコピーすればよい。

    $ wget https://phantomjs.googlecode.com/files/phantomjs-1.9.7-linux-i686.tar.bz2
    $ tar xvjf phantomjs-1.9.7-linux-i686.tar.bz2
    $ sudo cp phantomjs-1.9.7-linux-i686/bin/phantomjs  /usr/local/bin

### Yoeman, Grunt, Bowerをインストールする

    $ npm install -g yo grunt-cli bower

### mocha-phantomjsをインストール

    $ npm install -g mocha-phantomjs

### mochaジェネレータをインストール

    $ npm install -g generator-mocha-amd

### marionetteジェネレータをインストール

    $ npm install -g generator-marionette

### プロジェクトを作って実行してみる

    $ yo marionette

生成したアプリケーションを実行する

    $ grunt

ここでcompass/rubyが無いと言わるので、インストールする

    $ gem update --system
    $ gem install compass

再びgruntを実行すると、expressがエラーを出している。

    Running "express:dev" (express) task
    Starting background Express server
    
    events.js:72
            throw er; // Unhandled 'error' event
                  ^
    Error: listen EADDRINUSE
        at errnoException (net.js:901:11)
        at Server._listen2 (net.js:1039:14)
        at listen (net.js:1061:10)
        at Server.listen (net.js:1127:5)
        at NativeConnection.callback (/home/cond/proj/marionette-1/server/app.js:60:25)
        at NativeConnection.g (events.js:175:14)
        at NativeConnection.EventEmitter.emit (events.js:92:17)
        at open (/home/cond/proj/marionette-1/node_modules/mongoose/lib/connection.js:446:10)
        at NativeConnection.Connection.onOpen (/home/cond/proj/marionette-1/node_modules/mongoose/lib/connection.js:454:5)
        at /home/cond/proj/marionette-1/node_modules/mongoose/lib/connection.js:414:10

listen EADDRINUSEなので、expressが使おうとしたポート9000をすでに誰かが使っているというエラー。調べると、ポート9000は、Nginxとphpサーバの通信に使われている。

    $ sudo lsof -i:9000
    COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
    php5-fpm 1109     root    6u  IPv4   9551      0t0  TCP localhost.localdomain:9000 (LISTEN)
    php5-fpm 1113 www-data    0u  IPv4   9551      0t0  TCP localhost.localdomain:9000 (LISTEN)
    php5-fpm 1114 www-data    0u  IPv4   9551      0t0  TCP localhost.localdomain:9000 (LISTEN)
    php5-fpm 1115 www-data    0u  IPv4   9551      0t0  TCP localhost.localdomain:9000 (LISTEN)
    php5-fpm 1118 www-data    0u  IPv4   9551      0t0  TCP localhost.localdomain:9000 (LISTEN)
    $

ポート9000ではなく9001を使うことにした。

変更は2か所。それぞれ9000を9001に変更する。

    diff --git a/Gruntfile.js b/Gruntfile.js
    index 17ab709..120d6f7 100644
    --- a/Gruntfile.js
    +++ b/Gruntfile.js
    @@ -82,7 +82,7 @@ module.exports = function (grunt) {
             express: {
                 options: {
                     // Override defaults here
    -                port: '9000'
    +                port: '9001'
                 },
                 dev: {
                     options: {
    diff --git a/server/app.js b/server/app.js
    index 01ba505..9d58bf6 100644
    --- a/server/app.js
    +++ b/server/app.js
    @@ -32,7 +32,7 @@ db.once('open', function callback () {
            var app = express();

            app.configure(function(){
    -       app.set('port', 9000);
    +       app.set('port', 9001);

                app.set('view engine', 'handlebars');
                app.set('views', __dirname + '../app/scripts/views');

再びgruntコマンドを実行すると、ブラウザが立ち上がり、「CONGRATS!」という画面が表示される。

これでOK。

### Gitにコミットする

yeomanのジェネレータは.gitignoreファイルも生成してくれる。だから、yo marionetteした状態で

    $ git add .
    $ git commit -v

を実行すればファイル一式をコミットできる。

この際に、.gitignoreに自分用の設定を追加すればよい。

    *~
    #*

## Gitリポジトリからcloneして開発を始める手順

### リポジトリをcloneする

    $ git clone git@github.com:cond/marionette-1.git

### 作業ディレクトリに移動する

    $ cd marionette-1

### 「環境設定」の手順に従って必要な環境を整える。

npm install -gでインストールしたものは、すでに入っていると仮定。

### 必要なnpmパッケージをインストール

このコマンドはpackage.jsonで指定されているパッケージをインストールしてくれる。

    $ npm install

### bowerパッケージをインストール

    $ bower install

### gruntを実行して確認

これで開発できる環境が出来上がっている。gruntを実行して確認する。

    $ grunt
