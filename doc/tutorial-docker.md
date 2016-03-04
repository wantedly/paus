# Dockerを使ってみよう

# アジェンダ

0. Docker とは何か
0. Docker の全体像
0. Docker を動かしてみよう

---

# Docker とは何か
## Docker のインストール
<img src=https://www.docker.com/sites/default/files/products/tbox.jpg height=400px>

先に Docker 関連ツールのインストールをしておきましょう。

* https://www.docker.com/toolbox からダウンロード

```bash
$ docker -v
Docker version X.X.X, build XXXXX

$ docker-machine -v
docker-machine version X.X.X (XXXX)

$ docker-compose -v
docker-compose version: X.X.X
```

が実行できたら完了。


## エンジニアあるある
いきなり Docker とは何か、という話をする前にウェブエンジニアが抱えるよくある問題を取り上げたい。


* 開発環境の version で使っていた機能が本番環境の version にはまだない
* 開発環境を作るときに本番で使っているプラグインのバージョンを入れても動かない

Homebrew でインストールすると最新バージョンが入るのが主な原因なんだけど。
同じプラグインが入った同じバージョンを開発環境と本番環境に届けるだけなのに、難しい。

## 物流の課題

![Kobito.eGujnW.png](https://qiita-image-store.s3.amazonaws.com/0/18843/d0dd45d1-af1c-4085-0249-1c890e4310e2.png "Kobito.eGujnW.png")

今挙げたことと似た課題を抱えていた業界があって、物流がそうだった。
1940 年代は物の輸送というのは非常にコストがかかることだった。なんでかというと貨物の積み下ろしを人手でやっていたから。ある港では 30 人で荷物を降ろして、また別の港では 10 人しかいなくて荷物の降ろし方も違って、みたいなことだろうか。だからアメリカ産の牛肉で日本人がすき焼きを食べるとかは費用的にありえなかったらしい。

![Kobito.SFe9x9.png](https://qiita-image-store.s3.amazonaws.com/0/18843/2d3be77a-9c95-ece7-0079-d9c5a50c54b6.png "Kobito.SFe9x9.png")



そんな中生まれたのがコンテナだった。ただの箱といえば箱なんだけど、その影響度は大きくて、輸送の効率が圧倒的に良くなって自国で作った製品を他国に売るということが可能になって世界経済が一変したそう。

参考文献：コンテナ物語　世界を変えたのは「箱」の発明だった

![Kobito.lGd5uL.png](https://qiita-image-store.s3.amazonaws.com/0/18843/543fc5d9-9e33-952d-57ce-056d7e321c70.png "Kobito.lGd5uL.png")




そのコンテナが今ウェブ技術の世界に来ていて、ウェブの世界における "物流" の課題解決が Docker を担っている。

![image](https://cloud.githubusercontent.com/assets/261700/9404200/47565bb4-4828-11e5-8589-d86f963ca36f.png)

## Docker が解決する問題
Docker を使えば、あるソフトウェアをどこにでも **そのまま** 届けることができる

* 開発環境
* QA やステージング環境
* 本番環境
* CI などのテスト環境

のように環境が違っても

* Mac OS X
* Ubuntu
* CentOS
* Windows

のように OS が違っても。

## Docker はどう課題を解決してるのか
![Kobito.LLDBo6.png](https://qiita-image-store.s3.amazonaws.com/0/18843/acacc9a2-bd52-788f-89ad-c708797e639d.png "Kobito.LLDBo6.png")


詳細はこの後でまた説明するとして、ざっくりいうと Docker は

0. ソフトウェアをある形にパッケージングして、
0. それを必要な場所に届け、
0. 届けた先で開封するだけですぐにソフトウェアを動かせる

ということをやっている。

---

# Docker の基本
## Docker コンテナとイメージ
![https___docs_docker_com_article-img_architecture_svg](https://cloud.githubusercontent.com/assets/261700/9404600/e0b748d8-482b-11e5-9690-5b4973057c1c.png)

* Docker はソフトウェアを動かすときに **"コンテナ"** という箱として動かす
* コンテナは、 **"イメージ"** を元にいくつでも作ることができる
* **イメージ** は ruby が入ってるものだったり redis が入ってるものだったりとたくさんある

## Docker デーモン（サーバ）
![https___docs_docker_com_article-img_architecture_svg](https://cloud.githubusercontent.com/assets/261700/9404540/8bf00984-482b-11e5-839a-157dbb1d5bf7.png)

* コンテナの作成は **"Docker デーモン（サーバ）"** に依頼する
* Docker デーモンは API リクエストを受け付ける

```bash
$ curl -X POST http://<YOUR_DOCKER_SERVER>/containers/create -d '
{
    ...
    "User": "",
    "AttachStdin": false,
    "AttachStdout": true,
    "AttachStderr": true,
    ...
    "Env": null,
    "Cmd": [
      "echo hello world"
    ],
    "Entrypoint" "",
    "Image": "ubuntu",
     ...
}
'
```

## Docker クライアント
![https___docs_docker_com_article-img_architecture_svg](https://cloud.githubusercontent.com/assets/261700/9404719/cdbe3268-482c-11e5-9823-62fdd940991b.png)

* 毎回 API リクエストを投げるのも大変
* `docker` という CLI がある

```bash
# Docker デーモンがどこかを Docker クライアントに知らせる必要がある
$ export DOCKER_HOST=<YOUR_DOCKER_SERVER>

# Redis イメージを元にしてコンテナを作る
$ docker run redis
```

## Docker レジストリ
![https___docs_docker_com_article-img_architecture_svg](https://cloud.githubusercontent.com/assets/261700/9404884/fcaa8cc4-482d-11e5-9172-83ca138c22ff.png)

* コンテナの元となる **"イメージ"** は **"レジストリ"** と呼ばれる場所に保管する
* レジストリに保管されているイメージは Docker デーモンにダウンロードでき、それを元にコンテナを作れる
* => 世界中誰でも **"全く"** 同じ状態のソフトウェアが動かせる（コンテナとして）

# Docker を動かしてみよう
実際に手元の環境で Docker コンテナを動かしてみましょう。

## Docker ホスト及び Docker デーモンの用意
<img src=https://cloud.githubusercontent.com/assets/261700/9405307/b41994d8-4831-11e5-89b8-1322c04ed9e3.png height=200px>

Docker デーモンは Linux の機能に依存してるのもあって、 Mac OS X にインストールすることはできない。

<img src=https://cloud.githubusercontent.com/assets/261700/9405086/acc48398-482f-11e5-96fc-d1f4c7b859d2.png height=300px>

そこでコンテナを載せるための Linux マシンを用意して、そこに Docker デーモンを用意しましょう。
Docker デーモンが予めインストールされたマシンを用意できるツール `docker-machine` が Docker 公式に用意されている。

```bash
# Docker ホストを作成
$ docker-machine create --driver virtualbox default
Creating VirtualBox VM...
Creating SSH key...
Starting VirtualBox VM...
Starting VM...
To see how to connect Docker to this machine, run: docker-machine env default

# default が追加されてる
$ docker-machine ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM
default   *        virtualbox   Running   tcp://192.168.99.100:2376

# Docker サーバがどこにたったか確認

```bash
$ docker-machine env default
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.101:2376"
export DOCKER_CERT_PATH="/Users/koudaiii/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell:
# eval "$(docker-machine env default)"

# Docker クライアントの向き先を今作った default に向ける
$ eval "$(docker-machine env default)"

# クライアントがデーモンに無事に繋げているか確認
$ docker info
```

## Docker イメージのダウンロード
![https___docs_docker_com_article-img_architecture_svg](https://cloud.githubusercontent.com/assets/261700/9404884/fcaa8cc4-482d-11e5-9172-83ca138c22ff.png)

元となる **イメージ** がなければ **コンテナ** が作れないので、まずは **イメージ** を **レジストリ** からダウンロードしてきましょう

```bash
# 最初の段階でイメージが一切ない
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE

# イメージをダウンロードしてくる
$ docker pull koudaiii/hello-world
Using default tag: latest
Pulling repository docker.io/spesnova/hello-world
abbc273c7880: Download complete
511136ea3c5a: Download complete
8771fbfe935c: Download complete
0e30e84e9513: Download complete
ea659889c207: Download complete
18a74d010a7d: Download complete
2a09212a8117: Download complete
93152388f70c: Download complete
05857507fbaa: Download complete
Status: Downloaded newer image for spesnova/hello-world:latest
```

**レジストリ** には github のように `<アカウント>/<イメージリポジトリ>` のような形でイメージが管理されている

```bash
# イメージ一覧に出てきた！
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
koudaiii/hello-world   latest              abbc273c7880        5 months ago        152.6 MB
```

## Docker コンテナを動かす
![https___docs_docker_com_article-img_architecture_svg](https://cloud.githubusercontent.com/assets/261700/9404719/cdbe3268-482c-11e5-9823-62fdd940991b.png)

`docker run` を使ってコンテナを動かす。

```bash
$ docker run (<オプション>) <コンテナの元にするイメージ> <コンテナの起動コマンド>
```

先ほどダウンロードしてきた sinatra イメージを動かしてみよう

```bash
$ docker run \
  -d \
  -p 80:8080 \
  --name hello \
  koudaiii/hello-world
455dc1bfb175ee7b380605f6760975876ba4cbd5af50a3fbda67fd3755992714
```


コンテナ一覧を確認すると sinatra コンテナが立っているのがわかる

```bash
$ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                  NAMES
455dc1bfb175        koudaiii/hello-world   "bundle exec ruby app"   2 seconds ago       Up 2 seconds        0.0.0.0:80->8080/tcp   hello
```

sinatra コンテナは Docker マシン上の 80 ポートで動いているので、マシンの IP を調べる

```bash
$ docker-machine env default
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/koudaiii/.docker/machine/machines/default"
export DOCKER_MACHINE_NAME="default"
# Run this command to configure your shell:
# eval "$(docker-machine env default)"
```

http://192.168.99.100 にアクセス。	Hello World が見られるはず！

コンテナのログも見てみよう。

```bash
$ docker logs -f hello
[2015-08-21 09:33:10] INFO  WEBrick 1.3.1
[2015-08-21 09:33:10] INFO  ruby 2.1.5 (2014-11-13) [x86_64-linux-gnu]
== Sinatra/1.4.5 has taken the stage on 4567 for production with backup from WEBrick
[2015-08-21 09:33:10] INFO  WEBrick::HTTPServer#start: pid=1 port=4567
192.168.99.1 - - [21/Aug/2015:09:36:15 +0000] "GET / HTTP/1.1" 200 11 0.0048
192.168.99.1 - - [21/Aug/2015:09:36:15 UTC] "GET / HTTP/1.1" 200 11
```

コンテナを止めて削除しよう。

```bash
$ docker stop hello && docker rm hello
hello
hello
```

## イメージのビルド
今度はダウンロードしてきたイメージではなく、自分でイメージをビルドして、それを元にしてコンテナを動かしてみよう。

```bash
# 元のソースコードを clone してくる
$ git clone https://github.com/koudaiii/docker-hello-world
```

Docker のイメージをビルドするには `Dockerfile` というものを用意する。

```Dockerfile
# Docker イメージは継承みたいに重ねることができる
# FROM 句はどのイメージを元にしてイメージをビルドするか記述する
FROM debian:jessie

# RUN 句は下記のようにパッケージのインストールなど構築作業に値するものを記述する
RUN apt-get update && \
    apt-get install ruby -y && \
    rm -rf /var/lib/apt/lists/* && \
    gem install bundler --no-ri --no-rdoc

# COPY 句には元のソースコードのファイル/ディレクトリをコンテナイメージの指定ディレクトリにコピーすることを記述する
COPY .  /app

# WORKDIR 句にはコンテナのデフォルトディレクトリを記述する
WORKDIR /app

RUN bundle install

# EXPOSE 句はコンテナ内のどのポートを利用するか記述する
EXPOSE 8080

# CMD 句はコンテナのデフォルト起動コマンドを記述する
CMD ["script/server"]
```

`script/server` を見てみよう

```
$ cat script/server
#!/usr/bin/env bash

set -eux

ruby app.rb -p 8080 -o 0.0.0.0
```


`app.rb` を編集してみよう

```ruby
# app.rb
require "sinatra"

set :environment, :production

get "/" do
  # "Hello World"
  "Hello Docker"
end
```

ビルドしてみよう

```bash
$ docker build -t=hello .
Sending build context to Docker daemon 56.83 kB
Step 1 : FROM debian:jessie
 ---> f50f9524513f
Step 2 : RUN apt-get update &&     apt-get install ruby -y &&         rm -rf /var/lib/apt/lists/* &&             gem install bundler --no-ri --no-rdoc
 ---> Using cache
 ---> 55643f477e24
Step 3 : COPY . /app
 ---> fa00024b8c52
Removing intermediate container 6aaf39b218d9
Step 4 : WORKDIR /app
 ---> Running in 2a3f8af4adfa
 ---> dbb4d36d5521
Removing intermediate container 2a3f8af4adfa
Step 5 : RUN bundle install
 ---> Running in 7e1d15c4ec10
Don't run Bundler as root. Bundler can ask for sudo if it is needed, and
installing your bundle as root will break this application for all non-root
users on this machine.
Fetching gem metadata from https://rubygems.org/..........
Fetching version metadata from https://rubygems.org/..
Installing rack 1.6.4
Installing tilt 2.0.2
Using bundler 1.11.2
Installing rack-protection 1.5.3
Installing sinatra 1.4.7
Bundle complete! 1 Gemfile dependency, 5 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
 ---> 8d882c5cd940
Removing intermediate container 7e1d15c4ec10
Step 6 : EXPOSE 8080
 ---> Running in 348873c7faf9
 ---> 713eb922c4d4
Removing intermediate container 348873c7faf9
Step 7 : CMD script/server
 ---> Running in 0da42a86d98b
 ---> a354fb8b9833
Removing intermediate container 0da42a86d98b
Successfully built a354fb8b9833
```

`hello` という名前のイメージができている

```bash
$ docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
hello                  latest              0de4076a048c        17 seconds ago      155.3 MB
debian                 jessie              4a5e6db8c069        13 hours ago        125.2 MB
spesnova/hello-world   latest              abbc273c7880        5 months ago        152.6 MB
```

`hello` イメージを使って、先ほどと同じようにコンテナを起動してみよう。今度は `-d` を除いてフォアグランドで動かしてみる。

```bash
$ docker run \
  -p 80:8080 \
  --name hello \
  hello
```

http://192.168.99.100/ にアクセスすると Hello Docker となっているはず！

```bash
$ docker stop hello && docker rm hello
hello
hello
```

## コンテナに環境変数を設定する
表示するメッセージを環境変数から受け取るように変えて

```ruby
# app.rb
require "sinatra"

set :environment, :production

get "/" do
  ENV["MESSAGE"]
end
```

もう一度ビルド


```bash
$ docker build -t=hello .
```

`-e` で環境変数を渡して起動

```
$ docker run \
  -p 80:8080 \
  --name hello \
  -e MESSAGE="Hello Wantedly" \
  hello
```

## Compose
Docker のコマンドは長くなりがち。そこで便利なツールがある。Docker 公式のツール `compose` を使おう。

`docker-compose.yml` というファイルを用意して、コンテナの起動方法を定義しよう。

```yaml
# docker-compose.yml
web:
  build: .
  ports:
    - "8080"
  environment:
    - MESSAGE=Hello Wantedly
```

docker build と同じように docker-compose build をしてみよう

```bash
$ docker-compose build
Building web
Step 1 : FROM debian:jessie
 ---> f50f9524513f
Step 2 : RUN apt-get update &&     apt-get install ruby -y &&         rm -rf /var/lib/apt/lists/* &&             gem install bundler --no-ri --no-rdoc
 ---> Using cache
 ---> 55643f477e24
Step 3 : COPY . /app
 ---> 15fbeed45de0
Removing intermediate container 7a9d50fb1da8
Step 4 : WORKDIR /app
 ---> Running in 4c5402acc245
 ---> 5f0f39d6ab86
Removing intermediate container 4c5402acc245
Step 5 : RUN bundle install
 ---> Running in eb2bb558bef9
Don't run Bundler as root. Bundler can ask for sudo if it is needed, and
installing your bundle as root will break this application for all non-root
users on this machine.
Fetching gem metadata from https://rubygems.org/..........
Fetching version metadata from https://rubygems.org/..
Installing rack 1.6.4
Installing tilt 2.0.2
Using bundler 1.11.2
Installing rack-protection 1.5.3
Installing sinatra 1.4.7
Bundle complete! 1 Gemfile dependency, 5 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
 ---> 9d3f7bc40d33
Removing intermediate container eb2bb558bef9
Step 6 : EXPOSE 8080
 ---> Running in b490818365e6
 ---> 4d10854e16d0
Removing intermediate container b490818365e6
Step 7 : CMD script/server
 ---> Running in e928b65130b4
 ---> 20e450cfaa68
Removing intermediate container e928b65130b4
Successfully built 20e450cfaa68
koudaiii@koudaiii-no-mac:~/src/g
```

docker-compose up で立ち上げてみよう

```bash
$ docker-compose up
Creating dockerhelloworld_web_1
Attaching to dockerhelloworld_web_1
web_1 | + ruby app.rb -p 8080 -o 0.0.0.0
web_1 | [2016-03-04 02:03:04] INFO  WEBrick 1.3.1
web_1 | [2016-03-04 02:03:04] INFO  ruby 2.1.5 (2014-11-13) [x86_64-linux-gnu]
web_1 | == Sinatra (v1.4.7) has taken the stage on 8080 for production with backup from WEBrick
web_1 | [2016-03-04 02:03:04] INFO  WEBrick::HTTPServer#start: pid=6 port=8080
```

docker ps すると compose で作らたコンテナが見えます

```bash
$ docker ps
CONTAINER ID        IMAGE                  COMMAND             CREATED             STATUS              PORTS                     NAMES
6122e954ec20        dockerhelloworld_web   "script/server"     39 seconds ago      Up 38 seconds       0.0.0.0:32768->8080/tcp   dockerhelloworld_web_1
```

ブラウザで確認してみる

```bash
$ open http://$(docker-machine ip default):32768
```
