---
layout: post
category: "docker"
title: "Docker-Volume(2)"
tags: ["docker"]
---


저번 포스팅에서는 컨테이너를 띄울 때(docker run할 때) -v 옵션으로 Docker-Volume과 연동시키는 것까지 알아보았다. 이번 포스팅에서는 -v 옵션으로 호스트의 디렉토리와 연동시키는 것, volume container로 volume을 지정하는 방법에 대해 알아보고자 한다.

(이전 포스팅과 연결되는 내용이니 Docker-Volume(1) 포스팅을 읽고 와주세요!!)



- <h3>호스트 디렉토리를 volume으로 지정하는 경우</h3>

$ docker run -it -v [호스트 디렉토리]:[컨테이너 디렉토리] --rm [Docker image] /bin/bash

-it, --rm 옵션은 저번 포스팅에서 설명했으니 생략하겠다. -v 옵션으로 Docker-Volume을 연결했을 때와 똑같다. Docker-Volume 대신 volume으로 지정해 줄 호스트의 디렉토리를 써주면 된다. 이렇게 하면 컨테이너의 디렉토리가 호스트 디렉토리와 연동된다. 정확히는 [호스트 디렉토리]를 [컨테이너 디렉토리]에 mount 시키는 것이다. 하지만 Docker-Volume과 연동시켰을 때와 차이가 있다. 그 차이는 무엇인지 알아보자. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/70.JPG?raw=true" width="px">

현재 /home/pooh/test11이 volume이 될 호스트의 디렉토리이다. 아무것도 없는 상태이다. 이제 -v 옵션으로 호스트 디렉토리와 연동시켜서 컨테이너를 띄워보자.

 $ docker run -it -v /home/pooh/test11:/etc/mysql --rm mysql:5.7 /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/71.JPG?raw=true" width="750px">

Docker-Volume과의 차이를 알겠는가? Docker-Volume(1) 포스팅에서 컨테이너 디렉토리와 비어 있는 Docker-Volume을 연동시켰을 때는 컨테이너의 /etc/mysql에 여러 파일이 있었는데 이번에는 비어 있는 호스트의 디렉토리와 컨테이너의 /etc/mysql연동시켰더니 아무 파일도 없다. 도대체 어떻게 된 일일까?

Docker-Volume(1) 포스팅에서 설명했지만 다시 설명하면, Docker-Volume과 연동시켜 -v 옵션으로 컨테이너를 띄울 때 역시 원래는 호스트에 있는 Docker-Volume이 해당 컨테이너의 디렉토리에 mount 되는 구조이다. 하지만 이렇게 비어 있는 Docker-Volume이 비어 있지 않은 컨테이너 디렉토리에 mount 되는 경우에는 컨테이너의 디렉토리에 있는 파일이나 하위 디렉토리가 비어 있는 Docker-Volume으로 복사된다. 물론 이렇게 한 번 복사가 되면(Docker-Volume에 파일이나 디렉토리같은 무언가가 있으면) 그때부터는 Docker-Volume에 있는 파일들이나 디렉토리들이 컨테이너의 디렉토리가 비어 있던 그렇지 않던 컨테이너의 디렉토리에 mount 된다. 

하지만 Docker-Volume이 아닌 호스트 디렉토리와 연동시킨 경우는 호스트의 디렉토리가 비어 있어도 비어 있지 않은 컨테이너 디렉토리의 파일이나 디렉토리를 호스트의 디렉토리로 복사하지 않는다. 그냥 호스트의 디렉토리가 컨테이너 디렉토리(여기서는 /etc/mysql)로 mount 된다. 이렇게 호스트의 빈 디렉토리가 컨테이너 디렉토리의 파일들이 복사되지 않은 채로 mount 됐기 때문에 아무 파일이 없는 것이다. 그래도 이제 서로 연동이 된 상태이기 때문에 컨테이너에서 파일을 추가하던 호스트 디렉토리에서 파일을 추가하던 mount가 된 상태이기 때문에 호스트와 컨테이너에 똑같이 파일이 생성된다. 필자는 호스트에서 kkk.txt 파일을 생성해보겠다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/74.JPG?raw=true" width="px">

이제 컨테이너로 들어가서 /etc/mysql 디렉토리로 가보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/75.JPG?raw=true" width="750px">

똑같이 kkk.txt가 있다.

그렇다고 계속 저 상태는 아니다. 컨테이너를 빠져나와서(--rm 옵션을 줬기 때문에 exit만 해도 컨테이너가 삭제됨) 호스트 디렉토리와 연동시키지 않고(-v 옵션 빼고) 다시 새로 컨테이너를 실행시키면 해당 파일들이 다시 잘 나온다(단지, 호스트의 빈 디렉토리가 mount 돼서 아무 파일도 안 나온 것일 뿐 없어진 것이 아니다).

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/72.JPG?raw=true" width="px">

