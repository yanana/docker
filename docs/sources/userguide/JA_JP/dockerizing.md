page_title: アプリケーションをDocker化する："Hello world"
page_description: A simple "Hello world" exercise that introduced you to Docker.
page_keywords: docker guide, docker, docker platform, virtualization framework, how to, dockerize, dockerizing apps, dockerizing applications, container, containers

# アプリケーションをDocker化する："Hello world"

**結局このDockerというのは一体何なんだ？**

Dockerを使うとコンテナ内でアプリケーションを実行する事ができます。`docker run`とコマンドを打つだけです。

## Hello world

実際にやってみましょう。

    $ sudo docker run ubuntu:14.04 /bin/echo 'Hello world'
    Hello world

これで最初のコンテナが起動されました！

では，一体何が起こったのでしょうか？`docker run`コマンドが実際に行った事を追いかけてみましょう。

まず，`docker`バイナリ，そして実行させたい命令，すなわち`run`を指定しました。これらの組み合わせ，`docker run`はコンテナを**実行**します。

続いて，`ubuntu:14.04`というイメージを指定しました。これは先ほど実行したコンテナの基になるもので，今回はUbuntu 14.04のOSイメージを利用しました。

イメージを指定すると，DockerはまずあなたのDockerホストにそのイメージが無いか探します。もし見つからなければ，パブリックなイメージリポジトリである[Docker Hub](https://hub.docker.com)からダウンロードします。

次に，コンテナの中で何を実行するかを指定しました。

    /bin/echo 'Hello world'

コンテナが起動すると，DockerはUbuntu 14.04の環境を新たに作り，`/bin/echo`コマンドをその中で実行しました。我々は以下の実行結果をコマンドラインで確認しました。

    Hello world

何が起こったのでしょうか？Dockerコンテナは，指定されたコマンドが実行中の間だけ有効となります。この場合，`Hello world`がが表示されると，コンテナは停止します。


# インタラクティブコンテナ

`docker run`コマンドを再度実行してみましょう。今度は別の命令を出します。

    $ sudo docker run -t -i ubuntu:14.04 /bin/bash
    root@af8bae53bdd3:/#

前回と同様に`docker run`コマンドを指定し，`ubunt:14.04`イメージを起動しましたが，今回は`-t`と`-i`という二つのフラグを渡しています。`-t`フラグは擬似端末（pseudo-tty）かコンソール端末を割り当てます。`-i`フラグはコンテナの標準入力（`STDIN`）をつかむ事によってインタラクティブな接続を実現します。

また，新たなコマンドも追加しました。`/bin/bash`です。これによってBashシェルがコンテナ内で起動します。

コンテナが起動すると，コンテナ内でコマンドプロンプトが立ち上がったことが見て取れます。

    root@af8bae53bdd3:/#

コンテナ内でコマンドを実行してみましょう。

    root@af8bae53bdd3:/# pwd
    /
    root@af8bae53bdd3:/# ls
    bin boot dev etc home lib lib64 media mnt opt proc root run sbin srv sys tmp usr var

`pwd`を実行してカレントディレクトリが表示され，`/`，すなわちルートディレクトリにいることがわかります。また，ディレクトリ配下のファイルを一覧して，典型的なLinuxのファイルシステムであることがわかりました。

コンテナ内で更に遊ぶのもいいでしょう。やることを終えたら，`exit`コマンドでターミナルを抜けましょう。

    root@af8bae53bdd3:/# exit

今まで見てきたコンテナと同様に，Bashシェルのプロセスが終了すると，コンテナが停止します。

## Hello worldをデーモン化する

コマンドを実行して終了するコンテナは，使えない事は無いけれど，あまり便利とは言えません。Dockerを使って実行する事になる殆どのコンテナの様に，デーモンとして動くコンテナを作って見ましょう。

これも`docker run`コマンドでできます。

    $ sudo docker run -d ubuntu:14.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"
    1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147

おや？"Hello world"はどこに行ったんでしょうか？何をしているのか見てみましょう。とてもわかりやすいです。`docker run`を実行しましたが，今回は`-d`フラグを指定しました。`-d`フラグはコンテナを実行した後，そのプロセスをデーモン化するためにディタッチする様Dockerに伝えます。

先ほどと同様に，イメージは`ubuntu:14.04`を指定しました。

最後に，実行するコマンドを指定しました。

    /bin/sh -c "while true; do echo hello world; sleep 1; done"

これは最も下らないデーモン in the (hello) world で，`hello world`を永遠にエコーし続けます。

では，なぜ`hello world`がどこにも見当たらないのでしょうか？代わりにDockerはとても長い文字列を返しました。

    1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147

この非常に長い文字列は**コンテナID**と呼ばれるもので，コンテナを一意に識別するものです。

> **注釈**
>
> コンテナIDは少し長くて不格好ですが，もう少し読み進めると，短いIDが登場します。また，コンテナに名前を付けてより簡単にコンテナを取り扱う方法も出てきます。

このコンテナIDを使って，我々の`hello world`デーモンの身に何が起きているのか確認する事ができます。

では，まずコンテナが動いていることを確認しましょう。`docker ps`コマンドを使います。`docker ps`コマンドはDockerデーモンに自分が管理している全てのコンテナの情報を問い合わせます。

    $ sudo docker ps
    CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES
    1e5535038e28  ubuntu:14.04  /bin/sh -c 'while tr  2 minutes ago  Up 1 minute        insane_babbage

デーモン化したコンテナを確認できますね。`docker ps`は有用な情報を提供してくれました。また，この情報はコンテナIDの短い変化形，`1e5535038e28`で始まっています。

このコンテナをビルドするのに用いたイメージの`ubuntu:14.04`や，実行中のコマンド，現在のステータス，そして自動的に割り当てられた名前である`insane_babbage`を見出す事ができます。

> **注釈**
>
> Dockerは自動的に全てのコンテナに名前を付けますが，少し進むと自分で名前を指定する方法がわかります。

では，これでデーモン化したコンテナが動いていることを無事に確認できました。しかし，我々がやるように命令した事をちゃんとしているのでしょうか？確認するには，`docker logs`コマンドを使ってコンテナの内部を確認します。Dockerが割り振ったコンテナ名を使ってみましょう。

    $ sudo docker logs insane_babbage
    hello world
    hello world
    hello world
    . . .

`docker logs`コマンドはコンテナの中に入り込み，その標準出力を返します。今回は，`hello world`コマンドの出力ですね。

すばらしい！我々のデーモンはちゃんと動いており，初めてのDocker化したアプリケーションができあがりました！

こうして我々は自分でコンテナを作るという偉業を達成しました。では，デーモン化したコンテナを止めて後片付けをしましょう。`docker stop`コマンドを使います。

    $ sudo docker stop insane_babbage
    insane_babbage

`docker stop`コマンドは，実行中のコンテナを丁寧に停止するように命令します。成功したら，停止したコンテナの名前を返します。

ちゃんと止まったかどうか，`docker ps`コマンドで確認しましょう。

    $ sudo docker ps
    CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES

完璧です。コンテナは停止しました。

# 次のステップ

Dockerを始めるのはいかにシンプルかという事を見てきました。次は，もう少し進んだ事をするための方法を学びます。

[コンテナを扱う](/userguide/usingdocker)へ進む。
