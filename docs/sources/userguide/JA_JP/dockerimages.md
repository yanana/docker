page_title: Dockerイメージを扱う
page_description: How to work with Docker images.
page_keywords: documentation, docs, the docker guide, docker guide, docker, docker platform, virtualization framework, docker.io, Docker images, Docker image, image management, Docker repos, Docker repositories, docker, docker tag, docker tags, Docker Hub, collaboration

# Dockerイメージを扱う

[イントロダクション](/introduction/)でDockerイメージはコンテナの基礎である事を知りました。[以前の](/userguide/dockerizing/)[節](/userguide/usingdocker)で，我々は`ubuntu`や`training/webapp`等の既に存在するDockerイメージを使用してきました。

Dockerはホストにダウンロードしたイメージを保存する事もまた発見しました。イメージがホストに存在しなければ，レジストリからダウンロードされます。また，デフォルトでこのレジストリは[Docker Hub](https://hub.docker.com)が設定されています。

本節では，以下の内容を見ていき，Dockerイメージについて少し深掘りしたいと思います。

* ローカルDockerホスト上でイメージを取り扱う
* 基本的なイメージを作る
* イメージを[Docker Hub](https://hub.docker.com)にアップロードする。

## ホスト上にあるイメージを一覧する

最初に，ローカルホスト上にあるイメージを一覧することから始めましょう。`docker images`コマンドを使います。

    $ sudo docker images
    REPOSITORY       TAG      IMAGE ID      CREATED      VIRTUAL SIZE
    training/webapp  latest   fc77f57ad303  3 weeks ago  280.5 MB
    ubuntu           13.10    5e019ab7bf6d  4 weeks ago  180 MB
    ubuntu           saucy    5e019ab7bf6d  4 weeks ago  180 MB
    ubuntu           12.04    74fe38d11401  4 weeks ago  209.6 MB
    ubuntu           precise  74fe38d11401  4 weeks ago  209.6 MB
    ubuntu           12.10    a7cf8ae4e998  4 weeks ago  171.3 MB
    ubuntu           quantal  a7cf8ae4e998  4 weeks ago  171.3 MB
    ubuntu           14.04    99ec81b80c55  4 weeks ago  266 MB
    ubuntu           latest   99ec81b80c55  4 weeks ago  266 MB
    ubuntu           trusty   99ec81b80c55  4 weeks ago  266 MB
    ubuntu           13.04    316b678ddf48  4 weeks ago  169.4 MB
    ubuntu           raring   316b678ddf48  4 weeks ago  169.4 MB
    ubuntu           10.04    3db9c44f4520  4 weeks ago  183 MB
    ubuntu           lucid    3db9c44f4520  4 weeks ago  183 MB

[ユーザーガイド](/userguide/)で今まで利用してきたイメージをここに見ることができます。それぞれコンテナの起動時に[Docker Hub](https://hub.docker.com)からダウンロードしたものです。

この一覧から，極めて重要な情報を三つ読み取る事ができます。

* どのリポジトリから取得したか，例えば，`ubuntu`。
* イメージに付けられたタグ，例えば，`14.04`。
* 各イメージのイメージID

リポジトリは潜在的に，一つのイメージの複数の変種を保持します。我々の`ubuntu`イメージの例で言うと，Ubuntu 10.04，12.04，12.10，13.04，13.10，14.04を含む複数の変種を見て取れます。それぞれタグによって識別され，タグ付けされたイメージはこのようにして参照できます。

    ubuntu:14.04

したがって，コンテナを起動する際にタグ付けされたイメージを参照するのに以下の様にします。

    $ sudo docker run -t -i ubuntu:14.04 /bin/bash

Ubuntu 14.04ではなく，Ubuntu 12.04のイメージが欲しければ，こうなります。

    $ sudo docker run -t -i ubuntu:12.04 /bin/bash

単に`ubuntu`と指定してタグを指定しなかった場合，Dockerはデフォルトで`ubuntu:latest`イメージを使用します。

**Tip:**
例えば`ubuntu:12.04`の様にして，常にタグを指定してイメージを利用する事をおすすめします。こうするとイメージのどの変種が利用されているかがはっきりとわかります。

## 新しいイメージを取得する

どうやって新しいイメージを取得できるのでしょうか？ホスト上に存在しないイメージを利用しようとすると，Dockerは自動的にイメージをダウンロードします。しかし，この動作によって，潜在的に起動時間が延びることになります。事前にイメージを取得したい場合には，`docker pull`コマンドを使用します。`centos`イメージをダウンロードしたいとすると，

    $ sudo docker pull centos
    Pulling repository centos
    b7de3133ff98: Pulling dependent layers
    5cc9e91966f7: Pulling fs layer
    511136ea3c5a: Download complete
    ef52fb1fe610: Download complete
    . . .

イメージの各レイヤがプルされている事が確認できます。これでイメージのダウンロードを待つこと無く，このイメージを起動できます。

    $ sudo docker run -t -i centos /bin/bash
    bash-4.1#

## イメージを見つける

Dockerの特徴付けるものの一つに，数多くの人が様々な目的のためにDockerイメージを作成しているという事が挙げられます。この内の多くが[Docker Hub](https://hub.docker.com)にアップロードされているため，[Docker Hub](https://hub/docker.com)でこれらのイメージを検索する事ができます。

![indexsearch](/userguide/search.png)

`docker search`コマンドを使うと，コマンドライン上でイメージを検索できます。我々のチームはRubyとSinatraがインストールされたイメージを開発のため必要としているとしましょう。

We can also search for images on the command line using the `docker search`
command. Let's say our team wants an image with Ruby and Sinatra installed on
which to do our web application development. We can search for a suitable image
by using the `docker search` command to find all the images that contain the
term `sinatra`.

    $ sudo docker search sinatra
    NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    training/sinatra                       Sinatra training image                          0                    [OK]
    marceldegraaf/sinatra                  Sinatra test app                                0
    mattwarren/docker-sinatra-demo                                                         0                    [OK]
    luisbebop/docker-sinatra-hello-world                                                   0                    [OK]
    bmorearty/handson-sinatra              handson-ruby + Sinatra for Hands on with D...   0
    subwiz/sinatra                                                                         0
    bmorearty/sinatra                                                                      0
    . . .

We can see we've returned a lot of images that use the term `sinatra`. We've
returned a list of image names, descriptions, Stars (which measure the social
popularity of images - if a user likes an image then they can "star" it), and
the Official and Automated build statuses. Official repositories are built and
maintained by the [Stackbrew](https://github.com/dotcloud/stackbrew) project,
and Automated repositories are [Automated Builds](
/userguide/dockerrepos/#automated-builds) that allow you to validate the source
and content of an image.

We've reviewed the images available to use and we decided to use the
`training/sinatra` image. So far we've seen two types of images repositories,
images like `ubuntu`, which are called base or root images. These base images
are provided by Docker Inc and are built, validated and supported. These can be
identified by their single word names.

We've also seen user images, for example the `training/sinatra` image we've
chosen. A user image belongs to a member of the Docker community and is built
and maintained by them.  You can identify user images as they are always
prefixed with the user name, here `training`, of the user that created them.

## Pulling our image

We've identified a suitable image, `training/sinatra`, and now we can download it using the `docker pull` command.

    $ sudo docker pull training/sinatra

The team can now use this image by run their own containers.

    $ sudo docker run -t -i training/sinatra /bin/bash
    root@a8cb6ce02d85:/#

## Creating our own images

The team has found the `training/sinatra` image pretty useful but it's not quite what
they need and we need to make some changes to it. There are two ways we can
update and create images.

1. We can update a container created from an image and commit the results to an image.
2. We can use a `Dockerfile` to specify instructions to create an image.

### イメージを更新し，コミットする

イメージを更新するには，更新したいイメージからコンテナを起動する必要があります。

    $ sudo docker run -t -i training/sinatra /bin/bash
    root@0b2616b0e5a8:/#

> **注釈**
>
> すぐに必要となるので，作成されたコンテナIDをメモしておいてください。この例では`0b2616b0e5a8`です。

起動中のコンテナ内で，`json`gemを追加しましょう。

    root@0b2616b0e5a8:/# gem install json

追加できたら，`exit`コマンドでコンテナを終了しましょう。

これで，必要な変更が為されたコンテナを手に入れました。そして`docker commit`コマンドを使ってコンテナのコピーをコミットすることができます。

    $ sudo docker commit -m="Added json gem" -a="Kate Smith" \
    0b2616b0e5a8 ouruser/sinatra:v2
    4f177bd27a9ff0f6dc2a830403925b5360bfe0b93d476f7fc3231110e7f71b1c

今，`docker commit`コマンドを，`-m`と`-a`という二つのフラグを指定して使用しました。`-m`フラグは，バージョン管理システムでコミットするのと同様に，コミットメッセージを指定するために使います。`−ａ`フラグはこの更新の作者（author）を指定するのに使います。

また，新しいイメージを作る元となるコンテナ`0b2616b0e5a8`（少し前にメモしたIDです）を指定しました。そしてイメージの更新対象も指定しました。

    ouruser/sinatra:v2

この更新対象を少し詳しく見てみましょう。ユーザ`ouruser`，
Let's break this target down. It consists of a new user, `ouruser`, that we're
writing this image to. We've also specified the name of the image, here we're
keeping the original image name `sinatra`. Finally we're specifying a tag for
the image: `v2`.

We can then look at our new `ouruser/sinatra` image using the `docker images`
command.

    $ sudo docker images
    REPOSITORY          TAG     IMAGE ID       CREATED       VIRTUAL SIZE
    training/sinatra    latest  5bc342fa0b91   10 hours ago  446.7 MB
    ouruser/sinatra     v2      3c59e02ddd1a   10 hours ago  446.7 MB
    ouruser/sinatra     latest  5db5f8471261   10 hours ago  446.7 MB

To use our new image to create a container we can then:

    $ sudo docker run -t -i ouruser/sinatra:v2 /bin/bash
    root@78e82f680994:/#

### `Dockerfile`からイメージを作成する

`docker commit`コマンドを使うのは非常にシンプルにイメージを拡張する方法ですが，少し扱いづらく，チーム内でイメージの構築プロセスを共有するのが難しくなります。代わりに，`docker  build`コマンドを使ってスクラッチからイメージを構築できます。

`docker build`コマンドでイメージを構築するには，Dockerイメージをどの様にして構築するかをDockerに教えるための命令を集めた`Dockerfile`を作ります。

ディレクトリと`Dockerfile`をまず作りましょう。

    $ mkdir sinatra
    $ cd sinatra
    $ touch Dockerfile

各命令はイメージの新しいレイヤを構築します。独自のシンプルなSinatraイメージを開発チームのために作って見ましょう。

    # This is a comment
    FROM ubuntu:14.04
    MAINTAINER Kate Smith <ksmith@example.com>
    RUN apt-get -qq update
    RUN apt-get -qqy install ruby ruby-dev
    RUN gem install sinatra

この`Dockerfile`が何をするのか見てみましょう。各命令は大文字の命令文（ステートメント）が前置されています。

    INSTRUCTION statement

> **注釈**
> コメントを表すのに`#`を使っています。

最初の命令である`FROM`はこのイメージのソースが何であるかをDockerに教えます。ここでは，Ubuntu 14.04のイメージをベースにしています。

次に，`MAINTAINER`という命令を使ってこのイメージのメンテナが誰であるかを指定しています。

最後に，三つの`RUN`命令を書きました。`RUN`命令はイメージ内でコマンドを実行します。ここではAPTキャッシュをアップデートし，RubyとRubyGemsをインストールし，そしてSinatraのgemをインストールします。

> **注釈**
> Dockerfileで利用できる命令は，[他にもたくさん](/reference/builder)あります。

では，`Dockerfile`と`docker build`コマンドでイメージをビルドしてみましょう。

    $ sudo docker build -t="ouruser/sinatra:v2" .
    Uploading context  2.56 kB
    Uploading context
    Step 0 : FROM ubuntu:14.04
     ---> 99ec81b80c55
    Step 1 : MAINTAINER Kate Smith <ksmith@example.com>
     ---> Running in 7c5664a8a0c1
     ---> 2fa8ca4e2a13
    Removing intermediate container 7c5664a8a0c1
    Step 2 : RUN apt-get -qq update
     ---> Running in b07cc3fb4256
     ---> 50d21070ec0c
    Removing intermediate container b07cc3fb4256
    Step 3 : RUN apt-get -qqy install ruby ruby-dev
     ---> Running in a5b038dd127e
    Selecting previously unselected package libasan0:amd64.
    (Reading database ... 11518 files and directories currently installed.)
    Preparing to unpack .../libasan0_4.8.2-19ubuntu1_amd64.deb ...
    . . .
    Setting up ruby (1:1.9.3.4) ...
    Setting up ruby1.9.1 (1.9.3.484-2ubuntu1) ...
    Processing triggers for libc-bin (2.19-0ubuntu6) ...
     ---> 2acb20f17878
    Removing intermediate container a5b038dd127e
    Step 4 : RUN gem install sinatra
     ---> Running in 5e9d0065c1f7
    . . .
    Successfully installed rack-protection-1.5.3
    Successfully installed sinatra-1.4.5
    4 gems installed
     ---> 324104cde6ad
    Removing intermediate container 5e9d0065c1f7
    Successfully built 324104cde6ad



We've specified our `docker build` command and used the `-t` flag to identify
our new image as belonging to the user `ouruser`, the repository name `sinatra`
and given it the tag `v2`.

We've also specified the location of our `Dockerfile` using the `.` to
indicate a `Dockerfile` in the current directory.

> **Note:**
> You can also specify a path to a `Dockerfile`.

Now we can see the build process at work. The first thing Docker does is
upload the build context: basically the contents of the directory you're
building in. This is done because the Docker daemon does the actual
build of the image and it needs the local context to do it.

Next we can see each instruction in the `Dockerfile` being executed
step-by-step. We can see that each step creates a new container, runs
the instruction inside that container and then commits that change -
just like the `docker commit` work flow we saw earlier. When all the
instructions have executed we're left with the `324104cde6ad` image
(also helpfully tagged as `ouruser/sinatra:v2`) and all intermediate
containers will get removed to clean things up.

We can then create a container from our new image.

    $ sudo docker run -t -i ouruser/sinatra:v2 /bin/bash
    root@8196968dac35:/#

> **Note:**
> This is just the briefest introduction to creating images. We've
> skipped a whole bunch of other instructions that you can use. We'll see more of
> those instructions in later sections of the Guide or you can refer to the
> [`Dockerfile`](/reference/builder/) reference for a
> detailed description and examples of every instruction.

## Setting tags on an image

You can also add a tag to an existing image after you commit or build it. We
can do this using the `docker tag` command. Let's add a new tag to our
`ouruser/sinatra` image.

    $ sudo docker tag 5db5f8471261 ouruser/sinatra:devel

The `docker tag` command takes the ID of the image, here `5db5f8471261`, and our
user name, the repository name and the new tag.

Let's see our new tag using the `docker images` command.

    $ sudo docker images ouruser/sinatra
    REPOSITORY          TAG     IMAGE ID      CREATED        VIRTUAL SIZE
    ouruser/sinatra     latest  5db5f8471261  11 hours ago   446.7 MB
    ouruser/sinatra     devel   5db5f8471261  11 hours ago   446.7 MB
    ouruser/sinatra     v2      5db5f8471261  11 hours ago   446.7 MB

## Push an image to Docker Hub

Once you've built or created a new image you can push it to [Docker
Hub](https://hub.docker.com) using the `docker push` command. This
allows you to share it with others, either publicly, or push it into [a
private repository](https://registry.hub.docker.com/plans/).

    $ sudo docker push ouruser/sinatra
    The push refers to a repository [ouruser/sinatra] (len: 1)
    Sending image list
    Pushing repository ouruser/sinatra (3 tags)
    . . .

## Remove an image from the host

You can also remove images on your Docker host in a way [similar to
containers](
/userguide/usingdocker) using the `docker rmi` command.

Let's delete the `training/sinatra` image as we don't need it anymore.

    $ sudo docker rmi training/sinatra
    Untagged: training/sinatra:latest
    Deleted: 5bc342fa0b91cabf65246837015197eecfa24b2213ed6a51a8974ae250fedd8d
    Deleted: ed0fffdcdae5eb2c3a55549857a8be7fc8bc4241fb19ad714364cbfd7a56b22f
    Deleted: 5c58979d73ae448df5af1d8142436d81116187a7633082650549c52c3a2418f0

> **Note:** In order to remove an image from the host, please make sure
> that there are no containers actively based on it.

# Next steps

Until now we've seen how to build individual applications inside Docker
containers. Now learn how to build whole application stacks with Docker
by linking together multiple Docker containers.

Go to [Linking Containers Together](/userguide/dockerlinks).
