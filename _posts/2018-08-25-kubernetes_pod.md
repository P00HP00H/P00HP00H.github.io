---
layout: post
category: "kubernetes"
title: "Pod란?"
tags: ["kubernetes"]
---


이번 포스팅에서는 Pod에 대해 알아보고자 한다. 환경은 저번 포스팅과 같이 마스터 노드, 작업자 노드 모두 VM의 CentOS 7이다.

쿠버네티스를 이해하기 위해서는 먼저 클러스터의 구조를 이해해야 한다. 클러스터 전체를 관리하는 컨트롤러로써 마스터 노드가 존재하고, 컨테이너가 배포되는 머신(가상 머신이나 물리적인 서버 머신)인 노드(작업자 노드)가 존재한다. 쿠버네티스를 이해하기 위해서 또한 중요한 게 오브젝트(object)인데, 쿠버네티스는 가장 기본적인 구성단위가 되는 기본 오브젝트와 이러한 기본 오브젝트를 생성하고 관리함과 동시에 이에 추가적인 기능을 가진 컨트롤러(Controller)로 이루어진다. 그리고 이러한 오브젝트의 스펙(설정) 이외에 추가 정보인 메타 정보들로 구성이 되어 있다. 



- **기본 오브젝트(Basic Object)**

  : 여기서 말하는 오브젝트가 모두 기본 오브젝트(Basic Object)이며 쿠버네티스에 의해서 배포 및 관리되는 가장 기본적인 오브젝트는 컨테이너화되어 배포되는 애플리케이션의 워크로드를 기술하는 오브젝트로 Pod, Service, Volume, Namespace 4가지가 있다. 간단하게 설명 하자면 Pod는 컨테이너화된 애플리케이션, Volume은 디스크, Service는 로드밸런서 그리고 Namespace는 패키지명 정도로 생각하면 된다. 이번 포스팅에서는 Pod에 대해서만 살펴볼 것이다.

- **오브젝트 스펙(Object Spec)**

  : 오브젝트들은 모두 오브젝트의 설정 정보를 기술한 오브젝트 스펙(Object Spec)으로 정의가 되고, 커맨드 라인을 통해서 오브젝트 생성시 인자로 전달하여 정의를 하거나 또는 yaml(또는 yml)이나 json 파일로 스펙을 정의할 수 있다.




### Pod

Pod 는 쿠버네티스에서 가장 기본적인 배포 단위로 컨테이너를 포함하는 단위이기도 하며 하나 이상의 컨테이너를 포함한다.  쿠버네티스는 컨테이너를 개별적으로 하나씩 배포하는 것이 아니라 Pod 단위로 배포한다.

아래는 간단한 Pod를 정의한 오브젝트 스펙이다. 하나하나 살펴보면

(보기 쉽게 :set number를 이용했습니다.)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/55.JPG?raw=true" width="px">



- **apiVersion**

  : 이 스크립트를 실행하기 위한 쿠버네티스 API 버전으로 보통 v1을 사용한다.

- **kind**

  : 리소스의 종류를 정의하는 곳으로 Pod를 정의하려고 하기 때문에, Pod라고 넣는다. 어떤 오브젝트냐에 따라 정의가 달라진다.

- **metadata**

  : 이 리소스(Pod)의 라벨(Label)이나 리소스의 이름 등 각종 메타 데이타를 넣는다. 여기서는 Pod의 이름을 pooh-db-nginx-pod라고 정의했으며 label을 pooh-db-nginx-app라고 정의하였다. 이 라벨은 추후에 포스팅할 Service에 필요한데 Pod를 서비스할 때 Service 오브젝트에 라벨 셀렉터(label selector)로 해당 라벨을 지정해주면 지정해준 라벨의 Pod만 선택해서 서비스하게 되는 것이다(좀 더 자세히 알고 싶다면 뒤의 Service 포스팅을 참고해주세요!!). 약간 그룹화 비슷하게 생각하면 된다. 

