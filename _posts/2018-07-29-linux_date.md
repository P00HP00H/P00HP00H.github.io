---
layout: post
category: "others"
title: "리눅스 시간 설정하기"
tags: ["others"]
---

서버를 구축하면서 반드시 해야 할 작업 중의 하나가 시간을 제대로 맞추는 것이다. 도커 컨테이너를 띄울 때도 서버 시간과 컨테이너의 시간이 다른 경우가 있는데 상황에 따라 시간을 맞춰줘야 하는 경우가 생긴다. 해당 지역의 로컬 시간을 설정하기 위해서는 /etc/localtime을 원하는 지역의 것으로 바꾸어 주면 된다. 실습환경은 VM의 Ubuntu 16.04와 CentOS 7 두 곳에서 진행할 것이다.



- <h2>Ubuntu 16.04</h2>

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/11.JPG?raw=true" width="730px">

  오른쪽을 보면 현재시간은 2018년 07월 22일 08:03이다. 하지만 date를 입력해보면 시간이 다르다. 저번 포스팅에서 봤던 UTC(협정 세계시)로 되어있기 때문이다. 해당 시간은 /etc/localtime에 저장되어 있기 때문에 ls -al 명령어로 확인해보면

  ls -al /etc/localtime

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/12.JPG?raw=true" width="730px">

  심볼릭 링크로 UTC파일 즉, /usr/share/zoneinfo/UTC에 연결되어 있다. /usr/share/zoneinfo/로 가면 해당 지역들이 나온다. 거기서 지정하고 싶은 나라와 지역을 설정해주면 된다. 

  ls -al /usr/share/zoneinfo

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/13.JPG?raw=true" width="px">

  이런 식으로 쭉 나오는데 우리는 대한민국에 살기 때문에 Asia에 해당하므로 Asia를 택해준다. Asia가 directory로 되어 있으므로 해당 디렉토리를 살펴보자.

  ls -al /usr/share/zoneinfo/Asia

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/14.JPG?raw=true" width="px">

  여기에 Seoul이 보인다. Seoul은 파일로 되어있으므로 여기를 /etc/localtime이랑 연결해주면 된다.

  sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/15.JPG?raw=true" width="730px">

  이러고 나서 ls -al /etc/localtime으로 확인해보면 Seoul파일에 잘 연결되어 있다. date로도 입력해보면 KST 즉, 한국 시간으로 바뀌어 있음을 확인할 수 있다.

  ​

  하지만 여기서 드는 의문점이 하나 있었다. Seoul 파일을 보면 사실 심볼릭링크로 무언가에 연결되어 있는 것을 볼 수 있다. 보면 --> ../ROK라고 되어 있는 것으로 보아 Seoul 파일이 있는 위치의 상위 경로에 있는 ROK파일에 연결되어 있는 것을 알 수 있다. 그 말은 /usr/share/zoneinfo/Asia/Seoul이 아닌 /usr/share/zoneinfo/ROK로 해도 똑같다는 말 같아서 시간을 UTC로 되돌린 후 실험을 해봤다.

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/16.JPG?raw=true" width="730px">

  UTC 시간으로 맞춘 다음 /etc/localtime을 /usr/share/zoneinfo/ROK로 연결했더니 똑같이 한국시간 KST로 바뀌었다. 그럼 ROK도 Seoul처럼 어딘가에 심볼링 링크가 걸려있지않나 확인을 해봤더니

  ls -al /usr/share/zoneinfo

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/17.JPG?raw=true" width="px">

  따로 걸려있지 않다. 이 ROK 파일에 한국 시간이 저장되어 있는 것이다. 이걸 확인하다 보니 밑에 있는 UTC도 맨 밑의 Zulu와 심볼릭 링크로 연결되어 있는데 이 역시 /usr/share/zoneinfo/UTC 대신 /usr/share/zoneinfo/Zulu로 해도 똑같이 UTC로 시간이 설정된다. 

  ​

