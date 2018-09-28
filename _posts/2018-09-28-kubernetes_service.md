---
layout: post
category: "kubernetes"
title: "Service"
tags: ["kubernetes"]
---

이번 포스팅에서는 Service에 대해서 다뤄보려고 한다. 환경은 저번 포스팅과 같이 모든 노드들은 VM의 CentOS 7이다.


<br>
### Service

Pod와 볼륨을 이용하여 컨테이너들을 정의한 후 해당 Pod를 서비스로 제공할때, 일반적인 분산 환경에서는 하나의 Pod로 서비스 하는 경우는 드물고, 여러 개의 Pod를 서비스하면서, 이를 로드밸런서를 이용해 하나의 IP와 포트로 묶어서 서비스를 제공한다.

Pod는 Node 내부에서 고유한 IP를 가지지만 동적으로 생성이 되고, 장애가 생기면 자동으로 restart 되면서 IP가 바뀌게 된다. 즉, 쉽게 생성/삭제되는 특성 상, 로드밸런서에서 Pod의 목록을 지정할 때는 IP주소를 이용하는 것은 어렵고 또한 오토 스케일링으로 인하여 Pod 가 동적으로 추가 또는 삭제되기 때문에, 이렇게 추가/삭제된 Pod 목록을 로드밸런서가 유연하게 선택해 줘야 한다. 그래서 사용하는 것이 Pod 포스팅에서 언급했던 라벨(label)과 라벨 셀렉터(label selector)이다. 

Service(서비스)를 정의할 때, 어떤 Pod를 서비스로 묶을 것인지를 정의하는 것을 label selector라고 한다. 각 Pod를 생성할 때 메타데이타 정보 부분에 label을 정의할 수 있다. Service는 같은 Cluster 내에서 Pod가 어떤 Node에 생성되었는지와 관계 없이 label selector에서 특정 label을 가지고 있는 Pod를 선택하여 서비스에 묶게 된다. 이들은 Service 오브젝트를 통해 생성하고 활용할 수 있다.



Service 오브젝트의 type에는 다음과 같은 종류가 있다.

- Cluster-IP 

  : type을 지정해주지 않으면 Cluster-IP가 디폴트로 설정이 되며 해당 서비스에 클러스터 IP(내부 IP)를 할당한다. 클러스터 내부에서는 접근이 가능하지만 클러스터 외부에서는 외부 IP를 할당받지 못했기 때문에 접근이 불가능하다. 쉽게 생각하면 배포된 노드에만 적용된다고 생각하면 된다. 한 번 실험해보도록 하자.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/55.JPG?raw=true" width="px">

  현재 pod.yml이다. 이를 Cluster-IP로 노출시켜보자(Pod 생성하는 과정은 생략하겠다).

  kubectl expose pods [Pod명] --name=[Service name] --type=ClusterIP

  이 역시 물론 Service.yml로 서비스할 수도 있다.

  kubectl expose pods pooh-db-nginx-pod --name=pooh-pod-service --type=ClusterIP

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/74.JPG?raw=true" width="750px">

  Service 타입이 Cluster-IP임을 알 수 있다. 한 번 마스터 노드에서 Cluster-IP로 curl을 이용해 nginx를 띄워보자.

  curl 10.103.210.38:80

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/75.JPG?raw=true" width="px">

  역시나 되지 않는다. 그러면 배포된 노드에서 똑같이 실행해보자.

  curl 10.103.210.38:80

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/103.JPG?raw=true" width="px">

  worker-node2에 배포했기 때문에 worker-node2에서 똑같이 해보니 잘 됐다.

  ​

