---
layout: post
category: "linux"
title: "iSCSI initiator 설정"
tags: ["linux"]
---

저번 포스팅에서 iSCSI Target을 설치했는데, 이번 포스팅에서는 iSCSI initiator를 설치해볼 것이다. 저번 포스팅에서도 언급했지만 iSCSI initiator 환경은 VM의 CentOS 7이다. 

이따가 iSCSI Target의 디스크가 연결됐을 때와 비교하기 위해서 현재 iSCSI initiator의 디스크 현황을 보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/84.JPG?raw=true" width="px">

원래 있던 디스크에서 추후에 LVM으로 묶기 위해 4G, 6G (가상)디스크를 추가해 준 상태이다.



먼저 update를 해준다. 

yum update -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/74.JPG?raw=true" width="px">

update를 마쳤으면 이제 iSCSI 클라이언트, 즉, initiator을 설치한다.

yum install iscsi-initiator-utils -y

(참고 : Ubuntu는 apt-get install open-iscsi)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/75.JPG?raw=true" width="750px">

다 설치했으면 저번 포스팅에서 iSCSI Target 장비에 인증 기능을 활성화했기 때문에(Target에다가 계정 및 IP대역 설정) 해당 Target에 접근하기 위해서는 iSCSI initiator에 설정해 준 계정을 등록해야 한다.

vim /etc/iscsi/iscsid.conf

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/76.JPG?raw=true" width="px">

빨간색으로 된 부분을 추가해주면 되는데 저번 포스팅에서 Target에다가 username은 pooh, 비밀번호는 pooh1234로 설정했기 때문에 저렇게 입력한 것이다. node는 실제 iSCSI Target에 username과 비밀번호를 이용하여 인증하기 위한 설정이고 discovery 부분은 discovery 단계에서의 인증 정보를 의미한다.

다 설정해줬으면 이제 iSCSI Target 시스템을 검색해보자. Target에 접근하려면 당연히 Target의 IP를 알고 있어야 한다. 만약 이를 잊어버리거나 모른다고 할 때 Target에서 해당 initiator의 IP가 접근할 수 있도록 허용해줬다면 다음 명령어로 Target의 IP를 알아낼 수 있다.

iscsiadm -m node

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/81.JPG?raw=true" width="px">

이렇게 iSCSI Target의 IP와 함께 저번 포스팅에서 지정해준 Target의 이름이 나온다. 만약 IP를 알고 있다면 이렇게도 명령어를 사용할 수 있다.

iscsiadm -m discovery --type sendtargets --portal 192.168.0.92

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/82.JPG?raw=true" width="px">

둘 다 결과값이 똑같다. 둘 중 편한 명령어를 사용하면 된다.

그럼 만약 Target이 접근을 허용하지 않은 IP가 저렇게 명령어를 입력하면 어떻게 될까?? 다음 그림처럼 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/83.JPG?raw=true" width="px">

이런 식으로 나오게 된다. 아예 없다고 나온다.



이제 iSCSI Target 시스템에 로그인을 할 차례이다. Target의 IP와 이름을 알았으니 이렇게 써주면 된다.

iscsiadm -mode node --targetname iqn-2018-07.pooh:storage.lun1 --portal 192.168.0.92 --login

(참고 : --mode 대신 --m, -m을 써도 되고 로그아웃을 하려면 맨 뒤에만 --login 대신 --logout으로 바꿔주면 된다.)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/79.JPG?raw=true" width="750px">

밑에 successful이 보인다. 로그인에 성공한 것이다.

만약에 Target이 허용하지 않은 IP가 똑같이 로그인을 하면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/80.JPG?raw=true" width="750px">

이렇게 Could not login이라고 뜬다.

이제 iSCSI를 통해서 iSCSI Target에 있는 5G 디스크를 사용할 수 있게 되었다. 이를 확인하기 위해 이전 디스크 현황과 비교해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/78.JPG?raw=true" width="px">

맨 밑에 5G 디스크가 추가되었음을 확인할 수 있는데 이게 바로 iSCSI Target에 있는 그 5G 디스크이다. 즉, iSCSI로 연결된 것이다.

다음 명령어로도 확인이 가능하다.

cat /proc/scsi/scsi

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/85.JPG?raw=true" width="px">

빨간 부분을 보게되면 Lun 1번의 VIRTUAL-DISK 즉, iSCSI로 연결된 디스크를 의미한다. 

이제 이 iSCSI로 연결된 디스크를 사용하려면 똑같이 fdisk로 파티션 지정해주고 mkfs로 파일시스템을 생성한 다음 마운트 시켜주고 fstab으로 컴퓨터가 부팅될 때마다 마운트 된 파일시스템이 적용될 수 있도록 설정을 해주면 된다. 하지만 최종 목표는 이를 initiator의 디스크들과 함께 LVM으로 묶어서 사용하는 것이기 때문에 지금 이 과정들을 하지는 않겠다. 다음 포스팅에서는 initiator의 디스크들과 iSCSI로 연결된 디스크를 LVM으로 묶어서 파일시스템으로 만들어 마운트까지 시켜볼 것이다. 

 

참고 블로그 : 

- https://m.blog.naver.com/PostView.nhn?blogId=jesstter&logNo=220767176214&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F

​			

