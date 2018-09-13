---
layout: post
category: "django"
title:  "Django 서버 외부접속 허용 및 포트번호 변경"
tags: [django]
---

Django를 이용하여 개발용 서버를 띄울 때

$ sudo python manage.py runserver

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/14.JPG?raw=true" width="px">

이렇게 사용하게 되면 포트 번호는 기본적으로 8000번으로 지정된다.

하지만 이건 디폴트값이고 내가 마음대로 지정해 줄 수도 있다. 만약 다른 포트 번호를 사용하고 싶다면

$ sudo python manage.py runserver 8888

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/15.JPG?raw=true" width="px">

이런 식으로 사용하면 포트 번호를 변경할 수 있다. 잘 작동되는지 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/16.JPG?raw=true" width="750px">

잘 작동한다. 하지만 이는 포트 번호만 변경해 줄 뿐 외부 접속을 허용하는 것은 아니다. 외부 접속을 허용하려면

$ sudo python manage.py runserver 0.0.0.0:8000

이렇게 해주면 된다. 포트 번호 또한 마음대로 지정해 줄 수 있다. 이렇게 해줘야 외부에서 Django 서버를 띄운 컴퓨터에 할당된 IP와 해당 포트번호를 사용하여 접속할 수 있다. 하지만 공유기를 연결하여 사용하는 경우는 공유기에서 외부 접속을 막고 있기 때문에 포트 포워딩이 필요하다. 내 서버를 기준으로 포트포워딩을 통해 외부에서 접속되도록 설정해보겠다.

필자는 따로 서버가 없어서 VM을 서버로 두었다. VM에서 8000포트로 Django 서버를 띄우면 필자가 살고 있는 건물의 특정 포트를 포트포워딩을 통해 VM의 8000 포트로 연결시켜준다. 이렇게 하면 외부에서는 해당 건물의 IP와 포트로 접속했을 때 VM의 8000포트로 들어가 Django 서버를 이용할 수 있게 된다. 포트포워딩을 해주기 위해 브라우저를 켠 후 URL 창에 192.168.0.1을 입력해주자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/17.JPG?raw=true" width="px">

로그인을 한 다음

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/18.JPG?raw=true" width="px">

관리도구를 클릭한 후 고급 설정 -> NAT/라우터 관리 -> 포트포워드 설정을 들어가서

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/19.JPG?raw=true" width="750px">

외부포트는 지정해주고 싶은 포트 번호를 입력해주면 되고 내부포트는 VM이 Django 서버를 띄워주는 포트인 8000으로 해준다. 하지만 이 역시 무조건 8000은 아니다. 예를 들어, VM에서 Django 서버를 8888 포트로 띄웠으면 내부 포트도 8888이 되는 것이다. 필자는 Django 서버를 8000 포트로 띄웠기 때문에 8000을 써준 것이다. 내부 IP 주소는 VM 서버의 IP를 입력해주면 된다. 이렇게 하면 '필자가 살고 있는 건물의 IP:8000' 으로 접속해서 Django 서버를 이용할 수 있다(이에 대한 예시는 '[Django 챗봇] 2.2 버튼 만들기(2)' 포스팅을 참고해주세요!!).

<br><br><br>참고 사이트 : http://compunication.tistory.com/4

,

