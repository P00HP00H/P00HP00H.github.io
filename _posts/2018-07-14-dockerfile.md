---
layout: post
category: "docker"
title: "Dockerfile(1)"
tags: ["docker"]
---

이전 포스팅에서는 만들어져 있는 이미지 파일을 다운받았다면 이번 포스팅에서는 직접 이미지를 만드는 방법에 대해서 알아보도록 하자. 이때 사용하는 것이 Dockerfile이다. Dockerfile은 Docker 이미지 설정파일로 Dockerfile에 설정된 내용대로 빌드해서 이미지를 생성하는 것이다. 실습 환경은 CentOS 7에서 진행하였다.

먼저 기본(베이스) 이미지를 지정해줘야 하는데 이는 반드시 지정해줘야 하며 어떤 이미지도 베이스 이미지가 될 수 있다. tag는 될 수 있으면 latest(기본값)보다 구체적인 버전(16.04등)을 지정하는 것이 좋은데, 그 이유는 latest는 최신 버전이다보니 처음에 셋팅했던 환경과 호환이 안 되는 상황이 벌어질 수 있기 때문이다. 이미 만들어져 있는 다양한 베이스 이미지는 [Docker hub](https://hub.docker.com/explore/)에서 확인할 수 있다.

다음은 필자가 만든 dockerfile 이미지인데 이를 바탕으로 각 명령어들에 대해 알아보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/37.JPG?raw=true" width="px">

- FROM: Docker 이미지는 기존에 만들어진 즉, 베이스 이미지를 기반으로 생성하기 때문에 베이스 이미지를 설정해줘야한다. 어떠한 이미지도 베이스 이미지가 될 수 있고 이 이미지들은 Docker hub에서 확인할 수 있다.  \<이미지 이름\>:\<태그\> 형식으로 설정해주면 되고 태그를 따로 지정하지 않으면 latest를 default값으로 사용하며 이미지를 생성할때 FROM에 설정한 이미지가 로컬에 있으면 바로 사용하고, 없으면 Docker Hub에서 받아온다. 필자는 ubuntu:16.04를 베이스 이미지로 했다.

- MAINTAINER: Dockerfile을 관리하는 사람의 이름 또는 이메일 정보를 적는 부분으로 빌드에 딱히 영향을 주지는 않으므로 적지 않아도 아무런 지장이 없다. 필자의 경우 그냥 뒤에 닉네임만 붙였다.

- ADD : 해당 파일이나 디렉토리를 이미지로 복사한다. 파일뿐만 아니라 디렉터리도 설정할 수 있고, 디렉터리를 지정하면 디렉터리의 모든파일을 복사한다. 와일드 카드를 사용하여 특정 파일만 복사할 수 있다. dockerfile의 ADD와 COPY에 경로 없이 파일명만 있는 이유는 dockerfile의 경로와 같은 경로에 있기 때문이다. 사용법은

  ADD \<복사할 호스트 파일 경로\> \<이미지에서 파일이 위치할 경로\>

  \<이미지에서 파일이 위치할 경로\>는 무조건 절대경로로 지정해줘야 하는 반면, \<복사할 호스트 파일 경로\>는 dockerfile이 있는 경로보다 상위 경로는 사용할 수 없고 하위 경로라도 절대 경로로 지정할 수 없다. 하나의 예를 보자(위의 dockerfile과는 별개로 테스트용으로 만들어 본 것이니 헷갈리지 말아주세요).

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/39.JPG?raw=true" width="px">

  여기를 보게 되면 /home/pooh/pooh_test/poohpooh/linux-test.tar.bz2가 있음을 확인할 수 있다. 즉, 이 리눅스 소스파일은 dockerfile의 하위 경로에 있고 이를 절대경로로 ADD를 이용해 컨테이너에 넣어보려고 했다. 그래서 dockerfile을 다음과 같이 작성했다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/38.JPG?raw=true" width="px">

  이 상태에서 dockerfile이 있는 경로(/home/pooh/pooh_test)에서 dockerfile을 poohpooh라는 이름과 test라는 태그로 빌드하면

  docker build -t poohpooh:test .

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/40.JPG?raw=true" width="750px">

  분명 저 경로에 linux-test.tar.bz2 파일이 있음을 확인했는데도 불구하고 에러가 뜬다. 그럼 이를 상대경로로 나타내보자. /home/pooh/pooh_test/poohpooh/linux-test.tar.bz2를 현재 dockerfile이 있는 경로를  .으로 나타내서 ./poohpooh/linux-test.tar.bz2로 바꿔보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/41.JPG?raw=true" width="px">

  그러고 나서 다시 dockerfile을 poohpooh라는 이름과 test라는 태그로 빌드해보면

  docker build -t poohpooh:test .

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/42.JPG?raw=true" width="px">

  분명 같은 경로인데도 아까 절대경로로 지정해줬을 때와는 다르게 dockerfile이 잘 빌드되었다(이미지가 잘 생성되었다). 

  ​

  다시 돌아와서 ADD는 호스트 파일 경로 안에 압축 파일(tar.gz, tar.bz2, tar.xz)인 경우에는 압축을 해제하고 tar을 풀어서 추가되지만 인터넷 파일 URL은 압축만 해제한 뒤 tar 파일이 그대로 추가된다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/36.JPG?raw=true" width="px">

  위의 그림을 보게 되면  현재 호스트 경로에 linux-2.4.32.tar.bz2 즉, 압축된 리눅스 소스파일이 있고 ADD를 이용해서 컨테이너에 넣어볼 것이다.


- WORKDIR: RUN, CMD, ENTRYPOINT 등의 명령이 실행될 디렉터리이고, 중간에 다른 디렉토리로 변경이 가능하며 WORKDIR은 절대경로, 상대경로 둘 다 사용 가능하다. 사용법은

  WORKDIR \<경로\>

  dockerfile을 보게되면 WORKDIR /pooh로 되어있고 그 위에 RUN apt-get update -y, RUN apt-get install mysql-client -y가 있다. 저 2개의 RUN 명령어를 /pooh에서 실행한다는 뜻이다. 

- COPY : ADD와 거의 동일한데, 차이가 있다면 COPY는 압축파일을 추가할 때 압축을 해제하지 않고 파일 URL을 사용할 수 없다. ADD와 COPY의 차이를 보기 위해 리눅스 소스파일을 다운받은 후 이름을 바꿔서 컨테이너에 옮겨볼 것이다.

- RUN: RUN <명령> 형식으로 셸 스크립트 구문을 사용할 수 있고 FROM 에서 설정한 이미지 위에 쉘 스크립트 혹은 명령을 실행하는 구문이다. RUN으로 실행한 결과가 새 이미지로 생성되고 실행내역을 이미지의 히스토리에 기록하지만 FROM으로 설정한 이미지에 포함된 /bin/sh 실행 파일을 사용하며 /bin/sh 실행파일이 없으면 사용할 수 없다. 대신 RUN ["<실행 파일>", "<매개 변수1>", "<매개 변수2>"] 형식으로 사용하면 셸 없이 바로 실행하며 배열형태로 설정한다. RUN으로 실행한 결과는 캐시되며 다음 빌드때 재사용, 캐시된 결과를 사용하지 않으려면 docker build 명령에서 --no-cache 옵션을 사용한다. dockerfile의 RUN 부분을 보면 -y가 뒤에 붙어있는데 이는 이미지 생성 중에 입력을 받을 수 없기 때문에 yes 명령어인 -y 옵션을 준 것이다. 

- VOLUME : 디렉터리의 내용을 컨테이너에 저장하지 않고 호스트에 저장하고, 사용법은<br>VOLUME \<컨테이너 디렉터리\> 또는 VOLUME ["\<컨테이너 디렉터리1\>","\<컨테이너 디렉터리2\>"]처럼 배열형식으로 지정이 가능하고 해당 볼륨을 호스트의 특정 볼륨과 연결하려면 docker run 명령에서 -v 옵션을 사용한다. -v 옵션은

  -v \<호스트 디렉토리\>:\<컨테이너 디렉토리\>

  이건 필자의 개인적인 의견인데, 사실 VOLUME 옵션을 왜 사용해야 하는지 잘 모르겠다. VOLUME을 사용해줘야만 호스트 디렉토리와 컨테이너 디렉토리가 연결되는 줄 알았는데, VOLUME을 지정해주지 않은 컨테이너의 특정 디렉토리와 호스트 디렉토리를 docker run의 -v 옵션으로 그냥 연결해줬더니 연결이 잘 된다. 단지 설정 파일의 정보를 보기 위해 쓰는 것인가 하는 생각도 든다. 아까 ADD에서 테스트했던 dockerfile로 다시 예를 들어보자. 아까도 봤지만 테스트를 위해 만들었던 dockerfile은 이러하다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/41.JPG?raw=true" width="px">

  이를 아까처럼 poohpooh:test라는 이미지로 만들면 VOLUME을 따로 지정해주지도 않았고 ADD에 의해 압축이 해제된 linux-test라는 이름의 디렉토리와 그 안에 소스파일들이 있어야 한다. docker run에 -v 옵션으로 호스트 디렉토리(/home/pooh/volumes)와 컨테이너의 디렉토리(/pooh)를 연결해서 poohpooh:test의 컨테이너를 실행시켜 보자.

  docker run -it -v /home/pooh/volumes:/pooh --rm poohpooh:test /bin/bash

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/43.JPG?raw=true" width="750px">

  하지만 막상 /pooh를 들여다보면 linux-test는 없고 kkk.txt만 있다. 위의 그림에도 나와 있듯이 kkk.txt는 호스트의 디렉토리(/home/pooh/volumes)에 있는 파일이다. 즉 호스트의 디렉토리(/home/pooh/volumes)와 컨테이너의 디렉토리(/pooh)는 잘 연결된 것이다. 이 현상을 보고 이렇게 dockerfile에다가 VOLUME 명령어를 사용하지 않아서라고 생각할 수도 있다. 하지만 VOLUME을 지정해도 똑같이 이런 결과가 나온다.<br>이에 대해서는 추후 포스팅인 도커 볼륨부분에서 자세하게 다룰테니 지금은 이 정도로만 하고 넘어가자. 지금 여기서 얘기하고자 하는 것은 dockerfile에서 VOLUME 명령을 해주지 않았음에도 호스트의 디렉토리와 컨테이너의 디렉토리가 잘 연결된다는 것이다.




맨 위 dockerfile에는 없지만 3개 명령어를 추가로 더 보면

- .dockerignore : Dockerfile과 같은 디렉터리에 있는 모든 파일을 Context라고 하는데 그 중 이미지를 생성할때 필요없는 파일들은 .dockerignore 파일에 작성하면 된다. Docker는 Go언어로 작성되어있기때문에 Go문법에 따라 작성해주면 된다.

- CMD: 컨테이너가 시작되었을 때(docker run이나 docker start 실행 시) 스크립트 혹은 명령이 실행된다.<br>CMD는 dockerfile에서 한번만 사용가능하다. 사용법은

  CMD \<명령\> 셸(/bin/sh) 또는 CMD ["\<실행 파일\>", "\<매개 변수1\>", "\<매개 변수2\>"]

- EXPOSE: 호스트와 연결할 포트 번호를 설정한다. 사용법은<br>EXPOSE <포트 번호> 이며 동시에 두개 이상 설정 가능하고 포트를 외부에 노출하려면 docker run 명령의 -p 옵션을 사용해야한다.

  EXPOSE도 VOLUME과 마찬가지이다. 굳이 dockerfile에서 지정해주지 않아도 그냥 docker run의 -p 옵션을 연결해주면 연결이 잘 된다. 이 역시 설정 파일의 정보를 보기 위해 쓰는 것인가 하는 생각은 든다.

  아까 만들었던 poohpooh:test로 다시 똑같이 실험을 해보면 해당 dockerfile에는 EXPOSE를 해주지 않았다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/41.JPG?raw=true" width="px">

  하지만 dockerfile을 빌드한 후 docker run의 -p 옵션을 줘보면

  docker run -it -p 2222:22 --rm poohpooh:test /bin/bash

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/44.JPG?raw=true" width="750px">

  이를 해석해보면 호스트의 2222번 포트로 접속하면 poohpooh:test의 컨테이너 22번 포트로 접속한다는 뜻이다. 실제로 잘 되어있는지 다른 터미널에서 docker ps로 보면

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/45.JPG?raw=true" width="750px">

  dockerfile에서 EXPOSE 명령을 주지 않아도 잘 연결이 되어있음을 확인할 수 있다.

  ​


지금까지 각 명령어들에 대해 알아보았고 이렇게 dockerfile에 설정을 해줬으면 이제 dockerfile이 있는 경로에서 docker build 명령어를 통해 dockerfile을 빌드해서 이미지를 만들어 주면 된다(이미 위에서 예를 들다가 다 말해버렸지만...) .

docker build -t pooh:test .

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/34.JPG?raw=true" width="px">

-t 옵션으로 만들 이미지의 이름을 지정해줄 수 있는데 필자는 pooh라는 이름과 test라는 태그를 붙여줬다.

build가 완료되면 잘 만들어졌는지 docker images 명령어로 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/35.JPG?raw=true" width="750px">

잘 만들어졌음을 알 수 있다. 다음 포스팅에서는 해당 이미지가 dockerfile 설정대로 잘 만들어졌는지 컨테이너를 띄워보면서 확인해보자.
<br><br><br>

참고 블로그 : http://ryumso86.tistory.com/2

​                       http://pyrasis.com/book/DockerForTheReallyImpatient/Chapter04/02

​                       https://subicura.com/2017/02/10/docker-guide-for-beginners-create-image-and-deploy.html

​			

