---
layout: post
category: "docker"
title: "Docker 설치 및 컨테이너 실행"
tags: ["docker"]
---
저번 포스팅에서 Docker의 개념에 대해서 알아보았으니 이번 포스팅에서는 Docker를 설치해보고 컨테이너를 직접 실행해보자. 실습 환경은 Ubuntu 16.04 이다.

설치하기 전에 먼저 update를 해준다.

sudo apt-get update

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/1.JPG?raw=true" width="px">

업데이트를 마쳤으면 이제 Docker를 설치해보자.

sudo apt-get install docker.io

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/2.JPG?raw=true" width="px">

Docker가 잘 설치돼있는지 확인하기 위해 버전을 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/3.JPG?raw=true" width="px">

잘 설치되어 있다. Docker 명령어가  잘 실행되는지 확인하기 위해 실행중인 컨테이너는 없지만 현재 실행중인 컨테이너들을 출력해주는 명령어인 docker ps를 입력해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/4.JPG?raw=true" width="750px">

docker ps를 입력해보니 permission denied가 떴다. 기본적으로 대부분의 Docker 명령어를 실행할 때 root 권한이 필요하다. 그래서 sudo를 붙여야 하는데, Docker 명령어를 실행할 때마다 sudo를 붙이기에는 너무 귀찮은 노릇이다. 따라서, 이를 위해 해당 유저를 docker 그룹에 포함시켜준다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/5.JPG?raw=true" width="px">

첫번째 줄을 보면 groupadd로 docker 그룹을 추가했으나 docker를 설치할 때 이미 생성됐는지 already exists라는 메시지가 떴다. 그래서 그 다음줄에 gpasswd 명령어를 통해 해당 유저(pooh)를 docker라는 그룹에 추가해줬다. gpasswd에 -a 옵션을 주게 되면 특정 그룹에 새로운 그룹멤버를 추가해준다는 의미이다. 

즉, gpasswd -a ${USER} docker

이를 해석하면 해당 유저를 docker라는 그룹에 그룹멤버로 추가해준다는 뜻이다.

그러고 나서 docker를 restart해준 다음 다시 docker ps를 입력했는데

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/6.JPG?raw=true" width="750px">

docker 명령어가 실행되지 않았다. docker 그룹에 추가됐나 id 명령어로 확인해봤더니

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/7.JPG?raw=true" width="750px">

추가가 되어 있지 않았다. 그래서 reboot 한 다음에 다시 id 명령어로 확인해보니

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/8.JPG?raw=true" width="750px">

docker 그룹에 추가돼있음을 확인할 수 있었다. docker ps 명령어도 잘 실행되었고 이제 root가 아니어도 sudo를 붙일 필요 없이 Docker 명령어를 실행할 수 있게 되었다.

이제 이미지를 다운받아서 해당 이미지를 기반으로 생성된 컨테이너를 실행시켜보자. docker image 명령어를 통해 이미지가 있는지 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/18.JPG?raw=true" width="750px">

역시 아무것도 없다. 이번 포스팅에서는 centos 컨테이너를 띄워보려고 한다. 따라서, centos 이미지를 다운받아보자. 이미지를 다운받을 때는 pull 명령어를 사용한다.

docker pull centos

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/9.JPG?raw=true" width="px">

다운받은 후에 docker images를 통해 이미지를 확인해보면 centos 이미지가 잘 받아졌음을 확인할 수 있다. TAG 부분에 latest라고 되어있는데 다운받을 때 이런식으로 따로 태그를 지정해주지 않으면 latest 즉, 가장 최신 버전으로 다운을 받는다고 생각하면 된다. 태그는 이미지 이름에 : 를 붙여 사용한다.(centos:7 이런 식으로)

이제 이미지를 다운받았으니 해당 이미지를 기반으로 한 컨테이너를 실행시켜 보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/10.JPG?raw=true" width="px">

docker run 이라는 명령어로 컨테이너를 실행시키는데 여기서 옵션을 줄 수가 있다. 지금 여기서 준 옵션은 -i와 -t인데 이를 살펴보게 되면

- -i : 표준 입력(stdin)을 활성화하며 컨테이너와 연결(attach)되어 있지 않더라도 표준 입력을 유지해주는 옵션
- -t : tty 즉, 콘솔이나 터미널이라고 생각하면 되는데, Bash를 사용하려면 이 옵션을 반드시 설정해줘야 한다. 이 옵션을 설정해주지 않으면 명령어를 입력할 수는 있지만 쉘이 표시되지 않기 때문이다.

