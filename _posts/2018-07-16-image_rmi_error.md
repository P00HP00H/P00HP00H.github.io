---
layout: post
category: "docker"
title: "unable to delete <image ID> (must be forced) - image is referenced in multiple repositories"
tags: ["docker"]
---

지난 포스팅에서 dockerfile로 이미지를 작성하는 법에 대해서 알아봤는데 그러다보니 잘못 만들어진 이미지들도 많고 필요없어진 이미지들도 많다. 그래서 제거를 하려는데 밑에 보면 IMAGE ID가 같은 것이 있다(그 위에위에 같은 것이 또 있지만 신경쓰지 말아주세요).  이녀석부터 지워보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/30.JPG?raw=true" width="px">

Docker Image를 지우는 명령어는 docker rmi \<IMAGE ID\>이다.

docker rmi dc69bffc5350

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/31.JPG?raw=true" width="px">

unable to delete 즉, 지울 수 없다는 메시지가 떴고 여러 저장소에서 이미지가 참조되고 있다고 나온다. 그래서 must be forced 반드시 강제되어야 한다고 나와있다. 따라서, -f 옵션을 줘서 강제로 삭제해주면 된다.

docker rmi -f dc69bffc5350

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/32.JPG?raw=true" width="px">

이미지가 잘 지워진다.



