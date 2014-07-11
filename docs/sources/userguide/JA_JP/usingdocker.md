page_title: Working with Containers
page_description: Learn how to manage and operate Docker containers.
page_keywords: docker, the docker guide, documentation, docker.io, monitoring containers, docker top, docker inspect, docker port, ports, docker logs, log, Logs

# コンテナを扱う

[前節](/userguide/dockerizing)で，我々は最初のコンテナを立ち上げました。`docker run`コマンドを使って二つのコンテナを起動しました。

* フォアグラウンドで起動したインタラクティブなコンテナ
* バックグラウンドでデーモン化したコンテナ

その過程でいくつかのDockerコマンドを学びました。

* `docker ps` - コンテナを一覧表示する。
* `docker logs` - コンテナの標準出力を表示する。
* `docker stop` - 実行中のコンテナを停止する。

> **Tip:**
> [インタラクティブチュートリアル](https://www.docker.io/gettingstarted)でも`docker`コマンドを学ぶ事ができます。

`docker`クライアントは非常にシンプルです。Dockerに対して行う事ができる操作はコマンドになっており，各コマンドは一連のフラグと引数を取ることができます。

    # Usage:  [sudo] docker [flags] [command] [arguments] ..
    # Example:
    $ docker run -i -t ubuntu /bin/bash

`docker version`コマンドを使って，実際に見てみましょう。このコマンドは，現在インストールされているDockerクライアントとデーモンのバージョン情報を返却します。

    $ sudo docker version

このコマンドはDockerクライアントとデーモンのバージョン情報だけで無く，Go（Dockerを動かしているプログラミング言語）のバージョンも表示しています。


    Client version: 0.8.0
    Go version (client): go1.2

    Git commit (client): cc3a8c8
    Server version: 0.8.0

    Git commit (server): cc3a8c8
    Go version (server): go1.2

    Last stable version: 0.8.0

### Dockerクライアントができる事を見てみる

`docker`バイナリをオプションなしで実行すると，利用できる全てのコマンドを表示する事ができます。

    $ sudo docker

利用可能な全てのコマンドを見ることができます。

    Commands:
         attach    Attach to a running container
         build     Build an image from a Dockerfile
         commit    Create a new image from a container's changes
    . . .

### コマンドの使い方を知る

各Dockerコマンドの詳細を調べることもできます。

`[command]`を後ろに付けて`docker`を実行すると，そのコマンドの使用方法が表示されます。

    $ sudo docker attach
    Help output . . .

あるいは`--help`フラグをつけても使用方法を確認することが可能です。

    $ sudo docker images --help

こうすると，ヘルプ文言と全ての使用可能なフラグが表示されます。

    Usage: docker attach [OPTIONS] CONTAINER

    Attach to a running container

      --no-stdin=false: Do not attach stdin
      --sig-proxy=true: Proxify all received signal to the process (even in non-tty mode)

> **注釈**
> Dockerの完全なコマンドの一覧は[ここ](/reference/commandline/cli/)で見ることができます。

## DockerでWebアプリケーションを動かす

ここまで，`docker`クライアントについて更に少し学んで来ました。もっと多くのコンテナを動かすという重要なステップに移行しましょう。これまで我々が実行したコンテナは特に有用な事は何もしていませんが，WebアプリケーションをDockerで動かす事でこの経験を積むことにしましょう。

今回は，PythonのライブラリFlaskを使ってWebアプリケーションを作ります。`docker run`から始めましょう。

    $ sudo docker run -d -P training/webapp python app.py

このコマンドが何をしたか見直してみましょう。`-d`と`-P`という二つのフラグを指定しています。`-d`フラグは既に見た通り，コンテナをバックグラウンドで実行するためのものでした。新たに出てきた`-P`フラグは，コンテナ内で要求された全てのポートをホストのポートにマッピングします。これでWebアプリケーションを見ることができるようになります。

また，`training/webapp`というイメージを指定しました。これは我々が作っておいたイメージで，簡単なFlaskのWebアプリケーションが入っています。

最後に，コンテナに`python app.py`というコマンドを実行するように指定しました。このコマンドでWebアプリケーションが起動します。

> **注釈**
> `docker run`コマンドの詳細な情報は，[コマンドリファレンス](/reference/commandline/cli/#run)と[Docker Runリファレンス](/reference/run/)で確認できます。

## Webアプリケーションコンテナを見る

では，`docker ps`コマンドでコンテナを見てみましょう。

    $ sudo docker ps -l
    CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
    bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse

`docker ps`コマンドにフラグ`-l`を新たに追加したことがわかるでしょう。このフラグを追加すると，`docker ps`コマンドは**最後に**起動したコンテナの詳細な情報を表示します。

> **注釈**
> デフォルトでは，`docker ps`コマンドは起動中のコンテナの情報しか表示しません。停止中のコンテナの情報も確認したければ，`-a`フラグを追加してください。

[初めてコンテナをDocker化した際](/userguide/dockerizing)に見たコンテナの詳細情報に加え，`PORTS`列に追加された重要な情報を確認できます。

    PORTS
    0.0.0.0:49155->5000/tcp

`-P`フラグを付けて`docker run`コマンドを実行した場合，イメージが外部に公開したポートはホストのポートにマッピングされます。

> **注釈**
> [どうやってイメージをビルドするか]を学ぶ時に，ポートを外部に公開する詳細な方法について見ることになります。

この場合，Dockerはポート5000（Flaskのデフォルトポートです）をホストの49155番ポートにマッピングしています。

Dockerのポートバインディングは，多様な設定が行えます。この例で`-P`フラグは，コンテナのポート5000番を高いポート（49000から49900まで）にマッピングするという意味の`-p 5000`のショートカットです。`-p`フラグを使って，コンテナのポートを特定のポートにマッピングするように指定することもできます。例えば，こうします。

    $ sudo docker run -d -p 5000:5000 training/webapp python app.py

これはコンテナ内の5000番ポートをホストの5000番ポートにマッピングします。あなたはこう質問するかもしれません。なぜ高いポートにマッピングするのでは無く，ホストとコンテナを1:1にポートマッピングしないんだ，と。共に5000番ポートにコンテナ内でバインドされた二つのPythonアプリケーションをテストしたいとします。Dockerのポートマッピングが無ければ，一度に片方しかアクセスすることができませんね。

では，ブラウザでポート49155番を指定して見てみましょう。

![Viewing the web application](/userguide/webapp1.png).

Pythonアプリケーションが動いていますね！

> **注釈**
> OSXでboot2dockerバーチャルマシンをOS X，WindowsあるいはLinuxで使った場合，ローカルホストではなく仮想ホストのIPを取得する必要があります。これにはboot2dockerシェルで次の様にします。
>
>     $ boot2docker ip
>     The VM's Host only interface IP address is: 192.168.59.103
>
> この場合，上記の例でhttp://192.168.59.103:49155をブラウザで見ることになります。

## ポートのショートカット

`docker ps`コマンドでマッピングされたポートを調べるのはちょっとややこしいので，Dockerは便利なショートカット，`docker port`を用意しています。`docker port`コマンドはコンテナのIDまたは名前と，対応する公開ポートを知りたいポート番号を指定して使用します。

    $ sudo docker port nostalgic_morse 5000
    0.0.0.0:49155

この例では，コンテナ内のポート5000番が外部の何番ポートにマッピングされているかを確認しました。

## Webアプリケーションのログを見る

我々のアプリケーションで何が起こっているのか，もう少し詳しく見てみましょう。また，既に学んだ`docker logs`コマンドのまた別の使用法も見てみます。

    $ sudo docker logs -f nostalgic_morse
    * Running on http://0.0.0.0:5000/
    10.0.2.2 - - [23/May/2014 20:16:31] "GET / HTTP/1.1" 200 -
    10.0.2.2 - - [23/May/2014 20:16:31] "GET /favicon.ico HTTP/1.1" 404 -

新たに`-f`フラグを追加しました。これによって`docker logs`コマンドが`tail -f`コマンドのように働き，コンテナの標準出力を監視するようになります。ここでは，ポート5000番でアプリケーションが動いているという行とアクセスログをFlaskが出力しているのを確認できます。

## コンテナのプロセスを見る

コンテナのログに加え，コンテナ内で動くプロセスの情報を確認することも`docker top`コマンドによって可能です。

    $ sudo docker top nostalgic_morse
    PID                 USER                COMMAND
    854                 root                python app.py

`python app.py`コマンドがコンテナ内で動いている唯一のプロセスだと確認できました。

## コンテナを検査する

最後に，`docker inspect`コマンドを使ってDockerの低レベルな領域に飛び込む事ができます。このコマンドはDockerコンテナの設定とステータスをJSON形式で返却します。

    $ sudo docker inspect nostalgic_morse

JSON出力の例を見てみましょう。

    [{
        "ID": "bc533791f3f500b280a9626688bc79e342e3ea0d528efe3a86a51ecb28ea20",
        "Created": "2014-05-26T05:52:40.808952951Z",
        "Path": "python",
        "Args": [
           "app.py"
        ],
        "Config": {
           "Hostname": "bc533791f3f5",
           "Domainname": "",
           "User": "",
    . . .

要素を指定する事で，返却する情報を絞り込むこともできます。例えば，コンテナのIPを取得したい場合はこうします。

    $ sudo docker inspect -f '{{ .NetworkSettings.IPAddress }}' nostalgic_morse
    172.17.0.5

## コンテナを停止する

コンテナ上のWebアプリケーションがちゃんと動いていることは確認できました。今度は`docker stop`コマンドを使って，我々のコンテナ`nostalgic_morse`を停止してみましょう。

    $ sudo docker stop nostalgic_morse
    nostalgic_morse

コンテナが停止したかどうか`docker ps`コマンドを使って確認できます。

    $ sudo docker ps -l

## コンテナを再起動する

おおっと，コンテナを停止した直後に電話がかかってきました。別の開発者はこのコンテナが必要だそうです。対処するには二つの選択肢があります。新しくコンテナを作るか，古いコンテナを再起動するか，です。コンテナが復帰するのを確認してみましょう。

    $ sudo docker start nostalgic_morse
    nostalgic_morse

`docker ps -l`を実行してコンテナが復活したことを確認するか，ブラウザでURLを開いてアプリケーションが反応するか確認してください。

> **注釈**
> `docker restart`コマンドもこの状況で使えます。このコマンドはコンテナを一度停止してから起動します。

## コンテナを削除する

あなたの同僚が作業を終え，もうコンテナが必要無い事を知らせて来ました。`docker rm`コマンドを使って削除しましょう。

    $ sudo docker rm nostalgic_morse
    Error: Impossible to remove a running container, please stop it first or use -f
    2014/05/24 08:12:56 Error: failed to remove one or more containers

何が起きたのでしょう？実は，実行中のコンテナは削除できないのです。これにより，意図せず実行中の必要なコンテナを削除する事を防ぎます。では，コンテナを停止してからもう一度削除してみましょう。


    $ sudo docker stop nostalgic_morse
    nostalgic_morse
    $ sudo docker rm nostalgic_morse
    nostalgic_morse

これでコンテナが停止され，削除されました。

> **注釈**
> コンテナを削除するのは常に最後の手だと忘れないで下さい！

# 次のステップ

これまで，[Docker Hub](https://hub.docker.com)からダウンロードしたイメージだけを取り扱って来ましたが，自らイメージを作り，共有する方法を学びましょう。

[Dockerイメージを扱う](/userguide/dockerimages)に進んで下さい。
