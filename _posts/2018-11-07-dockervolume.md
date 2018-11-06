---
layout: post
category: "docker"
title: "Docker-Volume(1)"
tags: ["docker"]
---
이번 포스팅에서는 Docker-Volume에 대해서 다루고자 하는데 좀 길어져서 2번으로 나눠서 하려고 한다(너무 길어지면 읽기 싫어지니까...ㅎㅎ). 먼저 Docker-Volume이 무엇인지부터 알아보자.
<br><br><br>
- <h3>Docker-Volume</h3>

: Docker가 관리해주는 볼륨으로 Docker로 컨테이너를 띄웠을 때 데이터를 유지시켜주는 역할을 한다.  컨테이너의 변경 상태를 저장하려면 이미지로 저장해야 하는데, 그럴 필요없이 필요한 부분(디렉토리)을  Docker-Volume과 연동시키면 컨테이너의 변경 사항이 Docker-Volume에 그대로 저장이 된다. 그래서, Container를 내렸다가 올리더라도 Docker-Volume에서 데이터들을 불러오기 때문에 저장된 상태가 유지된다. Docker-Volume의 경로는 /var/lib/docker/volumes/[Docker-Volume name]에 있고 저장된 데이터는 /var/lib/docker/volumes/[Docker-Volume명]/_data/에 있다.

docker volume을 확인하는 명령어는 다음과 같다.

$ docker volume ls

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/62.JPG?raw=true" width="px">

어떤 값은 이름으로 나오는 반면 어떤 값은 해쉬값으로 나온다. 왼쪽 local이라고 되어 있는 부분은 DRIVER, 오른쪽 값들은 'Docker-Volume name'이다(위에 잘라서 안 보이는데 VOLUME NAME이라고 나옴). 그런데, 이게 모든 Docker-Volume들을 나타내지 않는다는 말이 있는데, 어떤 Volume은 안 나오는 경우가 있다고 한다(저는 다 나왔습니다).  만약 자기가 Docker-Volume을 만들었는데 안 나온다면 위 경우에 해당한다고 보면 될 것 같다. 그럼 이런 경우 확인을 못하는 걸까? 아니다. Docker-Volume의 정보를 볼 수 있으면서 docker volume ls보다 좀 더 자세한 명령어가 있다.

$ docker volume inspect [Docker-Volume name]

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/63.JPG?raw=true" width="750px">

물론 이 명령어는 하나의 Docker-Volume에 대해 자세하게 나오는 것이다. 만약 docker volume ls로 조회가 안 되는데 Docker-Volume의 이름을 알고 있다면 docker volume inspect 명령어로 만들어졌는지 확인하면 된다. 그림에서처럼 위는 우리가 알아볼 수 있는 Docker-Volume name인 반면 밑은 Docker-Volume name이 해쉬값으로 되어 있다. 그럼 어떤 경우에 Docker-Volume name이 해쉬값일까? 다음의 경우를 보자. 

- <h3>Dockerfile에서 Docker-Volume을 volume으로 지정하는 경우</h3>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/64.JPG?raw=true" width="px">

이는 dockerfile에서 Docker-Volume을 지정해주는 경우이다. 이를 해석해보면 mysql:5.7 이미지를 컨테이너로 실행시킬 때 /etc/mysql 디렉토리를 Docker-Volume과 연동시키겠다는 뜻이다. 이렇게 해주면 컨테이너가 내려졌다가 올라가더라도 다른 디렉토리들은 초기화가 되지만 /etc/mysql만큼은 Docker-Volume에 저장되어있는 데이터들이 불러와지기 때문에 저장된 상태가 유지된다.<br>(단, dockerfile에서의 VOLUME 명령어로는 Docker-Volume과만 연동될 뿐 호스트의 특정 디렉토리와는 연결할 수 X)<br>위 그림은 하나의 디렉토리만 지정했지만 VOLUME ["/etc/mysql", "/var/lib/mysql"] 이런 식으로 컨테이너의 여러 디렉토리를 지정해 줄 수도 있다. 이렇게 여러 디렉토리를 지정해주면 Docker-Volume 역시 여러 개가 생성된다. 하지만 Docker-Volume의 이름을 지정하지 않았기 때문에 해쉬값으로 저장되어 알아보기가 힘들다. 그래서 관리 측면에서더 효율적이지 않다. 물론 어떤 해쉬값이 어떤 컨테이너의 Docker-Volume인지 찾을 수는 있다. 그건 바로 컨테이너의 이름이다. 다음 그림들을 보자.

$ docker build -t pooh:volume .

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/79.JPG?raw=true" width="px">

이렇게 dockerfile을 pooh:volume이라는 이름의 이미지로 빌드한다. 그 다음 컨테이너를 띄운다.

$ docker run -it --name=pooh_volume --rm pooh:volume /bin/bash

-it, --rm 옵션에 대해서 설명하자면 -it은 -i 옵션 + -t 옵션이 합쳐진 것으로

- -i : 표준 입력(stdin)을 활성화하며 컨테이너와 연결(attach)되어 있지 않더라도 표준 입력을 유지해주는 옵션
- -t : tty 즉, 콘솔이나 터미널이라고 생각하면 되는데, bash를 사용하려면 이 옵션을 반드시 설정해줘야 한다. 우리는 눈으로 확인하기 위해 bash를 이용할 것이므로 -t 옵션을 주었다.
- -d : 여기에는 사용되지 않았지만 백그라운드로 돌려주는 옵션이다. 즉, -d 옵션을 주면 현재 화면은 호스트지만 백그라운드에서 컨테이너가 실행된다.

