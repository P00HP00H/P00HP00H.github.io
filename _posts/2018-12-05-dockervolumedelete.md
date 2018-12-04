---
layout: post
category: "docker"
title: "사용하지 않는 Docker-Volume 삭제하기"
tags: ["docker"]
---

Docker-Volume에서 이런저런 테스트를 하다보니 불필요한 Docker-Volume들이 많이 생겼다.<br>(Docker-Volume 경로 : /var/lib/docker/volumes/)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/90.JPG?raw=true" width="750px">

그림에서처럼 진짜 어마어마하게 많았다(너무 많아서 중간에서 잘랐습니다). 이전에 만들어놓은 것들도 있어서 그랬던 것 같다. 그런데 거의 해쉬값으로 되어 있어서 이게 어떤 Docker-Volume인지도 알 수 없거니와 어떤 Docker-Volume이 사용중이고 사용중이지 않은지 지금 이름들만 봐서는 도저히 알 길이 없었다(사용중 = Docker-Volume에 파일같은 무언가가 있음, 사용중 X = Docker-Volume에 아무것도 없음) . 이러한 상황에 사용할 수 있는 것이 바로 docker volume prune이다. prune을 해석하면 '잘라내다, 없애다' 라는 뜻이다.

$ docker volume prune

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/91.JPG?raw=true" width="750px">

빨간색으로 표시된 부분들이 모두 사용되지 않는 Docker-Volume들이다. 위에 Deleted Volumes이라는 메시지와 함께 해당 Docker-Volume들을 싹 다 지워버린다.

또한 필자의 경우 Docker-Volume이 있는 경로(/var/lib/docker/volumes/)에서 docker volume prune 명령어를 입력했지만 꼭 Docker-Volume이 있는 경로일 필요는 없다. 어떤 경로에서 입력하더라도 똑같이 수행된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/92.JPG?raw=true" width="750px">

그러고 나서 Docker-Volume(/var/lib/docker/volumes/)의 경로로 가보면 사용 중인 Docker-Volume만 남아있음을 확인할 수 있다. <br><br><br><br>

참고 사이트 : 

- https://dololak.tistory.com/404

