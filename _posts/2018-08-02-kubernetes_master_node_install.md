---
layout: post
category: "kubernetes"
title: "Kubernetes 설치(마스터 노드)"
tags: ["kubernetes"]
---


이번 포스팅에서는 kubernetes를 설치해보려고 한다. 마스터 노드와 작업자 노드들이 있지만 마스터 노드에 대해서만 다루도록 하겠다. 환경은 VM의 CentOS 7이다.



kubernetes를 설치하기 전에 먼저 update를 해준다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/0.JPG?raw=true" width="px">

각 노드(VM)들을 구분해주기 위하여 hostnamectl 명령어로 'k8s-master'라는 이름을 주었고 exec bash로 실행시켜 주면 이름이 localhost에서 k8s-master로 바뀌었음을 확인할 수 있다. 구분을 할 수 있다면 따로 해주지 않아도 상관은 없다. 그 다음 selinux를 해제해준다.

setenforce 0

sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/1.JPG?raw=true" width="750px">

그 다음 밑의 그림처럼 방화벽 설정을 해준다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/2.JPG?raw=true" width="px">

RHEL 7 부터는 방화벽을 관리하는 데몬이 firewalld 로 변경되었고 방화벽 설정은 복잡한 iptables 명령어 대신 firewall-cmd(콘솔), firewall-config(X-Windows) 명령어를 사용하는 것을 권장한다고 한다. firewall-cmd는 iptables 를 쓰기 쉽게 하는 wrapper이고 복잡한 규칙을 사용할 경우 --direct 옵션으로 iptables 내부에 접근해야 한다.<br>--add-port는 포트를 열어준다는 뜻이고 이를 반영하기 위해서 그리고 서비스를 재구동하기 위해서 --reload를 해준다(서비스 재구동시 service iptables restart 대신 firewall-cmd 명령어를 사용함). reload해주고 나서 reboot를 해줘야 modprobe br_netfilter 명령어가 작동한다. 그렇지 않으면 맨 밑에 나온 거처럼 FATAL: Module br_netfilter not found라는 오류가 발생한다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/30.JPG?raw=true" width="px">

reboot를 해주고 다음 명령어들을 입력해주면 잘 동작한다. br_netfilter는 bridge-netfilter라고 해서 {ip, ip6, arp} table이 802.1Q VLAN 또는 PPPoE 헤더에 캡슐화 된 경우에도 Bridge된 IPv4 / IPv6 / ARP 패킷을 필터링한다. 이렇게 하면 상태 보존형 투명 방화벽의 기능을 사용할 수 있으며, 모든 필터링, 로깅 및 NAT 기능 역시 Bridge된 프레임에서 사용할 수 있다. 또한 br_netfilter를 설치하면 모든 로컬 호스트가 인터넷에 직접 연결되어 있다고 간주한다. Linux 커널 3.18-rc1 이후로, bridge-netfilter를 활성화하려면 br-netfilter를 modprobe해줘야 하기 때문에 저렇게 명령어를 입력한 것이다.<br>(modprobe : 적재 가능한 커널 모듈을 리눅스 커널에 추가하거나 커널로부터 제거하는데 사용되는 Linux 프로그램)

밑에 echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables는

{ip, ip6, arp} table에서 Bridge된 트래픽을 볼 수 있게 하기 위해 /proc/sys/net/bridge/bridge-nf-call-iptables을 echo '1'로 해서 활성화해주는 것이다(물론 ip6, arp는 bridge-nf-call-iptables 대신에 bridge-nf-call-ip6tables, bridge-nf-call-arptables를 써줘야 함). 여기서는 iptable에서 Bridge된 트래픽만 설정해주었다.



그 다음은 Kubernetes 저장소를 구성하는 과정인데 Kubernetes 패키지는 기본 CentOS 7 및 RHEL 7 저장소(repository)에서 사용할 수 없기 때문에 아래 명령을 이용하여 저장소(repository) 파일을 만들어 패키지 저장소(repository)를 구성하는 것이다. 