- <h2>CentOS 7</h2>

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/18.JPG?raw=true" width="730px">

  여기도 마찬가지로 오른쪽을 보면 현재시간은 2018년 07월 29일 16:53이다. 하지만 date를 입력해보면 시간이 다르다. UTC(협정 세계시)로 되어있기 때문이다. CentOS 7 역시 해당 시간은 /etc/localtime에 저장되어 있기 때문에 ls -al 명령어로 확인해보면

  ls -al /etc/localtime

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/19.JPG?raw=true" width="730px">

  심볼릭 링크로 UTC파일 즉, /usr/share/zoneinfo/UTC에 연결되어 있다. CentOS도 /usr/share/zoneinfo/로 가면 해당 지역들이 나온다. 똑같이 지정하고 싶은 나라와 지역을 설정해주면 된다. 

  ls -al /usr/share/zoneinfo

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/20.JPG?raw=true" width="730px">

  우리는 대한민국에 살기 때문에 Asia에 해당하므로 Asia를 택해준다. 여기도 Asia가 directory로 되어 있으므로 해당 디렉토리를 살펴보자.

  ls -al /usr/share/zoneinfo/Asia

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/21.JPG?raw=true" width="730px">

  여기에 Seoul이 보인다. Seoul은 파일로 되어있으므로 여기를 /etc/localtime이랑 연결해주면 된다.

  ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/22.JPG?raw=true" width="730px">

  이러고 나서 ls -al /etc/localtime으로 확인해보면 Seoul파일에 잘 연결되어 있다. date로도 입력해보면 KST 즉, 한국 시간으로 바뀌어 있음을 확인할 수 있다.

  ​

  하지만 Ubuntu와 약간의 차이가 있다. 아까는 Seoul 파일이 ../ROK 파일과 심볼릭 링크로 연결이 되어 있었는데 CentOS 7은 그렇지가 않다. 근데 Ubuntu처럼 /usr/share/zoneinfo에 ROK파일이 존재하긴 한다. ROK가 Republic Of Korea 같아서 ROK를 /etc/localtime에 연결해도 한국 시간이 될 것 같긴 했지만 혹시나 해서 한 번 진행해봤는데

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/23.JPG?raw=true" width="730px">

  역시나 한국시간 KST로 잘 설정됨을 확인할 수 있다. 

  ​


**결론은 Ubuntu와 CentOS의 시간 설정해주는 방법은 아예 똑같다고 보면 된다.**
<br><br><br><br>
검색해보다가 dpkg-reconfigure라는 툴을 이용해서 시간을 설정하는 방법이 있었다. 이는 약간 번외느낌이므로 귀찮으신 분들은 스킵하면 될 것 같다. 이 방법은 Debian/Ubuntu 계열에만 해당되며 도커 컨테이너 이미지들이 대부분 기본으로 Debian이 깔려있기 때문에 도커 컨테이너에다가 직접 설정을 해볼 예정이다. 도커를 잘 모르시는 분들은 그냥 Ubuntu나 Debian에 설치한다고 생각하면 될 것 같다. 

환경은 VM의 CentOS 7이고 여기에 mysql 컨테이너가 있다. mysql 컨테이너의 경우는 Debian으로 되어 있기 때문에 위 내용들을 실행할 수 있다. 



- <h3>dpkg-reconfigure 툴을 이용한 설치</h3>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/81.JPG?raw=true" width="px">

도커를 모르시는 분들은 root@localhost pooh는 CentOS 7(현재시간이 맞춰져 있는 상태), root@b319cd6ad559:/ 를 Debian이라고 생각하면 된다. Debian에 mysql이 깔려있는 상태인 것이다. Debian의 시간이 따로 설정이 되어있지 않아 UTC로 되어 있다. 그리고 밑에 보면 Debian임을 확인할 수 있다. Debian에 dpkg-reconfigure 툴을 설치해서 시간을 맞춰보자.

먼저 업데이트를 해준다.

apt-get update -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/82.JPG?raw=true" width="730px">

업데이트를 마쳤으면 이제 dpkg-reconfigure 툴을 이용한다.

dpkg-reconfigure tzdata

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/83.JPG?raw=true" width="730px">

위 명령어를 입력하면 이렇게 화면이 뜨게 된다. 우리는 Asia / Seoul이므로 Asia를 선택한다. 6번에 Asia가 있으므로 6 누르고 Enter키를 누르면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/84.JPG?raw=true" width="730px">

이런 화면이 나온다. 여기서 Seoul을 선택해주면 된다. 68번에 Seoul이 있으므로 68 누르고 Enter키를 누르면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/85.JPG?raw=true" width="px">

이제 설정이 끝났다. time zone이 Asia/Seoul로 설정되었고 Local time이 KST 즉, 한국시간으로 설정되었다. date로 확인해봐도 KST로 잘 나온다. CentOS 7의 시간과 비교해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/86.JPG?raw=true" width="px">

컨테이너 밖으로 빠져나올 때의 시간으로 인해 3초의 차이가 생긴 것이고 결국 현재 시간과 잘 맞춰졌음을 확인할 수 있다(도커를 모르시는 분들은 Debian의 시간확인을 하고 CentOS 7로 넘어갈 때 3초의 시간이 걸려 3초가 차이 난 것이지 결국 똑같아졌다고 이해하면 된다(위의 시간은 Debian, 밑의 시간은 CentOS 7)). <br><br><br>

참고 블로그 : 

- https://m.blog.naver.com/PostView.nhn?blogId=ksmsumu&logNo=220039780937&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F
- http://gauryan.tistory.com/128