- NodePort 

  : 각 Node에서 static 포트를 노출해 외부에서 접근이 가능하고 클러스터 외부에서 :의 형태로 request가 가능하다. 이 방식은 클러스터 IP로만 접근이 가능한 것이 아니라, 모든 노드의 IP와 포트를 통해서도 접근이 가능하다. Service.yml을 보면서 설명하겠다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/82.JPG?raw=true" width="px">

  ​

  - **apiVersion**

    : 이 스크립트를 실행하기 위한 쿠버네티스 API 버전으로 보통 v1을 사용한다.

  - **kind**

    : 리소스의 종류를 정의하는 곳으로 Service를 정의하려고 하기 때문에, Service라고 넣는다. 어떤 오브젝트냐에 따라 정의가 달라진다.

  - **metadata**

    : 이 리소스(Service)의 라벨(Label)이나 리소스의 이름 등 각종 메타 데이타를 넣는다. 여기서는 Service의 이름을 pooh-pod-service라고 정의했으며 label을 app: pooh-service-label이라고 정의하였다. 

  - **spec**

    : 이 리소스(Service)에 대한 상세한 스펙을 정의하는 곳으로 그 밑은 spec에 대한 상세 내용들이다.

  - **spec.ports**

    : Pod가 관리할 컨테이너를 정의하는 부분이다.

  - **spec.ports.name**

    : Service 오브젝트가 관리할 포트의 이름을 정의한다. 이름을 정의해주지 않으면 오류가 발생하므로 반드시 정의해줘야 한다.

  - **spec.ports.port**

    : Service 오브젝트가 사용하는 포트번호이다. 외부 사용자가 Serivce 오브젝트에 할당되는 IP와 함께 이 포트번호로 접속하면 서비스하고 있는 Pod에 접근할 수 있다.

  - **spec.ports.protocol**

    : 통신할 때 사용할 프로토콜을 정의해주는 부분이다.

  - **spec.port.targetPort**

    : Pod 내의 각 컨테이너들이 사용할 포트들을 의미한다. Pod.yml에서 컨테이너 부분에 지정해 준 포트랑 같다.

  - **spec.port.nodePort**

    : 이는 Pod가 배포된 노드의 IP에 이 nodePort를 이용하면 배포된 Pod에 접근할 수 있다.  이렇게 nodePort로 사용하면 직접 설정해줄 수 있지만 이를 쓰지 않으면 알아서 할당해준다.

  - **spec.selector**

    : 이 부분이 Pod 포스팅에서 잠깐 언급했던 라벨 셀렉터(label selector)이다.  Service 오브젝트가 이 라벨 셀렉터로 해당 라벨을 찾아 이 라벨을 가진 Pod들을 서비스 하는 것이다. 밑에 app: pooh-db-nginx-app이라고 되어있는데 app: pooh-db-nginx-app인 라벨을 가진 Pod를 서비스하겠다는 의미이다.

  - **spec.selector.app**

    : 이 부분을 가지고 서비스할 Pod(또는 Pod 그룹)를 찾는다. 즉, 라벨이 app: pooh-db-nginx-app인 Pod를 찾는 것이다. 또한 Pod에서 app: pooh-db-nginx-app이라서 spec.selector.app인 것이지 host: pooh-db-nginx-host였으면 spec.select.host가 된다. 

  - **spec.type**

    : Service 오브젝트의 type을 정의해주는 부분으로 ClusterIP, NodePort, LoadBalancer, ExternalName 등이 있다.

    ​

  Service.yml의 전체 구조를 보자. 아래와 같이 pooh-pod-service라는 서비스를 NodePort 타입으로 선언을 하고, nginx의 nodePort를 30001로 설정하면 아래 설정에 따라 클러스터 IP의 8080포트로도 접근이 가능하지만, 해당 노드(worker-node2)의 30001 포트로도 서비스에 접근할 수 있다. 이를 그림으로 나타내보면

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/80.JPG?raw=true" width="750px">

  ​

  위 그림처럼 적용되는지 직접 확인해보자. nginx가 보여지기 쉬우니까 nginx로 하겠다.

  1) 클러스터 IP의 8080포트로 접속

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/84.JPG?raw=true" width="750px">

  클러스터 IP는 배포된 노드에만 적용된다 했으므로 현재 배포된 곳인 worker-node2에서 curl을 이용해 해당 클러스터 IP와 포트번호를 입력하면 잘 나오는 것을 확인할 수 있다.

  ​

  2) 노드의 30001포트로 접속

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/85.JPG?raw=true" width="px">

  /etc/hosts에 worker-node2의 IP를 등록해놨기에 저렇게 이름을 쓴 것이고 원래는 노드의 IP를 입력하면 된다. 각 노드의 IP는 kubectl get nodes -o wide로 확인할 수 있고 Pod가 배포된 노드의 IP를 확인하고 싶으면 kubectl get pods -o wide를 입력해주면 된다. 포트번호 30001과 함께 입력해주면 잘 나오는 것을 확인할 수 있다.

  ​

  ​

  번외) 갑자기 이런 의문이 들었다. Service는 라벨 셀렉터를 통해 Pod를 서비스한다. 만약 worker-node1에 배포된 Pod A와 worker-node2에 배포된 Pod B가 모두 aa라는 라벨을 가지고 있는 상태에서 Service가 aa라는 라벨을 가진 Pod를 서비스한다면 2개의 노드에 서비스가 되고 있는 상태이다. 이 서비스를 aa라는 라벨을 가지면서 동시에 특정 노드(worker-node1이나 worker-node2)에 있는 Pod만 서비스할 수 있는지가 궁금했다는 의미이다. 그래서 pod.yml의 옵션처럼 service.yml에도 nodeSelector라는 옵션을 줘서 실행해봤다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/104.jpg?raw=true" width="px">

  이렇게 해주고 Service 오브젝트를 생성해봤는데

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/83.JPG?raw=true" width="750px">

  nodeSelector는 unknown field, 즉, 알 수 없는 필드라고 나온다. 이렇게는 안 되는 것을 확인할 수 있었다.

  ​