vim /etc/yum.repos.d/kubernetes.repo

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/3.JPG?raw=true" width="px">

- **[kubernetes]** 
  : 저장소를 식별할 수 있는 id 로 여기에서는 "Kubernetes" 가 된다. yum 의 명령어 중에 저장소 목록을 보거나 특정 저장소를 지정하여 패키지를 설치하는 등의 저장소 관련 작업을 할 경우 필수 옵션으로 저장소의 id 를 넘겨줘야 하며 그 때 이 id를 사용하면 된다.

- **name**

  : 저장소의 이름이다.

- **baseurl**
  : 패키지를 설치하거나 업데이트 시 사용할 기본 저장소의 URL을 지정하며 fastestmirror 를 사용하여 동적으로 저장소를 찾지 않을 수 있지만 고정된 URL의 저장소를 사용할 경우 baseurl로 설정해 주면 된다.

- **mirrorlist**

  : RHEL은 RedHat이 제공하는 중앙 저장소의 URL이 고정되어 있지만 CentOS 는 나라별로 미러 사이트들이 다르므로 빠른 속도로 저장소에 접속하려면 CentOS 를 설치한 국가에 따라 저장소 URL이 달라져야 한다. mirrorlist 가 설정되어 있으면 fastestmirror라는 yum 플러그인이 동작하여 가장 빠른 미러 사이트를 찾아서 저장소 URL 을 변환해 준다.

- **enabled**
  : 1로 설정되어 있으면 이 저장소를 사용하겠다는 의미이며 0일 경우 사용하지 않겠다는 의미이다.<br>--enablerepo, --disablerepo 옵션으로 실행 시점에 사용 여부를 변경할 수 있다.

- **gpgcheck**, **repo_gpgcheck**

  : yum 으로 패키지 설치시 GPG(GNU Privacy Guard)  서명 검증을 할 지 여부이며 yum.conf 처럼 기본 설정은 서명 검증하는 모드인 1이며 0일 경우 서명 검증을 하지 않는다.

- **gpgkey**

  : gpgcheck 를 할 경우 사용할 서버의 공개키 경로를 설정한다. 일반적으로 저장소를 제공하는 측에서 GPG 키를 같이 배포하므로 저장소 추가시 공개키를 같이 받아서 설정해 주면 된다. 밑에 적힌 두 URL이 공개키 경로이다.

패키지 저장소(repository)를 다 만들었으면 kubeadm 및 docker 패키지를 설치한다.

yum install kubeadm docker -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/4.JPG?raw=true" width="px">

다 설치했으면 이제 kubectl 및 docker를 서비스 시작 및 사용가능(enable)하도록 해줘야하는데

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/5.JPG?raw=true" width="750px">

막상 해보면 이런 오류가 난다. 그래서 docker 상태를 확인해보니

systemctl status docker.service

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/6.JPG?raw=true" width="750px">

docker가 죽어 있다. 원래 restart를 하면 다시 살아나야 하는데 계속 살아나지 않아 reboot를 해준 후 다시 restart해줬더니 잘 동작했다(restart 했는데 잘 살아나면 굳이 reboot 해 줄 필요 X).

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/7.JPG?raw=true" width="px">

이게 VM이라서 그런 건지 아니면 실서버에서도 이렇게 적용되는지는 잘 모르겠다. reboot을 해준 후 못해줬던 restart 및 enable을 다시 해주면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/8.JPG?raw=true" width="750px">

이렇게 명령어가 잘 동작한다. 이제 kubeadm init 명령어를 통해 Kubernetes Master를 초기화해줘야 한다.

kubeadm init

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/9.JPG?raw=true" width="750px">

하지만 맨 밑을 보면 running with swap on is not supported. Please disable swap 이라는 메시지가 떴다. 이는 swap 파일의 구동을 지원하지 않아 swap을 중단시키라는 의미로 swap 파티션이나 swap 파일의 구동을 중단시키는 명령어인 swapoff -a를 써주면 된다.

swapoff -a

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/10.JPG?raw=true" width="px">

