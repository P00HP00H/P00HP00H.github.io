---
layout: post
category: "chat-bot"
title:  "[PHP 챗봇] VM서버 셋팅"
tags: [chat-bot]
---

![chatbot-arc](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/chatbot-arc.JPG?raw=true)

챗봇의 구조를 보게 되면 서버가 필요함을 알 수 있다. 필자는 따로 서버를 가지고 있지 않아서 VM을 서버로 두었다. 하지만, VM은 설정을 해줘야만 외부에서 접속이 되기 때문에 따로 설정을 해줘야 한다.  NAT방식이나 Bridge방식 둘 다 가능하지만, 필자는 Bridge 방식을 이용했다.   
<br><br>
<h3>1. Bridge 방식 설정</h3>

bridge 방식을 이용하기 위해 VM설정을 해주자. Edit -> Virtual Network Editor로 들어가보면

![vmsetting](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/vmsetting.jpg?raw=true)

![networkeditor](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/networkeditor.JPG?raw=true)

여기서 Change setting을 누른다.

![bridgesetting](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/bridgesetting.JPG?raw=true)

Bridge 부분을 누르고, OK버튼을 누른다. 이번엔 VM -> Settings를 들어간다.

![vmsetting2](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/vmsetting2.jpg?raw=true)

![networkadapter](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/networkadapter.JPG?raw=true)

왼쪽의 Network Adapter에서 Bridge를 선택해준다. VM의 IP를 확인해보면

![ip](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/ip.jpg?raw=true)

이러면 Bridge 설정은 끝났다. 

![Bridge](https://github.com/P00HP00H/P00HP00H.github.io/blob/4f88b77a75c27f99b7b478f210814a31c7f77347/img/Bridge.JPG?raw=true)

그럼 이제 VM에도 IP가 할당되어 서버처럼 쓸 수가 있는데, 사용자가 메시지를 보냈을 때 VM한테만 가도록 하기 위하여 포트포워딩을 해줘야 한다.

<br><br>

<h3>2. 포트포워딩 설정</h3>

필자는 iptime 공유기를 사용하기 때문에 iptime일때를 기준으로 설명하겠다.

URL창에 192.168.0.1을 입력하면

![iptime](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/iptime.jpg?raw=true)

이런 화면이 나오는데, 여기서 로그인 하고 관리도구를 눌러준다.

![iptime2](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/iptime2.jpg?raw=true)

여기서 다양한 정보들을 볼 수 있다. 먼저 고급설정 -> 네트워크 관리 -> 내부 네트워크 정보로 들어가보면

![iptime3](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/iptime3.jpg?raw=true)

지금 이 그림은 VM을 Bridge 방식으로 설정하기 전이다.



2. 포트포워딩 설정

이제 포트포워딩을 해주자. 왼쪽에서 고급 설정 -> NAT/라우터 관리 -> 포트포워드 설정으로 들어가보면

![Bridge](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/portforwarding.jpg?raw=true)

'새 규칙 추가'를 누른 후 아래쪽에 작성을 해줘야 하는데 내부 IP주소는 VM으로 포트포워딩을 해줄 것이기 때문에 VM의 주소를 입력해주면 되고 필자는 외부에서 터미널로 접속이 가능하게끔 외부 포트는 2222, 내부 포트는 22로 해주었다. 이렇게 되면 터미널에 접속할 때 공유기에서 2222번 포트로 들어가면 VM의 22번 포트로 접속이 되는 것이다.

![putty](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/putty.JPG?raw=true)

![puttysuccess](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/puttysuccess.jpg?raw=true)

외부 포트는 내가 지정해주고 싶은 포트번호를 입력해주면 되는데, 49152~65535번 포트는 동적 포트이기 때문에 서버 소켓 포트로 사용할 수 없고, 0~1023번은 well-known 포트라고 해서 UNIX/LINUX에서 root 권한으로면 포트를 열 수가 있어 보통은 1024~49151번 범위 내에서 외부포트 번호를 지정해준다. 다 지정해줬으면 확인 버튼을 누른다. 이러면 포트포워딩 설정이 끝난다.

다시 고급설정 -> 네트워크 관리 -> 내부 네트워크 정보로 들어가보면 pooh-virtual-machine이라는 이름으로 IP가 할당됐음을 알 수 있다. 아까 본 VM의 IP와 일치한다.

![iptime5](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/iptime5.jpg?raw=true)

이제 외부에서 필자의 공유기로 접속을 하게 되면 포트포워딩에 의해 VM으로 접속이 된다. 즉, VM 서버 셋팅이 완료된 것이다.



 

,