- **spec**

  : 이 리소스(Pod)에 대한 상세한 스펙을 정의하는 곳으로 Pod가 하나 이상의 컨테이너를 포함한다고 해서 2개의 컨테이너를 포함시켜 보았다. 그 밑은 spec에 대한 상세 내용들이다.

- **spec.containers**

  : Pod가 관리할 컨테이너를 정의하는 부분이다.

- **spec.containers.name**

  : Pod가 관리할 컨테이너의 이름을 정의한다.

- **spec.containers.image**

  : Pod가 관리할 컨테이너에 해당하는 image이다.

- **spec.containers.env**

  : Pod가 관리할 컨테이너의 환경설정을 해주는 부분이다. nginx의 경우 이러한 설정을 해줄 필요 없지만 거의 대부분의 컨테이너들이 이러한 설정을 해줘야 한다. mysql, django, python 등 모두 env 설정을 해주지 않고 진행했더니 CrashLoopBackOff 에러가 뜬다. 이에 대한 자세한 내용은 다음 포스팅인 CrashLoopBackOff를 참고하기 바란다.

- **spec.containers.ports**

  : Pod가 관리할 컨테이너가 사용할 포트를 설정해주는 부분으로 mysql을 사용할 것이기 때문에 3306으로 설정해주었고 nginx의 경우는 웹서버로 80번 포트를 사용하기 때문에 80으로 설정해주었다.

- **spec.nodeSelector**

  : Pod가 특정 노드에서만 실행되도록 정의하는 부분으로 hostname=worker-node2라고 정의했으므로 hostname(노드 이름)이 worker-node2인 노드에만 실행(배포)되도록 해준다. 이를 지정해주지 않으면 임의의 노드에 Pod가 실행된다. 어느 노드에서 실행되어도 상관없다면 굳이 정의해 줄 필요는 없다.<br>단, 이 옵션을 사용하기 위해선 해당 노드에 label을 추가해줘야 한다. 이 nodeSelector가 노드의 label을 보고 해당 노드에 Pod를 서비스하기 때문이다.

  kubectl get nodes [node명] hostname=[label명]

  node명은 kubectl get nodes 명령어를 통해 node들을 볼 수 있는데 이들 중 하나를 택하면 된다. 뒤의 label명은 짓고 싶은대로 지으면 되는데 필자의 경우는 이름과 label을 그냥 똑같이 했다(hostname=[label명]의 hostname은 nodeSeletor에 hostname으로 구별하기 때문에 저렇게 써준 거고 다른 것들을 쓸 수도 있다. 추후에 또 다룰 기회가 있으면 그 때 다시 설명하겠다).

  kubectl get nodes worker-node2 hostname=worker-node2

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/68.JPG?raw=true" width="px">

  노드에 지정한 label을 삭제할 수도 있다. 삭제하는 방법은

  kubectl label nodes worker-node2 hostname-

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/69.JPG?raw=true" width="px">

  이렇게 노드에 라벨을 지정해주지 않고 nodeSeletor 옵션을 주게 되면 계속 이런 대기상태가 된다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/70.JPG?raw=true" width="px">

  ​



이제 pod.yml에 정의한 Pod를 오브젝트로 생성해 줄 차례이다. Kubernetes에서 오브젝트 생성을 위한 명령어는 

kubectl create -f 파일명.yml   또는   kubectl apply -f 파일명.yml

필자는 kubectl create -f pod.yml을 사용했다. 이를 입력하면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/56.JPG?raw=true" width="px">

이렇게 아까 정의한 Pod의 이름(pooh-db-nginx-pod)대로 오브젝트가 생성됐다.

잘 생성되었는지 확인해보면

kubectl get pods

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/57.JPG?raw=true" width="750px">

-o wide를 추가해주면 더 자세한 정보들을 볼 수 있고 아까 Pod에 특정 노드(worker-node2)에 지정되도록 설정해줘서 worker-node2에 배포되었음을 확인할 수 있다. nodeSeletor를 설정해주지 않으면 현재 worker-node1, worker-node2 이렇게 있는 상태인데 worker-node1에 배포될 수도 있다.

