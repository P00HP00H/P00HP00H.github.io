---
layout: post
category: "docker"
title: "Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?"
tags: ["docker"]
---

CentOS 7에서 docker로 작업을 해야해서 docker ps 명령어를 입력했는데,  \"Cannot connect to the Docker daemon at unix\:\/\/\/var\/run\/docker.sock. Is the docker daemon running?\" 오류가 뜨면서 아무것도 나오지 않았다. 혹시 docker ps만 이런가 해서 다른 docker 명령어(docker images)를 입력해봤는데 역시나 똑같은 오류가 떴다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/55.JPG?raw=true" width="750px">

이렇게 docker 명령어가 안 먹을때는 역시 docker의 상태를 확인해보면 된다.

systemctl status docker.service

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/56.JPG?raw=true" width="750px">

역시나 도커가 죽어있다. 그러면 restart시켜주자.

systemctl restart docker

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/58.JPG?raw=true" width="750px">

그러고 나서 다시 docker 명령어를 입력하면(docker ps), 다시 잘 작동됨을 확인할 수 있다.