정리하면 docker run -i -t [이미지 이름 or IMAGE ID] /bin/bash

이미지 이름 대신에 IMAGE ID를 넣어도 되고 -i -t 옵션을 한 번에 주려면 -it로 써주면 된다. 그리고 필자는 /bin/bash라는 쉘을 실행시키기 위해 뒤에 /bin/bash를 붙여준 것이지만 python을 실행시키려면 python을 입력해준다던지 등 여러 명령들을 입력해 줄 수 있다. 나중에 다른 명령들을 실행하는 예제들이 나오니 지금은 그냥 넘어가도록 하자.

docker run -i -t centos /bin/bash 실행해보면 밑에 pooh@pooh-virtual-machine이 아닌 root@0328a08bbd7c가 실행됐음을 알 수 있다. 즉, 컨테이너가 생성되었고 해당 컨테이너로 Bash 쉘이 실행된 것이다. centos 이미지를 기반으로 잘 생성됐는지 확인하기 위해 cat /etc/\*release\*로 버전을 확인해보면 CentOS라고 나온다.

지금은 컨테이너에 접속중이니 다른 터미널(이를 2번 터미널이라 하겠다)을 하나 더 띄워서 docker ps를 입력해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/11.JPG?raw=true" width="750px">

컨테이너가 실행되고 있음을 확인할 수 있다.

다시 컨테이너가 있는 터미널로 돌아와서 여기서 컨테이너를 빠져나가려면 exit 명령어를 사용하면 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/12.JPG?raw=true" width="px">

root@0328a08bbd7c에서 다시 pooh@pooh-virtual-machine로 바뀌었다. 이번에도 똑같이 버전을 확인해보면 Ubuntu 16.04라고 나온다. 잘 빠져나온 것이다.

지금 현재 exit로 컨테이너를 빠져나왔기 때문에 docker ps를 입력해보면 아무것도 나오지 않는다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/13.JPG?raw=true" width="750px">

하지만 -a 옵션으로 docker ps -a를 입력하게 되면 아까 빠져나온 해당 컨테이너가 나온다. 이를 해석해보면 docker run에 의해 컨테이너가 띄워지고 /bin/bash를 실행했는데 exit로 인해 컨테이너가 중지된 것이다. 즉, 중지된 것이지 삭제된 게 아니라는 뜻이다. 아직 삭제가 된 것이 아니기 때문에 docker restart [이미지 이름 or IMAGE ID]를 해주게 되면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/14.JPG?raw=true" width="750px">

다시 컨테이너가 잘 실행되고 있음을 알 수 있다. 실행되고 있는 컨테이너에 접속하기 위해 attach명령어를 사용해보면

docker attach [컨테이너 이름 or CONTAINER ID]

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/15.JPG?raw=true" width="px">

아까처럼 root@0328a08bbd7c로 되었고 버전도 CentOS임을 확인할 수 있다.

그럼 이제는 컨테이너가 필요 없어졌을 때 컨테이너를 삭제하는 방법에 대해 알아보자. 컨테이너가 실행중일 때는 먼저 컨테이너를 중지하고 삭제를 시켜야 한다. 아까처럼 해당 컨테이너에서 exit로 컨테이너를 중지할 수도 있고 컨테이너에 직접 접속하지 않고 docker stop [컨테이너 이름 or CONTAINER ID]로 중지할 수도 있다.  중지를 시켰으면 이제 docker rm [컨테이너 이름 or CONTAINER ID]로 해당 컨테이너를 삭제해주면 된다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/16.JPG?raw=true" width="px">

docker ps -a로 입력해봐도 존재하는 컨테이너가 없다.

하지만 컨테이너를 실행시킬 때마다 이렇게 매번 중지하고 삭제를 해주자니 너무 번거롭다. 그래서 컨테이너를 실행할 때 --rm 옵션을 줄 수가 있다. --rm 옵션은 컨테이너를 중지하면 동시에 바로 삭제가 되는 옵션이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/17.JPG?raw=true" width="px">

해당 컨테이너에서 exit로 중지하고 나서 docker ps -a로 확인해보면 따로 docker rm [컨테이너 이름 or CONTAINER ID]을 해주지 않아도 삭제가 되었음을 확인할 수 있다.