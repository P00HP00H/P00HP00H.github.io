---
layout: post
category: "database"
title: "[MySQL] ERROR 2002 (HY000) : Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2) "
tags: ["database"]
---
환경 : VMware에서의 Ubuntu 16.04이다.

MySQL에 접속하기 위해 비밀번호를 입력했는데 이런 에러가 떴다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/4.JPG?raw=true" width="750px">

이 에러는 보통 MySQL 데몬이 실행되고 있지 않은 경우에 발생한다. 그러면 MySQL 데몬이 어떤 상태인지 확인해보자.

sudo service mysql status

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/5.JPG?raw=true" width="750px">

보면 inactive(dead)라고 나와 있고 MySQL Community Server가 Stop되어있는 것을 확인할 수 있다. 그러면 MySQL을 다시 시작해주자.

sudo service mysql start

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/6.JPG?raw=true" width="750px">

다시 접속했더니 잘 접속이 된다.

아까의 상태와 비교하기 위해서 다시 status 명령어를 입력해서 확인해보면

sudo service mysql status

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/7.JPG?raw=true" width="750px">

아까와는 다르게 active(running)로 되어있음을 확인할 수 있다.

