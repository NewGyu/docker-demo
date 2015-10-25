Dockerチュートリアル
===================

このプロジェクトはapache + tomcatで構築されたアプリケーションをDockerで動かして試すためのチュートリアル用デモです。

## 仕様

* 80番ポートでhttpアクセスできる
* 裏ではapacheがtomcatにフォワードしている

### 動作

http://host/index.jsp  → [./app/src/main/webapp/index.jsp](./app/src/main/webapp/index.jsp)
```html:/index.jsp
<html>
<head>
<title>Hello JSP!</title>
</head>
<body>
<h1>Hello JSP!</h1>

Sun Oct 25 13:38:54 UTC 2015
```

http://host/hello → [./app/src/main/java/jp/newgyu/HelloServlet.java](./app/src/main/java/jp/newgyu/HelloServlet.java)
```html:/hello
<html><body>
<h1>Hello World!</h1>
<p>Servletのサンプル（HelloServlet.java）</p>
</body></html>
```

## 構成

![](https://cacoo.com/diagrams/V55Sg3UVjneVSwQv-EB29A.png)

* /usr/local/apache2
    * [/usr/local/apache2/conf/extra/proxy.conf](./infra/apache.conf/extra/proxy.conf)
* /usr/local/tomcat

## Dockerイメージの依存関係
* [newgyu/docker-sample:1.0.0](https://hub.docker.com/r/newgyu/docker-sample/)  ([Dockerfile](./app/Dockerfile))
    * FROM [newgyu/tomcat:1.0.0](https://hub.docker.com/r/newgyu/tomcat/)  ([Dockerfile](./infra/Dockerfile))  
        * FROM [tomcat:7-jre8](https://hub.docker.com/_/tomcat/)  ([Dockerfile](https://github.com/docker-library/tomcat/blob/8c174e038fe911fecc1a920a56afc10fd343bce2/7-jre8/Dockerfile))

## 実行方法

```shell-session
$ sudo docker run -d -p 80:80 newgyu/docker-sample:1.0.0
```

## イメージのビルド方法

```shell-session
$ cd app
$ ./gradlew war
```

`./build/libs/hello.war `が出来ます。

```shell-session
$ docker build .

Step 0 : FROM newgyu/tomcat:1.0.0
 ---> a48de4915c8d
Step 1 : RUN rm -rf /usr/local/tomcat/webapps/ROOT
 ---> Using cache
 ---> 2a17d1069a42
Step 2 : COPY build/libs/hello.war /usr/local/tomcat/webapps/ROOT.war
 ---> Using cache
 ---> 38a291398a18
Successfully built 38a291398a18
                   ^^^^^^^^^^^^ #作られたイメージのID
```
Dockerイメージが出来ます。

## イメージをDockerhubにアップロード

```
$ docker tag 38a291398a18 newgyu/docker-sample:1.0.0
```

`38a291398a18`に`newgyu/docker-sample:1.0.0`というタグ名を与える。

```
$ docker push newgyu/docker-sample:1.0.0
```

Dockerhubにイメージをアップロードする。(実際にはhttps://hub.docker.com のnewgyuユーザーの認証が必要になります)

## dockerコマンドリファレンス

```
See 'docker build --help'.
Usage: docker build [OPTIONS] PATH | URL | -
Build a new image from the source code at PATH
```

```
See 'docker tag --help'.
Usage: docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
Tag an image into a repository
```

```
See 'docker push --help'.
Usage: docker push [OPTIONS] NAME[:TAG]
Push an image or a repository to the registry
```

```
See 'docker run --help'.
Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
Run a command in a new container
```