- LoadBalancer 

  : 특정 클라우드 provider(GCE/AWS)의 LoadBalancer에게 Service 오브젝트를 노출시키는 방법으로 보통 클라우드 벤더에서 제공하는 설정 방식이며 외부 IP를 가지고 있는 로드밸런서를 할당한다. AWS나 Google Cloud Engine과 같은 퍼블릭 클라우드에 Kubernetes가 설치된 경우에는 Loadbalancer 방식으로 설정해주면 좋다.  물론 퍼블릭 클라우드가 아니어도 LoadBalancer를 구매한 서버라던지 직접 LoadBalancer를 만든 서버에도 설정해 줄 수 있고, 그런 설정이 없어도 LoadBalancer 방식으로 서비스를 할 수는 있다. 그런 것들에 상관없이 일단 만들어보면 이 방식 역시 외부 IP를 가지고 있기  때문에, 클러스터 외부에서 접근이 가능하다.

  (※ 로드밸런싱(Load Balancing) : 하나의 인터넷 서비스가 발생하는 트래픽이 많을 때 여러 대의 서버가 분산처리하여 서버의 로드율 증가, 부하량, 속도 저하 등을 고려하여 적절히 분산처리하여 해결해 주는 서비스)

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/105.jpg?raw=true" width="px">

  이 역시 외부에서 접근이 가능하기 때문에 맨 밑의 type만 LoadBalancer이고, 나머지는 NodePort와 같은 방식으로 설정해 준 다음 서비스를 해주면 된다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/87.JPG?raw=true" width="750px">

  이 역시 노드의 IP로도 클러스터의 IP로도 접근할 수 있다.

  1) 클러스터 IP의 8080포트로 접속

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/88.JPG?raw=true" width="px">

  ​

  2) 노드의 30001포트로 접속

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/89.JPG?raw=true" width="px">

  ​

  하지만 이렇게 LoadBalancer를 제공하는 클라우드의 IP를 설정해주지 않고 type만 LoadBalancer로 지정해주면 type이 NodePort인 경우와 다를 게 없다. 그래서 실제로 클라우드의 LoadBalancer를 이용할 때는 이런 식으로 지정해줘야 한다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/106.jpg?raw=true" width="px">

  111.111.111.111이 LoadBalancer를 제공하는 클라우드의 IP이다. 추후에 이용할 클라우드가 있으면 ExternalName 방식과 함께 다시 포스팅하도록 하겠다.



<br><br><br>

참고 블로그 :

- http://bcho.tistory.com/1262?category=731548
- http://tech.cloudz-labs.io/posts/kubernetes/getting-start/
- https://kubernetes.io/docs/concepts/services-networking/service/

