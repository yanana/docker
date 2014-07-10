page_title: コンテナをリンクする
page_description: Learn how to connect Docker containers together.
page_keywords: Examples, Usage, user guide, links, linking, docker, documentation, examples, names, name, container naming, port, map, network port, network

# コンテナをリンクする

[Dockerを使う](/userguide/usingdocker)の節でネットワークポートを経由してコンテナ内で稼働しているサービスにアクセスする事に触れました。これはDockerコンテナの内部で動くサービスやアプリケーションとやりとりする方法の一つです。本節では，コンテナにネットワークポート経由で接続する方法の復習と，コンテナリンクのコンセプトを紹介します。

## ポートマッピングの復習

[Dockerを使う](/userguide/usingdocker)節でFlaskアプリケーションを動かすコンテナを作成しました。

    $ sudo docker run -d -P training/webapp python app.py

> **注釈**
> コンテナは内部のネットワークとIPアドレスを持っています（[Dockerを使う](/userguide/usingdocker/)で`docker inspect`コマンドを使ってコンテナのIPアドレスを調べた事を思い出してください）。Dockerは様々なネットワーク設定を行う事が可能です。Dockerのネットワークについては，[ここ](/articles/networking/)でより詳細な情報を見る事ができます。

このコンテナを作った際，`-P`フラグを使ってホストの49000番から49900番までの間のランダムなポートをコンテナのポートにマッピングしました。その後，`docker ps`コマンドを実行してコンテナのポート5000番がホストのポート49155番にマッピングされている事を確認しました。

    $ sudo docker ps nostalgic_morse
    CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
    bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse

`-p`フラグを使ってコンテナのポートホストの指定したポートにマッピングできる事も確認しました。

    $ sudo docker run -d -p 5000:5000 training/webapp python app.py

そしてまた，こうしてポートマッピングを行う事があまりいい方法では無いという事も学びました。なぜなら，この特定のポートにただ一つのコンテナを結びつけてしまうからです。

`-p`フラグを使って設定する方法が他にも少しだけあります。`-p`フラグはデフォルトでホスト上の全ネットワークインターフェースにポートマッピングを行いますが，マッピングを特定のインターフェースに限定する事も可能です。`localhost`にだけマッピングする例を見てみましょう。

    $ sudo docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py

このコマンドは，コンテナのポート5000番をホストの`localhost`あるいは`127.0.0.1`のポート5000番にマッピングします。

あるいは，コンテナのポート5000番をホストのポートに動的に割り振りたいが，`localhost`上でのみマッピングをしたい場合はこうします。

    $ sudo docker run -d -p 127.0.0.1::5000 training/webapp python app.py

ポート指定の後ろに`/udp`を追加する事でUDPポートをマッピングする事もできます。

    $ sudo docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py

現在のポートマッピングがどうなっているかを調べる，便利な`docker port`コマンドにも触れました。これはまた特定のポートに関する設定を表示するのにも有用です。例えば，コンテナのポートをホストの`localhost`にバインドした場合，`docker port`は次の様な出力をします。

    $ docker port nostalgic_morse 5000
    127.0.0.1:49155

> **注釈**
> `-p`フラグを複数使って，複数のポートマッピングを指定できます。


## Dockerコンテナリンク

Dockerコンテナが別のものに接続する方法はポートマッピングだけではありません。Dockerはコンテナを相互にリンクさせ，接続情報を互いに共有する手段も持ち合わせています。Dockerコンテナリンクは親コンテナが子コンテナの限定された情報を参照できるという親子関係を作ります。

## コンテナの命名
## Container naming

このリンク作成を行うために，Dockerはコンテナの名前に依存します。各コンテナは自動生成された名前を割り当てられる事を既に見ました。実際，私たちはこのガイドを通じて古くからの友人`nostalgic_morse`とすっかり仲良くなりましたね。コンテナを自分で命名することもできます。命名には二つの価値ある機能があります。

1. Webアプリケーションを内部に持つコンテナを`web`と命名するように，機能に即した名前をコンテナに付けることでコンテナを覚えやすくなります。

2. Dockerに


2. It provides Docker with a reference point that allows it to refer to other
   containers, for example link container `web` to container `db`.

`--name`フラグを使ってコンテナに名前を付ける事ができます。例えば，

    $ sudo docker run -d -P --name web training/webapp python app.py

`--name`フラグを使ってコンテナを`web`と命名して，新たにコンテナを起動した事がわかりますね。コンテナの名前は`docker ps`コマンドを使って確認できます。

    $ sudo docker ps -l
    CONTAINER ID  IMAGE                  COMMAND        CREATED       STATUS       PORTS                    NAMES
    aed84ee21bde  training/webapp:latest python app.py  12 hours ago  Up 2 seconds 0.0.0.0:49154->5000/tcp  web

`docker inspect`コマンドを使ってコンテナの名前を取得することもできます。

    $ sudo docker inspect -f "{{ .Name }}" aed84ee21bde
    /web

> **注釈**
> コンテナ名はユニークでなければなりません。つまり，一つのコンテナしか`web`と呼ぶ事はできないということです。もしコンテナ名を再利用したければ，`docker rm`コマンドで古いコンテナを削除してから，同じ名前のコンテナを新規に作成する必要があります。こうする代わりに，`--rm`フラグを付けて`docker run`コマンドを実行しても同じ事ができます。この方法では，コンテナを停止して即これを削除します。


## コンテナリンク
## Container Linking

リンクによって，コンテナはお互いを見つけ，セキュアに通信する事ができます。リンクを作成するには，`--link`フラグを使います。では，新しいコンテナを作って見ましょう。今度はデータベースです。

    $ sudo docker run -d --name db training/postgres

