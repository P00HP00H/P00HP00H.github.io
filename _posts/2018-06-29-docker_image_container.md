---
layout: post
category: "docker"
title: "이미지와 컨테이너"
tags: ["docker"]
---
이번 포스팅에서는 직접 실습해보면서 이미지와 컨테이너의 개념들을 잡아보도록 하자. 실습 환경은 Ubuntu 16.04 이다.

컨테이너는 이미지를 기반으로 생성되고 실행이 된다. 실행된 컨테이너에서 작업을 하게 되면 컨테이너가 당연히 처음상태와는 달라져 있다. 그러면 이미지도 달라질까? 아니다. 이미지는 컨테이너가 변한다고 이미지가 변하지는 않는다. 이미지는 그냥 그대로 있다. 비유를 들어보면 iso파일로 vm을 만들어 사용한다고 해서 iso파일이 변하지 않는 것과 같은 맥락이라고 생각하면 된다. 

그러면 처음 이미지에서 뭔가를 추가해놓은 것을 이미지로 만들 수는 없을까? 있다. 하지만 이미지로 바로 추가할 수는 없고 컨테이너를 실행시킨 후 추가하고 싶은 것을 추가한 뒤 이를 이미지화시켜주면 된다. 간단하게 예를 들어보자.

저번 포스팅 때 centos 이미지를 다운받았었다(만약에 없다면 docker pull centos 입력). centos에 mysql이 설치된 이미지를 만들어보자.  저번처럼 centos 이미지를 컨테이너로 실행해보면

docker run -it centos:latest /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/22.JPG?raw=true" width="750px">

잘 실행이 된다. 그리고 mysql이 있는지 버전을 확인해보면 mysql이 설치되지 않았으므로 당연히 command not found가 뜬다. 이 컨테이너에 mysql을 설치해보자.

yum update

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/23.JPG?raw=true" width="750px">

yum install mysql

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/24.JPG?raw=true" width="750px">

잘 설치가 됐는지 mysql --version으로 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/25.JPG?raw=true" width="750px">

아까는 command not found가 떴지만 지금은 mariadb 버전이 나온다(CentOS 7은 mysql을 설치하면, mariadb가 설치됨).

이제 이 컨테이너를 다시 이미지화시켜보자. 컨테이너를 이미지화시키려면 commit 명령어를 써줘야 한다.

docker commit <옵션> <컨테이너명 or 컨테이너 ID> <이미지 이름>:<태그>

필자는 옵션이 따로 없어서 옵션을 주지 않고 commit을 했다. 다른 터미널(이를 이제부터 2번 터미널이라고 하겠음)을 하나 띄워서 docker ps를 통해 실행되고 있는 컨테이너명이나 컨테이너 ID를 알아내고 그 뒷부분은 내가 정해줄 이미지 이름과 태그를 써주면 된다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/26.JPG?raw=true" width="750px">

그걸 바탕으로 명령어를 입력해보면

docker commit d7d86b17d551 centos:mysql

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/27.JPG?raw=true" width="750px">

이를 해석해보면 해당 컨테이너 상태를 centos:mysql이라는 이름으로 이미지로 만들겠다는 말이다. 필자의 경우 centos에다가 mysql을 추가했기 때문에 저런 식으로 이름을 지은 것이지만 짓고 싶은대로 마음대로 지어주면 된다. 잘 생성되었는지 터미널에서 docker images를 입력해보면 다른 이미지가 하나 더 생성되었다. 새로 생성한 이미지를 실행시킨 컨테이너에 mysql이 설치되어있는지 확인하기 위해 해당 컨테이너에 접속해서 확인해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/28.JPG?raw=true" width="750px">

아까와는 다르게 mysql --version을 입력했을 때 command not found가 아닌 mysql 버전이 잘 나옴을 확인할 수 있다.

정리하면 이미지 A를 실행시킨 컨테이너에서 어떤 작업을 해서 내용을 변화시킨다고 해도 이미지 A는 절대 변하지 않고 변화시킨 컨테이너의 내용을 저장하고 싶으면 해당 컨테이너를 commit해서 이미지 B로 만들어야 한다.<br>그 이유는 이미지 B로 만들지 않으면 컨테이너를 삭제한 후 다시 올렸을 때 이미지 A의 상태 즉, 변경사항이 저장되지 않은 상태의 컨테이너가 실행되기 때문이다. 이미지 B를 만들고 이미지 B를 실행시킨 컨테이너의 내용를 확인해보면 변화된 상태가 적용이 되어있을 것이다.
<br><br>

참고 블로그 : http://pyrasis.com/Docker/Docker-HOWTO

