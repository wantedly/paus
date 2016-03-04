# Paus を使ってみよう

## アジェンダ

0. SSH の公開鍵を登録
0. git remote で push 先を設定
0. リリースしてみよう
0. ブラウザで確認

## SSH の公開鍵を登録

* https://paus.wantedlyapp.com/ へアクセス
* terminal上で、 `$ cat ~/.ssh/id_rsa.pub | pbcopy` をする

下記のようにtextarea に書き込みをして `Submit` をする

![Paus.png](https://qiita-image-store.s3.amazonaws.com/0/18843/001d64c1-29cc-a22f-6cc1-753539c37258.png "Paus.png")


## git remote で push 先を設定

※ 以下 user名 を koudaiii でやっています

* 設定

```bash
git remote add paus git@git.wantedlyapp.com:koudaiii/docker-hello-world
```

* 確認

```bash
$ git remote -v
origin	git@github.com:koudaiii/docker-hello-world.git (fetch)
origin	git@github.com:koudaiii/docker-hello-world.git (push)
paus	git@git.wantedlyapp.com:koudaiii/docker-hello-world (fetch)
paus	git@git.wantedlyapp.com:koudaiii/docker-hello-world (push)
```


## リリースしてみよう

**今までの変更を commit する**

```bash
$ git push paus master
```


```console
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 375 bytes | 0 bytes/s, done.
=====> docker-compose.yml was detected
remote: Sending build context to Docker daemon 13.82 kB
Step 1 : FROM debian:jessie
jessie: Pulling from library/debian
Digest: sha256:a9c958be96d7d40df920e7041608f2f017af81800ca5ad23e327bc402626b58e
Status: Downloaded newer image for debian:jessie
 ---> 040bf8e08425
Step 2 : RUN apt-get update &&     apt-get install ruby -y &&         rm -rf /var/lib/apt/lists/* &&             gem install bundler --no-ri --no-rdoc
 ---> Running in ac82fbc1a703
Ign http://httpredir.debian.org jessie InRelease
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Get:2 http://httpredir.debian.org jessie-updates InRelease [136 kB]
Get:3 http://security.debian.org jessie/updates/main amd64 Packages [270 kB]
Get:4 http://httpredir.debian.org jessie Release.gpg [2373 B]
Get:5 http://httpredir.debian.org jessie Release [148 kB]
Get:6 http://httpredir.debian.org jessie-updates/main amd64 Packages [3619 B]
Get:7 http://httpredir.debian.org jessie/main amd64 Packages [9034 kB]
・・・
Successfully installed bundler-1.11.2
1 gem installed
 ---> 8c498379e2a1
Removing intermediate container ac82fbc1a703
Step 3 : COPY . /app
 ---> 48409e785db0
Removing intermediate container b86c7fbd699f
Step 4 : WORKDIR /app
 ---> Running in 1594116ea02c
 ---> 011ffb44beff
Removing intermediate container 1594116ea02c
Step 5 : RUN bundle install
 ---> Running in 4d0bae8c7c59
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
 ---> 3b81d608db4e
Removing intermediate container 4d0bae8c7c59
Step 6 : EXPOSE 8080
 ---> Running in 56c09d505df0
 ---> 811d256bd0be
Removing intermediate container 56c09d505df0
Step 7 : CMD script/server
 ---> Running in 12d8c213b1f5
 ---> 764c0991f06c
Removing intermediate container 12d8c213b1f5
Successfully built 764c0991f06c
=====> Pushing image...The push refers to a repository [localhost:5000/koudaiii/koudaiii-docker-hello-world1] (len: 1)
764c0991f06c: Preparing
764c0991f06c: Pushing
764c0991f06c: Pushed
811d256bd0be: Preparing
811d256bd0be: Pushing
811d256bd0be: Pushed
3b81d608db4e: Preparing
3b81d608db4e: Pushing
3b81d608db4e: Pushed
011ffb44beff: Preparing
011ffb44beff: Pushing
011ffb44beff: Pushed
48409e785db0: Preparing
48409e785db0: Pushing
48409e785db0: Pushed
8c498379e2a1: Preparing
8c498379e2a1: Pushing
8c498379e2a1: Pushed
73e8d4f6bf84: Preparing
73e8d4f6bf84: Pushing
73e8d4f6bf84: Pushed
latest: digest: sha256:bf197b69c74e882ff4174743d8ce88bed070f7fefea2a25dbd14e6af377ffe81 size: 12202
=====> Building...
remote: web uses an image, skipping
=====> Pulling...
remote: Pulling web (localhost:5000/koudaiii/koudaiii-docker-hello-world1:latest)...
=====> Deploying...
remote: Creating koudaiiidockerhelloworldca0b6bc_web_1
=====> koudaiii-docker-hello-world was deployed at https://koudaiii-docker-hello-world-ca0b6bc.wantedlyapp.com/
=====> koudaiii-docker-hello-world was deployed at https://koudaiii.wantedlyapp.com/
To git@git.wantedlyapp.com:koudaiii/docker-hello-world
 * [new branch]      master -> master
```


## ブラウザで確認

Pausでは現在1アカウントにつき3つまでURLが払いだされます。常に最新には `username.wantedlyapp.com` になります。


```bash
$ open https://koudaiii-spring-intern-2016-api-samples-8797934.wantedlyapp.com/
```

```bash
$ open https://koudaiii.wantedlyapp.com/
```

![Kobito.Bne59K.png](https://qiita-image-store.s3.amazonaws.com/0/18843/5f192901-5098-38b6-9ea5-2cfd15ad2827.png "Kobito.Bne59K.png")
