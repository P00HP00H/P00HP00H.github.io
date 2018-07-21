---
layout: post
category: "linux"
title: "iSCSI Target 설정"
tags: ["linux"]
---

저번 포스팅에서 iSCSI에 대해서 알아보았다. 이 iSCSI와 이전에 배웠던 LVM을 통합해서 어떤  한 노드에서 iSCSI를 통해 다른 노드에 있는 디스크를 자신의 디스크처럼 사용하고 이를 원래 있던 디스크들과 함께 LVM으로 묶어볼 것이다. 이해를 돕기 위해 그림을 그려보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/70.JPG?raw=true" width="750px">

두 노드가 있는데, 하나의 노드는 iSCSI target이고 하나의 노드는 이 iSCSI target에 접근하는 iSCSI initiator이다. 원래 iSCSI initiator에는 SSD1, SSD2만 있지만 iSCSI target의 SSD4를 iSCSI를 통해 자신의 디스크처럼 사용한다는 뜻이다. 결국 iSCSI initiator에는 SSD1, SSD2, SSD4가 있는 상태인 것이고 이를 LVM으로 묶어본다는 말이다. 이제 이를 실제로 구현해보자. 환경은 VM에서 진행하였고 iSCSI initiator은 CentOS 7, iSCSI target은 Ubuntu 16.04이다.  



먼저 update를 해준다. 

sudo apt-get update -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/60.JPG?raw=true" width="px">

update를 마쳤으면 이제 iSCSI 서버, 즉, target을 설치한다.

sudo apt-get install tgt -y

(참고 : CentOS는 yum install scsi-target-utils) 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/61.JPG?raw=true" width="750px">

다 설치했으면 iSCSI 서버를 실행한다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/62.JPG?raw=true" width="px">

서버를 실행하고 나면 iSCSI target이 될 디스크 즉, 위 그림의 SSD4에 해당하는 디스크의 이름을 먼저 지정해준다.

sudo tgtadm --lld iscsi --op new --mode target --tid 1 -T iqn-2018-07.pooh:storage.lun1

(참고 : 해당 target 삭제는 sudo tgtadm --lld iscsi --op delete --mode target --tid 1)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/63.JPG?raw=true" width="750px">

--tid 1은 밑에 보면 Target 1 : iqn-2018-07.pooh:storage.lun1의 Target 1을 의미한다. --tid 3이라고 지정하면 Target 3으로 지정이 된다. 그 뒤에 iqn-2018-07.pooh:storage.lun1이  이름인데 이에 대해 설명하면 iqn은 iSCSI qulified name의 약자로 target의 디스크가 유일한 이름을 가지도록 해주는 iSCSI 표준이며, initiator가 해당 장치를 찾기 위한 도메인이름이라고 생각하면 된다.

iqn.yyyy-mm.~[:identifier]

형식은 이러하며 iqn 뒤에 \~부분이 도메인, 뒤의 identifier는 디스크(볼륨) 이름이다. 하지만 이는 알아보기 위해서 이렇게 형식을 지정해준 것일 뿐 사실 iqn~ 이럴 필요 없이 aaa 이렇게 이름을 지어도 전혀 상관은 없다. 필자의 경우 2018년 7월에 만들었고 도메인 이름을 그냥 pooh라고 했으며 identifier부분은 저장소 lun(logical unit number)1이라는 뜻에서 저렇게 storage.lun1이라고 붙여준 것이다. 

그 밑을 보게되면 sudo tgtadm --lld iscsi --op show --mode target (또는 sudo tgt-admin -s) 명령어를 통해 iSCSI 서버의 데이터들을 확인할 수 있는데

Target 1의 이름이 지정되어 있고 Driver는 iscsi이며 따로 볼륨을 지정하지 않고 이름만 지정을 했는데 LUN 0이 있음을 확인할 수 있다. 이는 controller에서 사용하는 것이기 때문에 있는 것이고 Size는 0MB라고는 나와있지만 옆에 Block size: 1이므로 없는 것이 아니다. 그래서, LUN을 지정할 때 1번부터 지정하는 것이다.

LUN이란 Logical Unit Number라는 뜻으로 해석하면 논리 단위 번호라는 의미이다. 현 상황에 빗대어 말하면, 호스트의 SCSI LUN 번호로 제공되는 주소 지정 방식을 통해 외부에 노출되는데 대상 ID(여기서는 SCSI ID)와 결합되어 포트의 디스크를 고유하게 식별하는 번호이다. 



현재 iSCSI 서버(target)의 디스크 현황은 이러하다. 원래 있던 40G의 디스크에다가 iSCSI target 디스크를 지정해주기 위해 5G, 7G (가상)디스크를 추가했다.<br>(VM에서 (가상)디스크를 추가하는 방법이 궁금하시면 'LVM 실습(1) - 파티선 설정' 편을 참고해주세요!)

