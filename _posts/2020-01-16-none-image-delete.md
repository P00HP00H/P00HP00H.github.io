---
layout: post
category: "docker"
title: "docker <none> image 한 번에 삭제하기"
tags: ["docker"]
---

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/118.JPG?raw=true" width="750px">

어느 날 docker image들을 확인해보니 이렇게 \<none\>으로 되어 있는 이미지들이 많이 있었다. 저런 이미지들은 이미지 생성 도중 에러가 났을 때 \<none\>으로 표시되는데 정리를 안해서 저렇게 많이 쌓여 있던 것이다. 그래서, 이를 하나하나 지워봤다. 우려고 하다보니 너무 많고 귀찮았다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/119.JPG?raw=true" width="650px">

근데, 이렇게 하나하나 지우려고 하다보니 너무 많고 귀찮았다. 그리고 상식적으로 당연히 이렇게 하나하나 지우도록 했을리가 없을 것 같았고 한 번에 지우는 명령어가 있을 것 같아서 검색해봤더니 역시나 있었다. 먼저 \<none\> 이미지들을 한 번에 삭제하기 위해 \<none\> 이미지만 나오게 해보자.<br><br>$ docker images -f "dangling=true" -q

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/122.JPG?raw=true" width="450px">

이 명령어를 입력하면 \<none\> 이미지들만 뽑아낼 수 있다. 이제 이들을 삭제해보자.<br><br>$ docker rmi \$(docker images -f "dangling=true" -q)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/120.JPG?raw=true" width="650px">

보면 엄청나게 많은 \<none\> 이미지들이 삭제되는 것을 볼 수 있다. 그러고 나서 잘 삭제되었는지 확인해보면<br><br>$ docker images

 <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/docker/121.JPG?raw=true" width="750px">

아까와는 다르게 엄청 깨끗해진 것을 확인할 수 있다.<br><br><br><br>참고 사이트 :

- <https://www.slipp.net/questions/536>