그러고 나서 다시 kubeadm init으로 Kubernetes Master를 초기화해주면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/11.JPG?raw=true" width="px">

아까와는 다르게 오류는 뜨지않고

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/33.JPG?raw=true" width="750px">

Your Kubernetes master has initialized successfully!라는 메시지가 떴다. 성공적으로 초기화했다는 뜻이다.

그런데, 그 밑에 너의 클러스터를 사용하기 위해서는 다음 명령어를 실행해야 한다고 한다.

mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf \$HOME/.kube/config

sudo chown \$(id -u):\$(id -g) \$HOME/.kube/config

만약 이를 실행하지 않고 kubectl을 사용하려고 하면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/31.JPG?raw=true" width="750px">

The connection to the server localhost:8080 was refused - did you specify the right host or port? 오류가 발생한다.

그 다음 줄에는 클러스터에 Pod Network를 배포해야된다고 나와 있으며 그 밑에는 root로써 각 동작하고 있는 노드들을 join시킬 수 있다고 나온다. 밑의 부분(kubeadm join ~)을 그대로 복사헤서 해당 노드에 입력해주면 마스터노드에 작업자 노드가 연결된다. 이 부분은 작업자 노드를 설치하는 과정에서 다시 보여주겠다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/13.JPG?raw=true" width="px">

이 명령어를 입력한 후 클러스터의 상태를 확인해보자.

kubectl get nodes

(참고 : kubectl은 Kubernetes를 운영하기 위한 CLI 도구로 상당히 많은 기능들을 제공한다.)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/14.JPG?raw=true" width="px">

일단은 node를 따로 연결하지 않았기 때문에 아무것도 연결되어 있지 않고 Master만 있다.  Host의 이름이 k8s-master여서 저렇게 나오는 것이고 aaa로 지정했으면 aaa로 나온다. 다음 포스팅에서 node에다가도 알기 쉽게 hostnamectl을 통해 이름을 지을 건데 지은 이름 그대로 클러스터에 나타나게 된다. 그리고 지금은 NotReady 상태이다.

kubectl get pods --all-namespaces

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/15.JPG?raw=true" width="px">

이는 Pod의 모든 리스트들을 조회하는 명령어인데 처음엔 이런 상태이다가 시간이 지나면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/32.JPG?raw=true" width="px">

이렇게 바뀌는데 위의 coredns들은 pending 즉, 계속 대기중인 상태이다. 클러스터 상태를 준비하고 coredns 상태를 실행하려면 다른 호스트의 컨테이너가 서로 통신하도록 Pod Network를 배포해야 한다. Pod Network는 작업자 노드 간의 Overlay 네트워크이다. <br>(Overlay Network : 기존 Network를 바탕으로 그 위에 구성된 또 다른 Network로 기존의 Network 위에 별도의 노드들(nodes)과 논리적 링크들(logical links)을 구성하여 이루어진 가상 Network이다. Overlay Network에서 이웃 노드들은 물리적인 이웃 노드가 아니라 논리적인 이웃 노드이다. 기존의 Network를 최대한 활용하여 보다 효율적인 Network 서비스를 제공할 수 있다.)

Pod Network를 배포하는 방법은 아래처럼 해주면 된다.

export kubever=$(kubectl version | base64 | tr -d '\n')

kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$kubever"

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/16.JPG?raw=true" width="750px">

이 다음에 다시 Pod를 조회해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kubernetes/17.JPG?raw=true" width="750px">

이제 coredns 부분도 Running 상태가 되었다. 아까 Master도 NotReady 상태였는데 지금은 Ready상태로 바뀐 것을 확인할 수 있다.



이제 마스터 노드는 설정이 완료되었다. 다음 포스팅에서는 작업자 노드 설정을 해보자.

<br><br><br>

참고 블로그 :

- https://www.linuxtechi.com/install-kubernetes-1-7-centos7-rhel7/
- https://www.lesstif.com/pages/viewpage.action?pageId=22053128
- https://www.lesstif.com/display/1STB/yum
- http://ebtables.netfilter.org/documentation/bridge-nf.html
