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

ここでは[32ビット用バイナリパッケージ](https://phantomjs.googlecode.com/files/phantomjs-1.9.2-linux-i686.tar.bz2)をダウンロードした。アーカイブを展開するとbinサブディレクトリの中にバイナリファイルphantomjsが入っているので、/usr/local/binにコピーすればよい。

    $ wget https://phantomjs.googlecode.com/files/phantomjs-1.9.2-linux-i686.tar.bz2
    $ tar xvjf phantomjs-1.9.2-linux-i686.tar.bz2 
    $ sudo cp phantomjs-1.9.2-linux-i686/bin/phantomjs  /usr/local/bin
