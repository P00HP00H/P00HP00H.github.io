---
layout: post
category: "docker"
title: "Dockerfile(2)"
tags: ["docker"]
---

저번 포스팅에서 dockerfile에 대해서 알아보았고 dockerfile로 이미지까지 만들어보았다. 이번 포스팅에서는 만든 이미지를 컨테이너로 실행했을 때 해당 설정들이 잘 들어가있는지 확인해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/37.JPG?raw=true" width="px">

저번 포스팅에서 만든 dockerfile이다. 이를 poohpooh라는 이름과 test 태그로 이미지를 build 하였다. docker run 명령어로 컨테이너를 실행시킨다.

docker run -it poohpooh:test /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/47.JPG?raw=true" width="px">

해당 컨테이너로 접속하였다. 이제 각 설정들을 하나하나 확인해보자.

- FROM ubuntu:16.04

  ubuntu:16.04를 기본 이미지로 했기 때문에 ubuntu:16.04가 깔려 있어야 한다. cat /etc/\*release\* 명령어로 확인해보면

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/48.JPG?raw=true" width="px">

  ubuntu:16.04가 잘 설치되었다.

- MAINTAINER pooh

  dockerfile 만든 사람의 정보가 쓰여 있는 것으로 build 할 때 아무런 영향이 없으므로 넘어가자.

- ADD linux-2.4.32.tar.bz2 /pooh

  호스트에 있는 linux-2.4.32.tar.bz2 파일을 컨테이너의 /pooh라는 곳에 추가하겠다는 의미이다. 저번 포스팅에도 언급했지만 경로가 따로 없고 파일명만 있는 것은 dockerfile과 같은 경로에 있기 때문이다. 그리고 ADD는 호스트의 압축 파일(tar.gz, tar.bz2, tar.xz)인 경우 압축을 해제하고 tar을 풀어서 컨테이너에 추가된다고 했다. 실제로 그렇게 되어있는지 /pooh로 가서 확인해보자.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/49.JPG?raw=true" width="px">

  linux-2.4.32.tar.bz2가 있는 것이 아닌 linux-2.4.32라는 이름의 디렉토리가 있는 것을 확인할 수 있다. 해당 디렉토리로 가보면

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/50.JPG?raw=true" width="px">

  해당 리눅스 파일들이 있음을 확인할 수 있다. 즉, tar.bz2 파일이 ADD에 의해 압축이 해제되고 tar이 풀린 채로 컨테이너에 저장된 것이다.

- WORKDIR /pooh

  RUN, CMD, ENTRYPOINT 등의 명령이 실행될 디렉터리로 밑의 RUN 명령어들을 /pooh에서 실행했다고 생각하면 된다. 그리고 docker run으로 컨테이너에 처음 접속할 때 경로를 보면 /pooh로 되어있음을 확인할 수 있는데

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/51.JPG?raw=true" width="px">

  이 역시 WORKDIR을 /pooh로 해줬기 때문이다.

- COPY linux-copy.tar.bz2 /pooh

  ADD와 똑같이 호스트에 있는 linux-copy.tar.bz2 파일을 컨테이너의 /pooh라는 곳에 추가하겠다는 의미이다. 하지만 저번 포스팅에서 COPY는 호스트에 있는 압축파일을 추가할 때 압축을 해제하지 않는다고 했다. 실제로 확인을 해보면

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/53.JPG?raw=true" width="px">

  아까 ADD로 인해 추가된 linux-2.4.32.tar.bz2는 압축이 풀어지고 tar까지 풀어져서 linux-2.4.32라는 디렉토리로 되어있는 반면 linux-copy.tar.bz2는 압축이 풀어지지 않아 파일 형태 그대로 컨테이너에 있다.

- RUN apt-get update -y, RUN apt-get install mysql-client -y

  이 RUN 명령어를 통해 해당 컨테이너에 mysql-client를 설치하였다. 잘 설치가 되어있는지 확인해보자.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/52.JPG?raw=true" width="750px">

  mysql에 root 계정으로 들어가는 것은 실패했다. 지금 난 에러는 mysql 서버가 실행되지 않아서 나는 에러인데 지금은 mysql 에러를 잡으려고 하는 것이 아니기 때문에 이 부분은 다루지 않겠다. 여기서는 mysql 명령어가 실행됐다는 것에 초점을 맞추자. 만약 mysql-client가 지 않았다면 mysql : command not found가 떠야 한다. 하지만 mysql -uroot -p 명령어가 실행은 됐기 때문에 RUN 명령어들도 잘 수행됐음을 알 수 있다.

- VOLUME ["/pooh"]

  컨테이너의 /pooh 디렉토리를 Docker-Volume에 마운트시킨다는 의미이다. 이렇게 해주면 -v 옵션으로 따로 주지 않아도 Docker-Volume과 연동이 된다. 단, Docker-Volume의 이름은 해쉬값으로 저장한다. 이에 대한 자세한 내용은 저번 포스팅에서도 얘기했지만 추후에 자세하게 다룰 예정이다.