sudo fdisk -l

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/64.JPG?raw=true" width="px">

우리는 빨간색으로 표시된 5G 하드디스크(/dev/sdb)를 iSCSI target으로 지정해서 iSCSI initiator가 이를 로컬디스크처럼 사용할 수 있도록 할 것이다. 아까 봤던 구성도에서 SSD4에 해당한다.

이제 iSCSI target이 될 디스크를 등록해주자. 

sudo tgtadm --lld iscsi --op new --mode logicalunit --tid 1 --lun 1 -b /dev/sdb

이를 해석하면 Target 1에 5G 하드디스크(/dev/sdb)를 LUN 1번으로 등록할 것이라는 의미이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/65.JPG?raw=true" width="750px">

잘 등록됐는지 확인해보면 /dev/sdb가 있고 크기도 5G로 되어있다. 그 밑 부분은 이 Target 1으로 접근할 때 허용하는 계정 부분과 IP 대역 부분인데 아직 바인딩(포트 오픈)을 안 해줬기 때문에 빈 칸인 것이다. 먼저 계정부터 설정해보자.

sudo tgtadm --lld iscsi --op new --mode account --user pooh --password pooh1234

user는 pooh 비번은 pooh1234인 계정을 설정해주었다. 

sudo tgtadm --lld iscsi --op bind --mode account --tid 1 --user pooh

해당 설정을 Target 1에 적용한다는 의미이다. 따라서, iSCSI initiator가 Target 1에 접근할 때 해당 계정이 있어야만 접속할 수 있다. 모든 IP 대역을 허용한다고 할 때 계정을 설정해주지 않으면 누구나 다 접속할 수 있기 때문에 계정을 설정해주는 것이 좋다.  

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/66.JPG?raw=true" width="750px">

설정이 잘 되었는지 확인해보면

sudo tgtadm --lld iscsi --op show --mode target 또는 sudo tgt-admin -s

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/67.JPG?raw=true" width="px">

아까와는 다르게 맨 밑을 보면 Account information 부분에 pooh라고 되어있음을 확인할 수 있다. 그 다음은 해당  IP대역을 설정해주자. 필자는 특정 IP만 접근 가능하도록 설정할 것이다.

sudo tgtadm --lld iscsi --op bind --mode target --tid 1 -I 192.168.0.9

이는  iSCSI initiator가 Target 1에 접근할 때 해당 IP(192.168.0.9)만 접속할 수 있다는 것을 의미하고 해당 설정을 Target 1에 적용한다는 뜻이다. 모든 IP가 접속 가능하게 하려면 해당 IP부분에 ALL이라고 적어주면 된다.

(참고 : 설정해준 IP를 제거하려면 sudo tgtadm --lld iscsi --op unbind --mode target --tid 1 -I 192.168.0.9 )

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/68.JPG?raw=true" width="750px">

다른 부분은 다 똑같으니 패스하고 맨 밑에 ACL information 부분에 해당 IP가 추가됐음을 확인할 수 있다.

잘 bind되었는지 확인해보면

sudo netstat -nap | grep tgtd

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/69.JPG?raw=true" width="750px">

이제 마지막으로 여태까지 설정한 tgt 정보를 설정파일로 등록해준다.

tgt-admin --dump > /etc/tgt/targets.conf

이 명령어는 pooh계정에서 sudo로 해도 permission denied 걸려서 그냥 root계정으로 수행했다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/71.JPG?raw=true" width="750px">

이렇게 해당 설정들이 들어가게 되는데 암호부분은 직접 입력해줘야 한다. 아까 pooh라는 user에 비번을 pooh1234로 해줬으므로 그대로 pooh1234로 바꿔주자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/72.JPG?raw=true" width="px">

이제 iSCSI target 설정이 끝났다. 다음 포스팅에서는 iSCSI initiator 설정을 해보자.
<br><br><br>

참고 블로그 : https://m.blog.naver.com/PostView.nhn?blogId=jesstter&logNo=220767176214&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F

​			https://www.joinc.co.kr/w/Site/System_management/iSCSI

​			http://kindno.tistory.com/entry/%EB%94%94%EC%8A%A4%ED%81%AC-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80%EC%97%90%EC%84%9C-%EC%93%B0%EC%9D%B4%EB%8A%94-%EC%9A%A9%EC%96%B4%EC%9D%B8-LUN%EC%9D%B4%EB%9E%80

​			http://thinkfarm.tistory.com/entry/ISCSI-%EC%84%A4%EC%A0%95

​			

