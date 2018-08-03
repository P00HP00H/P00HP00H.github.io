---
layout: post
category: "kubernetes"
title: "Kubernetes 설치(작업자 노드)"
tags: ["kubernetes"]
---


이번 포스팅에서는 마스토 노드가 아닌 작업자 노드에서 kubernetes를 설치해보려고 한다. 마스터 노드일 때와 상당히 비슷하다. 환경은 저번 포스팅과 같이 VM의 CentOS 7이다.



kubernetes를 설치하기 전에 먼저 update를 해준다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/0.JPG?raw=true" width="px">

업데이트를 마쳤으면 이번에도 역시 각 VM들을 구분해주기 위하여 hostnamectl 명령어로 이름을 지었는데 작업자 노드가 여러 개 있으면 그것들도 다 구분해야 하므로 'worker-node1'라는 이름을 주었고 exec bash로 실행시켜 주면 이름이 localhost에서 worker-node1로 바뀌었음을 확인할 수 있다. 그 다음은 selinux를 해제해준다.

setenforce 0

sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/20.JPG?raw=true" width="750px">

그 다음 밑처럼 방화벽 설정을 해준다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/21.JPG?raw=true" width="px">

마스터 노드에서 열어준 포트랑 약간 다르고 이를 반영하기 위해서 그리고 서비스를 재구동하기 위해서 마찬가지로 --reload를 해준다. 마스터 노드에서는 reload해주고 나서 modprobe br_netfilter 명령어를 실행시켜야 하기 때문에 reboot을 해줘야 하지만 작업자 노드에서는 그런 과정이 필요없기 때문에 reboot을 해줄 필요도 없다. 하지만 iptable에서 Bridge된 트래픽을 볼 수 있게 하기 위해 /proc/sys/net/bridge/bridge-nf-call-iptables을 echo '1'로 해서 활성화해준다.

echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables



그 다음은 Kubernetes 저장소를 구성하는 과정인데 마스터 노드 때도 언급했지만 Kubernetes 패키지는 기본 CentOS 7 및 RHEL 7 저장소(repository)에서 사용할 수 없기 때문에 아래 명령을 이용하여 저장소(repository) 파일을 만들어 패키지 저장소(repository)를 구성하는 것이다. 

vim /etc/yum.repos.d/kubernetes.repo

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/47.JPG?raw=true" width="px">

- **[kubernetes]** 
  : 저장소를 식별할 수 있는 id 로 여기에서는 "Kubernetes" 가 된다. yum 의 명령어 중에 저장소 목록을 보거나 특정 저장소를 지정하여 패키지를 설치하는 등의 저장소 관련 작업을 할 경우 필수 옵션으로 저장소의 id 를 넘겨줘야 하며 그 때 이 id를 사용하면 된다.

- **name**

  : 저장소의 이름이다.

- **baseurl**
  : 패키지를 설치하거나 업데이트 시 사용할 기본 저장소의 URL을 지정하며 fastestmirror 를 사용하여 동적으로 저장소를 찾지 않을 수 있지만 고정된 URL 의 저장소를 사용할 경우 baseurl로 설정해 주면 된다.

- **mirrorlist**

  : RHEL은 RedHat이 제공하는 중앙 저장소의 URL이 고정되어 있지만 CentOS 는 나라별로 미러 사이트들이 다르므로 빠른 속도로 저장소에 접속하려면 CentOS 를 설치한 국가에 따라 저장소 URL이 달라져야 한다. mirrorlist 가 설정되어 있으면 fastestmirror라는 yum 플러그인이 동작하여 가장 빠른 미러 사이트를 찾아서 저장소 URL 을 변환해 준다.

- **enabled**
  : 1로 설정되어 있으면 이 저장소를 사용하겠다는 의미이며 0일 경우 사용하지 않겠다는 의미이다.<br> --enablerepo, --disablerepo 옵션으로 실행 시점에 사용 여부를 변경할 수 있다.

- **gpgcheck**, **repo_gpgcheck**

  : yum 으로 패키지 설치시 GPG(GNU Privacy Guard)  서명 검증을 할 지 여부이며 yum.conf 처럼 기본 설정은 서명 검증하는 모드인 1이며 0일 경우 서명 검증을 하지 않는다.

- **gpgkey**

  : gpgcheck 를 할 경우 사용할 서버의 공개키 경로를 설정한다. 일반적으로 저장소를 제공하는 측에서 GPG 키를 같이 배포하므로 저장소 추가시 공개키를 같이 받아서 설정해 주면 된다. 밑에 적힌 두 URL이 공개키 경로이다.

패키지 저장소(repository)를 다 만들었으면 kubeadm 및 docker 패키지를 설치한다.

yum install kubeadm docker -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/23.JPG?raw=true" width="px">