`training/postgres`イメージを使って，PostgreSQLが稼働する`db`という名前のコンテナを作成しました。

次に，リンクしたコンテナで置き換えられるよう，前に作った`web`コンテナを削除しておきましょう。

    $ docker rm -f web

では，`web`というコンテナを新たに作り，`db`コンテナとリンクしてみましょう。

    $ sudo docker run -d -P --name web --link db:db training/webapp python app.py

これで，`web`コンテナと`db`コンテナがリンクされました。`--link`フラグは次の形を取ります。

    --link name:alias

`name`にはリンクする対象のコンテナの名前を，`alias`にはリンクのエイリアスを設定します。エイリアスがどのように使用されるのかはすぐにわかります。

リンクしたコンテナを`docker ps`で見てみましょう。

    $ docker ps
    CONTAINER ID  IMAGE                     COMMAND               CREATED             STATUS             PORTS                    NAMES
    349169744e49  training/postgres:latest  su postgres -c '/usr  About a minute ago  Up About a minute  5432/tcp                 db, web/db
    aed84ee21bde  training/webapp:latest    python app.py         16 hours ago        Up 2 minutes       0.0.0.0:49154->5000/tcp  web

`db`と`web`が確認できます。そして，`db`コンテナの`NAMES`列に`web/db`があるのがわかります。これはつまり，`web`コンテナが`db`コンテナに親子関係でリンクしているという事です。

では，コンテナをリンクさせると何が起きるのでしょうか？リンクはコンテナ間に親子関係を生じるという事を既に確認しました。親コンテナ，この場合は`web`，は子コンテナ`db`の情報にアクセスできます。これを実現するため，いかなるポートもコンテナ外に公開する必要無しに，Dockerはコンテナ間にセキュアトンネルを作成します。`db`コンテナを起動した際，`-P`フラグも`-p`フラグも指定しなかった事に気付いたと思います。コンテナ間をリンクしているため，PostgreSQLデータベースをネットワーク上で公開する必要が無いのです。

Dockerは子コンテナ内で，親コンテナに提供するための接続情報を二つの方法で公開します。

* 環境変数
* `/etc/hosts`ファイルを更新する

最初に，Dockerが設定する環境変数を見てみましょう。`env`コマンドを叩いてコンテナの環境変数を一覧してみます。

```
    $ sudo docker run --rm --name web2 --link db:db training/webapp env
    . . .
    DB_NAME=/web2/db
    DB_PORT=tcp://172.17.0.5:5432
    DB_PORT_5000_TCP=tcp://172.17.0.5:5432
    DB_PORT_5000_TCP_PROTO=tcp
    DB_PORT_5000_TCP_PORT=5432
    DB_PORT_5000_TCP_ADDR=172.17.0.5
    . . .
```

> **注釈**
> これらの環境変数はコンテナ内の最初のプロセスにだけ設定されます。同様に，ある種のデーモン（`sshd`の様な）は，コネクションのためのシェルを生成する際に環境変数を削除します。

`db`コンテナに関する有用な情報を含む，一連の環境変数をDockerが作成したことを確認できます。各変数の名前は先頭に`DB_`が付与されていますが，これはコンテナリンクを作成する際に設定した`alias`から作られたものです。もし`alias`が`db1`だったら，環境変数の接頭辞は`DB1_`になっています。これらの環境変数を使って，`db`コンテナ上のデータベースに接続するための設定をアプリケーション上で行う事ができます。このコネクションはセキュアで，プライベートかつリンクされた`web`コンテナのみが`db`コンテナに接続が可能です。

環境変数に加え，Dockerはリンクした親コンテナのホスト情報を`/etc/hosts`に追加します。`web`コンテナ上の`/etc/hosts`を見てみましょう。

    root@aed84ee21bde:/opt/webapp# cat /etc/hosts
    172.17.0.7  aed84ee21bde
    . . .
    172.17.0.5  db

二つの関連するホスト情報を見ることができます。最初のものは`web`コンテナのもので，ホスト名としてコンテナIDを利用しています。二つ目のものは，リンクのエイリアスを使って`db`コンテナのIPアドレスを参照します。では，このホスト名でpingを飛ばしてみましょう。

    root@aed84ee21bde:/opt/webapp# apt-get install -yqq inetutils-ping
    root@aed84ee21bde:/opt/webapp# ping db
    PING db (172.17.0.5): 48 data bytes
    56 bytes from 172.17.0.5: icmp_seq=0 ttl=64 time=0.267 ms
    56 bytes from 172.17.0.5: icmp_seq=1 ttl=64 time=0.250 ms
    56 bytes from 172.17.0.5: icmp_seq=2 ttl=64 time=0.256 ms

> **注釈**
> コンテナにインストールされていなかったため，`ping`コマンドをインストールする必要がありました。

`db`コンテナにpingを飛ばすため，`172.17.0.5`に解決する`db`コンテナのhostsファイルのエントリを使って`ping`コマンドを実行しました。アプリケーションから`db`コンテナを利用するのに，このホスト設定を活用することができます。

> **注釈**
> 一つの親コンテナに複数の子コンテナをリンクさせる事が可能です。例えば，`db`コンテナに複数のWebアプリケーションのコンテナをリンクさせる事ができます。


# 次のステップ

これでコンテナをリンクさせる方法がわかりました。次のステップで，データとボリュームを管理し，コンテナ内でマウントする方法を学びましょう。


[コンテナのデータ管理](/userguide/dockervolumes)に進む。