하지만, 이렇게 Pod 오브젝트만 생성해주면 외부에서 Pod 내부에서 실행되는 컨테이너의 응용 프로그램(application)에는 접근할 수 없다. 외부에서 접근이 가능하도록 노출을 시켜줘야 한다(해당 서비스를 외부의 사용자가 사용할테니까). 이를 서비스시킨다고 하는데, 현재 Pod 오브젝트에 대해서 다루는 것처럼 나중에 Service 오브젝트에 대해서도 다룰 것이다. 지금은 그냥 Pod를 서비스하기 위해 외부에 노출시켜야 한다는 정도로만 이해하고 넘어가자. 일단 Pod를 외부로 노출시켜주는 명령어는

kubectl expose pods [Pod명] --name=[Service name] --type=Nodeport

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/58.JPG?raw=true" width="750px">

해당 Pod를 [Service name]이라는 이름으로 서비스한다는 의미이다. 제일 중요한 게 맨 뒤의 --type=Nodeport이다. 이렇게 type을 Nodeport로 바꿔줌으로 인해서 외부에서 접근이 가능해지기 때문이다.

다음 그림은 클러스터 외부를 가정하기 위해 만든 VM Ubuntu이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/71.JPG?raw=true" width="px">

kubernetes를 설치하지도 않았고 서비스되고 있는 노드의 IP와 포트는 안다는 가정 하에 curl 명령어를 통해 봤더니 서비스가 잘 되고 있음을 확인할 수 있다. 

지금은 이렇게 간단하게 했지만 여기도 Service.yml을 만들어 오브젝트로 생성하면 더 많은 설정들을 담아 서비스할 수 있다. 아까도 언급했지만 추후에 다룰 예정이다.

잘 서비스되는지 확인해보려면

kubectl get service

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/59.JPG?raw=true" width="750px">

이 역시 -o wide로 좀 더 자세하게 볼 수 있으며 service 대신 svc로 써도 있다(kubectl get svc).

그럼 worker-node2로 잘 배포가 되었는지 worker-node2로 가서 직접 확인해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/60.JPG?raw=true" width="750px">

밑에 아까 pod.yml에서 설정한 이름들이 보인다. 설정해준대로 잘 배포가 된 것이다. 

잘 돌아가고 있는지 docker exec 명령어로 컨테이너에 접속해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/61.JPG?raw=true" width="px">

pooh-db-container라는 이름의 mysql 컨테이너에 접속해봤는데 잘 실행이 된다. nginx 컨테이너도 이런 식으로 접속할 수 있다.

지금까지는 Pod와 expose 명령어를 통한 Service를 생성해봤다면 이제 삭제하는 방법에 대해 보도록 하자. 방법은 2가지다.

kubectl delete -f 파일명.yml   또는  kubectl delete pod [Pod명]  

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/66.JPG?raw=true" width="px">

필자는 후자의 명령어를 사용했다. 만약 전자의 명령어를 사용했다면 pod.yml로 Pod를 생성했기 때문에 kubectl delete -f pod.yml을 입력해주면 된다. Pod가 잘 삭제됐는지 확인해보면 No resources found, 즉, 잘 삭제되었다는 것을 알 수 있다. Service의 경우도 마찬가지이다. expose를 통해 Service를 생성했으니 unexpose같은 게 아닐까하고 처음에 생각했지만 어쨌든 Service를 생성한 것이기 때문에 Service를 삭제해주면 된다. Pod 삭제할 때와 똑같다.

kubectl delete -f 파일명.yml   또는   kubectl delete service [Service명]

나중에 포스팅할 Service때는 Service.yml이라는 파일을 만들어 Serivce 오브젝트를 생성해 서비스를 할 건데 이 때 전자의 방법을 사용할 수 있다. kubectl delete -f Service.yml<br>하지만 지금은 그게 아니므로 후자의 방법을 택하면 된다.

