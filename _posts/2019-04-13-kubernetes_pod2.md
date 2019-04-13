---
layout: post
category: "kubernetes"
title: "Pod에 문제가 발생한 경우"
tags: ["kubernetes"]
---

이번 포스팅에서는 Pod가 문제가 발생한 경우에 대해서 간단하게 포스팅하려고 한다. 환경은 마스터 노드(hpclab : 서버1), Worker 노드(hpclab2 : 서버2, hpclab3 : 서버3) 이렇게 3개의 노드로 구성되어 있으며 모두 CentOS 7이다.

<br><br>

**(1) 배포된 Pod의 컨테이너가 죽었을 때**

: 이는 Pod에 있는 컨테이너에 문제가 생겼을 경우이다. 이 때 어떻게 되는지 보자.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/107.JPG?raw=true?raw=true" width="300px">

현재 Pod는 이러하다. mysql(db) 컨테이너, nginx 컨테이너가 있고 이 Pod를 서버2에서 실행되도록 nodeSelector를 이용하여 배포하려고 한다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/108.JPG?raw=true" width="750px">

현재 서버2에 Pod가 배포된 상태이다. 이를 Pod 자체에 문제가 있는 상황으로 가정하여 해당 노드(서버2)에서 Pod의 컨테이너를 제거해보겠다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/109.JPG?raw=true" width="750px">

본 화면은 서버2이다(hpclab2). 여기서 배포된 nginx 컨테이너를 삭제했다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/110.JPG?raw=true" width="px">

docker ps로 확인해보면,  분명 방금 nginx 컨테이너를 삭제했는데 nginx 컨테이너가 있다. 그렇다. 새 nginx 컨테이너가 생성된 것이다(CONTAINER ID가 바뀌어있음). 또한 마스터 노드로 가서 Pod 상태를 확인해보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/111.JPG?raw=true" width="750px">

RESTART가 0에서 1로 바뀌었음을 확인할 수 있다. 이렇게 해당 Pod에 문제가 생기면 Pod를 Restart 시킨다. 즉, Pod의 컨테이너에 문제가 생겨도 Pod가 실행되는 작업 노드가 문제 없다면 쿠버네티스가 알아서 관리해주기 때문에 이런 상황에 대해 신경쓸 필요가 없다. 물론 Pod의 IP가 그대로인 것으로 보아 해당 Pod가 새로운 Pod로 대체된 것은 아님을 알 수 있다. 단지 Restart 됐을 뿐이다.<br><br><br><br>**(2) Pod가 배포된 노드에 장애가 발생한 경우**

: 이는 작업 노드에 Pod가 배포되어 실행되고 있는데, 노드에 문제가 생긴 경우이다. 예를 들어, 서버2에 Pod가 실행되고 있는데 서버2가 클러스터에서 빠지는 것이다. 이러한 상황을 가정하기 위해 서버2를 마스터 노드에서 제거해보겠다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/112.JPG?raw=true" width="450px">

서버2를 제거한 다음 Pod 현황을 확인해봤더니 아무것도 없다. 즉, Pod가 서버3로 옮겨졌다던지 이러지 않고 노드가 클러스터에서 제거됐기 때문에 Pod도 같이 없어진 것이다. 이러한 상황을 방지하기 위해 쿠버네티스는 Replication Controller를 제공한다. 이는 다음 포스팅에서 다룰 예정인데, Replication Controller의 관리를 받으면 이렇게 노드가 장애가 발생하더라도 다른 노드로 Pod가 배포된다. 그렇게 되면 위의 (1)과 비슷하게 문제가 생겨도 알아서 관리해주기 때문에 따로 신경쓰지 않아도 된다.<br><br><br><br>**(3) Pod가 배포된 노드는 문제없고 Pod 자체가 장애가 발생한 경우**

: 이는 (1)과 비슷하면서 좀 다른데 (1)은 엄연히 Pod 자체는 문제가 없다. (3)은 어떤 이유로 인해 Pod 자체가 삭제된 경우를 말하는 것이다.  이번에도 역시 해당 상황을 가정하기 위해 Pod를 지워보겠다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/113.JPG?raw=true" width="750px">

Pod가 삭제됐는데 아무 일도 일어나지 않는다. 이 경우도 역시 Replication Controller를 이용해서 관리해줄 수 있는데, RC를 이용하면 Pod가 저렇게 삭제되더라도 재생성해서 계속 실행할 수 있도록 해준다.