다 설치했으면 이제 docker를 서비스 시작 및 사용가능(enable)하도록 해줘야 하는데(마스터 노드에서는 kubectl을 사용해야 하기 때문에 kubelet도 서비스 시작 및 사용가능(enable)하도록 해줘야 했지만 작업자 노드에서는 docker만 해주면 된다.)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/24.JPG?raw=true" width="750px">

오류가 난다. 똑같이 docker 상태를 확인해보니 역시나 docker가 죽어 있다. 원래 restart를 하면 다시 살아나야 하는데 살아나지 않아 reboot를 해준 후 다시 restart해줬더니 잘 동작했다(restart 했는데 잘 살아나면 굳이 reboot 해 줄 필요 X). 그 다음 enable도 해준다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/26.JPG?raw=true" width="750px">

이제 마스터 노드에 작업자 노드를 join 시켜볼 차례이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/37.JPG?raw=true" width="750px">

여기가 저번 포스팅에서 봤던 마스터 노드 화면인데 빨간색 표시된 부분을 통째로 복사해 그대로 작업자 노드에 붙여넣기하면 된다. 하지만 join해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/35.JPG?raw=true" width="750px">

certificated has expired or is not yet valid라고 뜬다. 즉, 마스터에 join할 때 인증서 같은 거로 인증을 하는데 이가 파괴되었거나 유효하지 않다는 뜻이다. 검색해보니 이는 서로 시간이 안 맞을 때 발생하는 것이라고 한다. 그래서 마스터 노드와 작업자 노드의 시간을 비교해보니

마스터 노드

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/38.JPG?raw=true" width="px">

작업자 노드

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/39.JPG?raw=true" width="px">

역시나 시간이 서로 맞지 않았다. 마스터 노드의 시간이 잘못된 상태에서 설정이 된 상태라서 작업자 노드 시간은 현재 시간과 잘 맞았음에도 마스터 노드 입장에서는 잘못된 시간으로 인식되어 저런 오류가 났던 것이다. 그럼 마스터 노드의 설정을 다시 해주자. 지금부터는 마스터 노드 화면이다.

먼저 시간을 바로 잡아주자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/42.JPG?raw=true" width="750px">

혹시 UTC로 되어있지 않나 해서 봤더니 Asia/Seoul로 잘 연결되어 있었다. 즉, 지역은 바꿀 필요 없고 시간만 바꿔주면 된다는 뜻이다. timedatectl 명령어로 시간을 바꿔준다. 시간을 맞춰주었으면 마스터 노드를 다시 설정해줘야 하기 때문에 마스터 노드를 다시 reset해줘야 한다. 

kubeadm reset

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/40.JPG?raw=true" width="750px">

해당 명령어로 reset을 해준 후 다시 마스터 노드를 초기화시켜준다(설정해준다).

kubeadm init

초기화해 줄 때 원래는 running with swap on is not supported. Please disable swap 이라는 메시지가 떠서 swapoff -a를 해준 후 다시 kubeadm init을 입력해야 하는데 reset해주고 다시 init해주면 이게 안 뜨고 한 번에 초기화되기도 한다. 상황에 따라 유동적으로 하면 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/41.JPG?raw=true" width="750px">

다시 새로운 값으로 초기화가 됐다. 저번 포스팅에서 했던 것처럼 똑같이 해주면 된다.

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf \$HOME/.kube/config

sudo chown \$(id -u):\$(id -g) \$HOME/.kube/config

그 다음에 Pod Network를 배포해준다.

export kubever=$(kubectl version | base64 | tr -d '\n')

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$kubever"

그 다음 마지막 부분(kubeadm join ~)을 그대로 복사해서 해당 노드에 입력해주면 마스터에 노드가 연결된다. 지금부터는 다시 작업자 노드이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/48.JPG?raw=true" width="750px">

그대로 복사해주면 이 노드가 마스터에 잘 join됐다고 나온다. 성공한 것이다. 단, 여기서도 running with swap on is not supported. Please disable swap가 뜨기도 한다. worker-node1은 안 떴으나 worker-node2는 떴다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/45.JPG?raw=true" width="750px">

이 때는 swapoff -a 해준 후 다시 kubeadm join ~을 해주면 된다. 

마스터 노드로 가서 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/44.JPG?raw=true" width="px">

worker-node1이라는 이름의 노드가 생성됐다. 이런 식으로 추가하고 싶은 노드들을 다 추가해주면 된다. 필자는 최종적으로 worker-node2라는 노드도 추가해서 총 마스터 노드 포함 총 3개의 노드를 만들 것이다. worker-node2는 worker-node1처럼 똑같이 해주면 되고 다 해주면 최종적으로는

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/46.JPG?raw=true" width="px">

이런 식으로 계속 마스터에 연결(join)하고 싶은 노드들을 연결해 줄 수 있다. 

<br><br><br>

참고 블로그 :

- https://www.linuxtechi.com/install-kubernetes-1-7-centos7-rhel7/