kubectl delete service pooh-pod-service

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/67.JPG?raw=true" width="px">

아까 위에서 봤던 Nodeport 유형의 pooh-pod-service가 없다. 잘 삭제된 것이다.



※ Pod 안에 한 개 이상의 컨테이너를 가질 수 있다고 했는데 왜 개별적으로 하나씩 컨테이너를 배포하지 않고 여러 개의 컨테이너를 Pod 단위로 묶어서 배포하는 것일까? 그 이유는 Pod가 다음과 같은 재미있는 특징을 갖고 있기 때문이다.

- 1. Pod 내의 컨테이너는 IP와 Port를 공유한다. 

  : 예를 들어 2개의 컨테이너가 하나의 Pod를 통해서 배포되었다고 할 때, localhost를 통해서 통신이 가능하다.   만약 컨테이너 A가 80, 컨테이너 B가 6543으로 배포가 되었다고 하면 컨테이너 B에서 컨테이너 A를 호출할때는 localhost:80으로 호출하면 되고, 반대로 A에서 B를 호출할 때는 localhost:6543로 호출하면 된다.  위에서 만든 pod.yml로 실험을 해보자. 아까의 얘기를 다시 말해보면 현재 worker-node2에 pooh-db-container가 3306번 포트로 pooh-nginx-container가 80번 포트로 2개의 컨테이너가 배포된 상태이다. pooh-db-container에서 pooh-nginx-container를 호출할 때 localhost:80으로 호출할 수 있다. nginx는 웹서버이므로 눈으로 확인하기 쉽게 curl 명령어를 이용해보자(단, 컨테이너에 curl 명령어가 없어 apt-get install로 설치했습니다. 이 과정은 생략하고 썼음을 알려드립니다).

  먼저 pooh-db-container에 접속한다.

  docker exec -it 8ec122944cd6 /bin/bash

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/62.JPG?raw=true" width="px">

  그 다음 curl 명령어로 확인해보면

  curl localhost:80

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/63.JPG?raw=true" width="px">

  nginx가 잘 나옴을 확인할 수 있다. 원래는 각 컨테이너마다 IP가 달라 curl 명령어에 해당 컨테이너의 IP를 입력해야 하는데, 그럴 필요 없이 각 컨테이너들이 같은 Pod에 있으면 이렇게 IP를 공유하기 때문에 localhost로 쓸 수 있다(물론 IP를 직접 입력해도 된다). 실제로 각 컨테이너에 접속해서 net-tools를 설치한 후 ifconfig로 IP를 조회해보면 IP가 같다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/64.JPG?raw=true" width="px">

  이는 pooh-db-container이고

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/65.JPG?raw=true" width="px">

  이는 pooh-nginx-container이다. 둘 다 10.40.0.1을 사용하고 있음을 확인할 수 있다. 지금까지 잘 따라왔다면 이 IP를 보고 아까 kubectl get pods -o wide에서 봤던 즉, 우리가 배포한 Pod의 IP임을 알 수 있다.

  ​

- 2. Pod 내에 배포된 컨테이너 간에는 디스크 볼륨을 공유할 수 있다. 

  여기는 뒤의 Volume 포스팅을 참고해주세요!

  ​



이러한 특징들로 인해 컨테이너를 Pod 단위로 배포하는 것이다. 다음 포스팅에서는 CrashLoopBackOff 오류에 대해서 다뤄보겠다.

<br><br><br>

참고 블로그 :

- http://bcho.tistory.com/1256?category=731548
- http://tech.cloudz-labs.io/posts/kubernetes/getting-start/
- https://www.linuxtechi.com/deploy-pod-replication-controller-service-kubernetes-1-7-on-centos-7/
- https://developer.ibm.com/kr/cloud/2018/01/25/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EC%8B%A4%EC%8A%B5/
- https://kubernetes.io/docs/concepts/storage/volumes/

