---
layout: post
category: "docker"
title: "Docker 컨테이너 백업"
tags: ["docker"]
---
이번 포스팅에서는 Docker 컨테이너 자체를 백업하는 것에 대해서 다루려고 한다. 



- <h3>Docker 컨테이너 백업</h3>

: Docker 컨테이너(그냥 컨테이너라고 하겠습니다.)에서 여러 작업들을 하다가 컨테이너가 문제가 생겨 내렸다가 다시 띄우면 컨테이너 자체가 휘발성이므로 작업했던 것들이 모두 없어진다. 물론 저번 포스팅들에서 배웠던 것처럼 이런 특성 때문에 Docker-Volume이라던지 호스트 볼륨이라던지 등등 볼륨들을 놓아 데이터들을 유지시켰지만 이런 데이터들 말고 툴 같은 것들이 설치된 경우는 볼륨으로 관리하기가 너무 까다롭다. 거기다 툴이 하나면 모를까 여러 개가 설치된 경우는 더더욱 관리하기가 어렵다. 이런 경우에는 컨테이너 자체를 백업하는 게 좋다. 컨테이너를 백업한 후(정확히는 컨테이너에 해당하는 이미지를 백업) 백업한 것을 다시 실행시켜주면 해당 작업들이 그대로 유지되어 있기 때문이다. 이제 백업하는 방법에 대해 알아보자. 먼저 백업할 컨테이너를 띄워보겠다.

$ docker run -it --rm ubuntu:16.04 /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/93.JPG?raw=true" width="750px">

그 다음 작업한 상태를 가정하기 위해 pooh.txt 파일을 추가해준다.

$ echo '1' > pooh.txt

이제 이 컨테이너 자체를 백업해보자. 
<br><br><br>
<h4>(1) 컨테이너 상태 저장하기</h4>

백업하기 전에 컨테이너 상태를 먼저 저장해야 한다. --rm 옵션이 있어서 해당 컨테이너를 나가면 바로 삭제되기 때문에 터미널을 하나 더 켜서 새로운 터미널에서 해당 명령을 입력해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/94.JPG?raw=true" width="750px">

현재 우리가 띄우고 있는 ubuntu:16.04 컨테이너이다. 이를 저장할 것이다. [컨테이너 이름 or ID] 부분에서 필자는 컨테이너 ID를 택했다.

$ docker commit [옵션]\[컨테이너 이름 or ID] [저장할 이미지 이름]:[태그]

$ docker commit -p a1a5db3887ee pooh_backup:test<br>(-p 옵션을 주지 않아도 똑같이 동작하긴 하는데, -p 옵션은 commit하기 위해 해당 컨테이너를 일시중지시키는 옵션이다.)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/95.JPG?raw=true" width="750px">

docker images를 통해 확인해보면 pooh_backup:test라는 이름의 도커 이미지가 잘 생성되었다. 실제 저 pooh_backup:test라는 이미지를 컨테이너로 실행시켜보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/96.JPG?raw=true" width="750px">

아까 만들었던 pooh.txt가 있는 것을 확인할 수 있다.
<br><br><br>
<h4>(2) 백업하기</h4>

도커 이미지가 있는 곳으로 commit한 이미지가 저장된 것으로 보아 이 자체가 백업이라고 생각할 수 있는데 docker가 삭제되면 도커 이미지들도 모두 삭제되기 때문에 해당 이미지를 압축파일로 저장해서 호스트에 저장하는 방식으로 백업해야 한다. 

$ docker save -o [저장 경로]\[저장할 파일이름].tar [이미지 이름]

$ docker save -o /home/pooh/test14/pooh_backup.tar pooh_backup:test  

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/97.JPG?raw=true" width="750px">

단, 저장 경로는 반드시 만들어져 있는 디렉토리여야 한다. 그렇지 않으면 밑의 화면처럼 없는 경로라고 에러가 뜬다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/98.JPG?raw=true" width="750px">
<br><br><br>
<h4>(3) 복원하기</h4>

도커 이미지가 없는 상태에서 잘 동작하는지 확인하기 위해 백업하면서 만들어졌던 pooh_backup:test 이미지를 지워보자.

$ docker rmi pooh_backup:test

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/99.JPG?raw=true" width="750px">

docker images로 보게되면 pooh_backup:test 이미지가 없다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/100.JPG?raw=true" width="750px">

이제 해당 이미지를 복원해서 컨테이너로 실행시켜보자. 복원하는 방법은 이렇다.

$ docker load < [저장 경로]\[저장할 파일이름].tar

$ docker load < /home/pooh/test14/pooh_backup.tar

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/101.JPG?raw=true" width="750px">

pooh_backup:test라는 이미지가 다시 생성되었다. 이제 컨테이너로 실행해서 아까 추가했던 pooh.txt 파일이 그대로 있는지 확인해보자.

$ docker run -it --rm pooh_backup:test /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/102.JPG?raw=true" width="750px">

pooh.txt 파일이 있음을 확인할 수 있다.<br><br><br><br>

참고 사이트 : 

- https://m.blog.naver.com/chandong83/221006388637			