(Docker-Volume과 호스트 디렉토리의 차이가 좀 헷갈리 수 있습니다. 하지만, 직접 해보시면 금방 감이 오니까 직접 해보시는 걸 추천합니다!)



- <h3>volume container를 volume으로 지정하는 경우</h3>


정확히는 volume container의 volume을 volume으로 지정하는 것이다. 이해를 돕기 위해 다음 그림을 보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/76.JPG?raw=true" width="750px">

volume container는 그림에서도 나와 있듯이 같은 volume(volume이 여러 개일수도 있음)을 여러 개의 컨테이너가 참조할 때 쓰면 좋다. 단, 이렇게 volume container의 volume을 참조하려면 이 volume container의 volume은 반드시 호스트 디렉토리와 연결되어 있는 volume이어야 한다. 직접 만들면서 이해해보자.

먼저 volume container를 만들어야 한다.

\$ docker run --name volume_container -itd -v /home/pooh/host-volume1:/container-volume1 -v /home/pooh/host-volume2:/container-volume2 --rm centos

이렇게 volume-container가 만들어졌다. 이제 volume-conatiner에 container aaa와 container bbb를 붙여보자.(volume을 붙이려는 container 이름만 위 그림의 aaa, bbb와 같을 뿐 위의 그림처럼 volume container를 만들지는 X)

$ docker run --name=aaa --volumes-from=volume_container -it --rm ubuntu /bin/bash

이런 식으로 --volumes-from을 써주면 된다. volume_container는 계속 volume 역할을 할 수 있도록 -d 옵션으로 백그라운드에서 돌아가도록 했고 aaa, bbb 컨테이너는 눈으로 보기 위해서 백그라운드(-d 옵션)로 실행하지 않았지만 백그라운드로 실행해도 전혀 상관없다. 여튼 aaa 컨테이너에서 / 경로를 보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/77.JPG?raw=true" width="750px">

container-volume1, container-volume2가 있다. 터미널을 하나 더 열어서 bbb 컨테이너도 확인해보자.

$ docker run --name=bbb --volumes-from=volume_container -it --rm ubuntu /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/78.JPG?raw=true" width="750px">

역시나 있다. 즉, volume-container의 지정해 준 모든 volume이 mount 된다. 

그럼 volume container의 volume을 위에서처럼 호스트의 디렉토리와 연결시키지 않고 그냥 컨테이너 디렉토리만 정의해주는 경우는 어떨까? 직접 해보면서 알아보자.

(컨테이너 이름들 잘 확인하면서 따라와주세요! 밑은 volume_test라는 이름의 volume container를 생성하는 과정이고 /container_volume_test는 컨테이너의 디렉토리입니다.)

$ docker run --name=volume_test -v /container_volume_test -itd --rm ubuntu

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/84.JPG?raw=true" width="750px">

이렇게 한 다음 /container_volume_test에 kkk.txt 파일을 만들어보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/86.JPG?raw=true" width="750px">

이 상태에서 volume_test라는 이름의 volume container에 아까처럼 컨테이너를 붙여보자.

$ docker run --name=kkk --volumes-from=volume_test -it --rm centos /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/87.JPG?raw=true" width="750px">

--volumes-from으로 volume container와 연결시켰더니 volume container의 volume에 따로 호스트 디렉토리를 연결시키지 않았음에도 불구하고 volume container의 volume이 잘 mount 되었음을 확인할 수 있다. 하지만, 지금 말한 것은 틀린 말이다. volume container의 volume(/container_volume_test/를 의미함)이 호스트의 디렉토리와 연결시키지 않은 것처럼 보이지만 사실은 저렇게 정의한 거 역시 호스트의 디렉토리와 연결이 된 상태이다. Docker-Volume (1) 포스팅에서 배웠던 Docker-Volume과 연결된 것이다. 물론 따로 Docker-Volume의 이름을 지정하지는 않았기 때문에 이름은 해쉬값으로 되어 있을 것이다. 그래도 한 번 확인해보자.

$ docker inspect --format="{{.Mounts}}" [container name]

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/85.JPG?raw=true" width="750px">

해당 컨테이너에 대한 Docker-Volume이 생성되어 있음을 확인할 수 있고 역시나 해쉬값으로 되어 있다. 정리하면, Docker-Volume 역시 호스트의 디렉토리이므로 결국 호스트의 디렉토리와 연결이 돼서 저렇게 volume container로 쓸 수 있었던 것이다. 다시 말하지만 volume container에서 호스트의 디렉토리와 연결된 volume만 사용 가능하다.<br><br><br><br>

참고 사이트 : 

- https://darkrasid.github.io/docker/container/volume/2017/05/10/docker-volumes.html
	 https://m.blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=220421203146&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F			