--rm 옵션은 해당 컨테이너를 나갔을 때(exit) 컨테이너를 자동으로 삭제시켜준다. --rm 옵션을 주지 않고 컨테이너를 띄우면 exit해도 컨테이너가 삭제되지 않는다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/80.JPG?raw=true" width="px">

현재 컨테이너의 /etc/mysql에 있는 파일들이다. 컨테이너를 띄웠으면 이제 컨테이너의 이름에 해당하는 볼륨을 볼 수 있다. 터미널을 하나 더 켜보자.

$ docker inspect [container name]

$ docker inspect pooh_volume

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/81.JPG?raw=true" width="750px">

원래는 엄청나게 많은 내용이 나오지만 지금은 Docker-Volume이 어떻게 생성되었는지만 볼 것이기 때문에 "Mounts" 부분만 보면 된다. 또는 애초에 명령어를 "Mounts"만 나오게 할 수도 있다.

$ docker inspect --format="{{.Mounts}}" [container name]

$ docker inspect --format="{{.Mounts}}" pooh_volume 

이 중에서 "Source" 부분이 경로인데 보면 해쉬값이 들어가 있는 것을 확인할 수 있다. 그럼 저 경로에 컨테이너의 /etc/mysql이 잘 mount 됐는지 확인해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/82.JPG?raw=true" width="750px">

잘 mount 되어있다.

이렇게 dockerfile에서는 Docker-Volume의 이름을 지정할 수는 없고 만약 해쉬값이 아닌 이름으로 관리를 하고 싶다면 다른 방법을 이용해야 한다. 그 방법은 먼저 Docker-Volume을 만든 다음, 컨테이너를 띄울 때(docker run할 때) 만들어놓은 Docker-Volume과 연동시키는 것이다.



- <h3>Docker-Volume을 만든 후 컨테이너를 띄울 때(docker run할 때) 만들어 놓은 Docker-Volume을 volume으로 지정하는 경우</h3>

$ docker volume create [Docker-Volume name]

(Docker-Volume 삭제는 create 대신 rm을 써주면 된다)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/65.JPG?raw=true" width="px">

이렇게 하면 자신이 지은 이름(mysql_test)의 Docker-Volume을 생성한 것이다. 이렇게 하면 해쉬값일 때보다 알아보기가 쉬워 관리하기도 쉽다. 이 상태에서 컨테이너를 띄울 때(docker run 할 때) -v 옵션으로 방금 생성한 Docker-Volume과 컨테이너의 해당 경로를 연결시켜주면 된다.

$ docker run -it -v [Docker-Volume name]:[컨테이너 해당 경로] --rm [Docker image] /bin/bash

$ docker run -it -v mysql_test:/etc/mysql --rm mysql:5.7 /bin/bash

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/66.JPG?raw=true" width="750px">

현재 컨테이너의 /etc/mysql 경로에는 해당 파일들이 있다. 이 컨테이너의 /etc/mysql 경로에 pooh.txt 파일을 하나 추가해보자. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/67.JPG?raw=true" width="px">

터미널을 하나 더 켜서 mysql_test라는 이름의 Docker-Volume으로 가보면

$ cd /var/lib/docker/volumes/mysql_test/_data/

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/68.JPG?raw=true" width="px">

mysql 컨테이너의 /etc/mysql에 있는 파일들이 똑같이 있다. 그 다음 다시 컨테이너에 접속해있는 터미널로 다시 가서 exit로 컨테이너를 빠져나가면 --rm 옵션을 줬기 때문에 컨테이너는 사라진다. 따라서, 원래 Docker-Volume과 연동시키지 않았으면 다시 mysql 컨테이너를 띄웠을 때 아까 전에 mysql 컨테이너의 /etc/mysql에 새로 추가한 pooh.txt가 없어야 한다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/69.JPG?raw=true" width="px">

하지만, 다시 Docker-Volume과 연동시켜 컨테이너를 띄웠기 때문에 Docker-Volume에 저장된 데이터들을 불러와 pooh.txt가 있음을 확인할 수 있다. 컨테이너의 데이터들은 휘발성이기 때문에 컨테이너가 내려갔다가 다시 올라가면(띄워지면) 변경 사항들이 다 없어지지만 이렇게 Docker-Volume을 사용하게 되면 변경 사항이 저장된다. 이게 Docker-Volume이 사용되는 이유다.

그런데, 이런 의문점이 들 수 있다. 초기 Docker-Volume(아무것도 mount 되지 않은 상태) 상태에서 docker run -v 옵션으로 컨테이너와 연결했을 때는 컨테이너의 디렉토리가 Docker-Volume에 mount 됐다. 따라서, 이번에도 docker run -v 옵션으로 컨테이너와 Docker-Volume을 연결했기 때문에 컨테이너의 /etc/mysql이 Docker-Volume을 mount하여 pooh.txt 파일이 없어져야 하는 거 아닌가 하는 생각이 들 수도 있다. 하지만 그게 아니다. 이에 대한 내용은 다음 포스팅에 나온다. 다음 포스팅에서 이어서 하겠다.

<br><br><br>

참고 사이트 : 

- https://darkrasid.github.io/docker/container/volume/2017/05/10/docker-volumes.html			

