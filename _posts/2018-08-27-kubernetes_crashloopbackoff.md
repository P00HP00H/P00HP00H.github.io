---
layout: post
category: "kubernetes"
title: "CrashLoopBackOff 에러가 났을 때"
tags: ["kubernetes"]
---

이번 포스팅에서는 CrashLoopBackOff 에러에 대해서 다뤄보려고 한다. 이 에러는 Pod를 온전히 생성하는 데 실패했을 경우 나타나는 에러이다. 이런 에러가 났을 시에 어떻게 해결해야 하는지에 대해 알아보자. 환경은 저번 포스팅과 같이 모든 노드들은 VM의 CentOS 7이다.



<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/55.JPG?raw=true" width="px">

11번째 줄을 보면 Pod가 관리할 컨테이너의 환경설정을 해주는 부분이라고 저번 포스팅에서 설명했다. nginx의 경우 이러한 설정을 해줄 필요 없지만 거의 대부분의 컨테이너들, 예를 들어, mysql, django, python 등은 이러한 설정을 해줘야 한다고 했다. 만약 해주지 않는다면 어떤 현상이 발생할까?? 궁금해서 직접 해보기로 했다.

밑의 pod.yml은 위의 pod.yml에서 env부분을 삭제한 상태이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/50.JPG?raw=true" width="px">

이 상태에서 Pod 오브젝트를 생성해보자.

kubectl create -f pod.yml

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/51.JPG?raw=true" width="750px">

Pod 오브젝트를 만든 다음 잘 생성되었는지 확인하기 위해 kubectl get pods -o wide 명령어를 입력해봤더니  STATUS에 Running이 아니라 CrashLoopBackOff라는 에러가 떴다. 컨테이너를 만드는 데 실패했다는 뜻이다. 잘 생성되었으면 READY도 2/2여야 하는데 READY에 1/2로 표시되어 있다. 즉, pod.yml에 2개의 컨테이너를 정의했는데 이 중 1개의 컨테이너만 생성되었다는 의미이다. 어떤 컨테이너가 잘못됐는지 확인해보기 위해 로그를 보자.

Pod에서 생성한 컨테이너의 로그를 보기 위해서는

kubectl logs [Pod명] -c [Pod에서 지정한 컨테이너명]

우리는 pod.yml에 각 컨테이너의 이름을 pooh-db-container와 pooh-nginx-container라고 지정해주었다. 이들을 [Pod에서 지정한 컨테이너명]에 넣어주면 되고 Pod명은 pooh-db-nginx-pod라고 정의했으므로 [Pod명]에 입력해주면 된다. 그럼 먼저 pooh-db-container부터 확인해보자.

kubectl logs pooh-db-nginx-pod -c pooh-db-container

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/52.JPG?raw=true" width="750px">

이렇게 설정을 해주지 않았기 때문에 실패했다는 문구가 뜬다. 에러를 살펴보면 PASSWORD를 지정해주지 않았기 때문에 발생했다고 나온다(password option is not specified). 그래서 맨 처음에 pod.yml의 env에다가 여기에 나온 MYSQL_ROOT_PASSWORD를 설정해 준 것이다. 이런 식으로 Pod의 컨테이너에 대한 로그를 확인할 수 있다. 이번엔 pooh-nginx-container에 대한 로그를 확인해보자.

kubectl logs pooh-db-nginx-pod -c pooh-nginx-container

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/53.JPG?raw=true" width="750px">

아무 에러가 없기 때문에 로그가 나오지 않는다. 그러면 여기는 단지 Pod가 잘 생성되는지 아닌지만 기록해주는 로그일까?? 아니다. 해당 컨테이너들을 배포된 worker-node2에서 실행하게 되면 그에 대한 로그들이 여기에 쌓이게 된다. 그래서 배포된 컨테이너에서 이런저런 작업들을 한 후 마스터 노드에서 로그를 확인해보면 해당 로그들이 많이 쌓여있게 될 것이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/54.JPG?raw=true" width="750px">

로그를 남겨보기 위해 worker-node2에서 이것저것 시도하다가 pooh-nginx-container를 stop하고 restart했더니 처리가 잘못되어 pooh-nginx-container가 2개 생기는 현상이 발생했다. 그래서 마스터 노드에서 pooh-nginx-container의 로그를 살펴보면 address already in use가 기록되어 있다. 물론 잘 처리되면 이런 fail 기록이 남지 않는다(운이 좋았네요... ^^).



정리하면, Pod를 생성하다가 CrashLoopBackOff 에러가 뜨면 해당 컨테이너의 로그를 확인해서 설정하라는 대로 pod.yml의 env에 설정해주면 잘 생성이 될 것이다.

<br><br><br>

참고 블로그 :

- https://www.linuxtechi.com/deploy-pod-replication-controller-service-kubernetes-1-7-on-centos-7/
- https://stackoverflow.com/questions/47129376/kubernetes-kubectl-a-container-name-must-be-specified-but-seems-like-it-is
- https://portworx.com/mysql-kubernetes/

