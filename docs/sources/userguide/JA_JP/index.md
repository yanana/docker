page_title: Dockerユーザーガイド
page_description: Dockerユーザーガイド
page_keywords: docker, introduction, documentation, about, technology, docker.io, user, guide, user's, manual, platform, framework, virtualization, home, intro

# Dockerユーザーガイドへようこそ

[Introduction](/)を読んで，Dockerがいったい何をして，どのように動くのかがなんとなくわかったのではないでしょうか。このガイドでは，Dockerを使って自分の環境にそれを適用するための基礎を提供します。

このガイドでは，Dockerを使って以下の要件を実現するための方法を学ぶ事ができます。

* あなたのアプリケーションをDocker化（Dockerize）する
* 自分のコンテナを実行する
* Dockerイメージを作成する 
* 自分で作ったDockerイメージをシェアする
* その他もろもろ

Dockerのライフサイクルに沿って追いかけられるように，ガイドを大きな章に分けました。

## Docker Hubを使ってみよう

**Docker Hubはどうやって使えばいいの？**

Docker HubはDockerのハブです。パブリックなDockerイメージをホストし，Docker環境をビルド・管理する手助けをします。より深く学ぶためには，こちらをご参照ください。

[Using Docker Hub](/userguide/dockerhub)を見て下さい。


## アプリケーションをDocker化してみよう："Hello world"

**コンテナ内でアプリケーションを実行するにはどうすればいいの？**

Dockerは**コンテナベースの**仮想環境を提供し，あなたのアプリケーションを支援します。アプリケーションをDocker化して実行するには，[Dockerizing Applications](/userguide/dockerizing)をご覧ください。


## コンテナとのつきあい方

**コンテナをどうやって管理すればいいんだろう？**

Dockerコンテナでアプリケーションを動かす事がわかったら，そのコンテナをどうやって管理すればいいのかお教えします。コンテナを調査・モニター，管理する方法を知りたければ，こちらをご覧下さい。

[Working With Containers](/userguide/usingdocker)を見て下さい。

## Dockerイメージ

**自分のイメージにアクセスしたり，あるいは共有したりビルドするにはどうすればいいんだ？**

Dockerの使い方がわかったら，次のステップを踏み出し，Dockerを使って自分のアプリケーションイメージをビルドする方法を学ぶ時です。

[Working with Docker Images](/userguide/dockerimages)を見て下さい。

## コンテナ同士を連携させる

ここまで，Dockerコンテナ内の独立したアプリケーションをビルドする方法を見てきました。今度は，コンテナをリンクさせてアプリケーションスタック全体をDockerで構築する方法を学びましょう。

[Linking Containers Together](/userguide/dockerlinks)を見て下さい。

## コンテナでデータを管理する

我々は既にコンテナ同紙を連携させる術を知っています。次のステップは，データ，ボリューム，そしてコンテナ内でのマウントを管理する方法です。

[Managing Data in Containers](/userguide/dockervolumes)を見て下さい。

## Docker Hubを使う

さあ，Dockerの使い方に少し詳しくなってきましたね。次は，DockerとDocker Hubで提供されているサービスをどのように連携させるかを学びます。これにはトラステッドビルドとプライベートリポジトリの話題も含まれています。

[Working with Docker Hub](/userguide/dockerrepos)を見て下さい。

## Getting help

* [Docker homepage](http://www.docker.io/)
* [Docker Hub](https://hub.docker.com)
* [Docker blog](http://blog.docker.io/)
* [Docker documentation](http://docs.docker.io/)
* [Docker Getting Started Guide](http://www.docker.io/gettingstarted/)
* [Docker code on GitHub](https://github.com/dotcloud/docker)
* [Docker mailing
  list](https://groups.google.com/forum/#!forum/docker-user)
* Docker on IRC: irc.freenode.net and channel #docker
* [Docker on Twitter](http://twitter.com/docker)
* Get [Docker help](http://stackoverflow.com/search?q=docker) on
  StackOverflow
* [Docker.com](http://www.docker.com/)

