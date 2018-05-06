---
layout: post
category: "django"
title: "Django로 hello world 띄우기[2]"
tags: ["django"]
---

이번 포스팅에서는 hello world를 직접 띄워볼 것이다. 장고 프로젝트는 여러 앱들로 구성이 되고(물론 1개의 앱으로도 구성 가능) 여기서 앱은 웹사이트를 기능별로 분리해놓은 단위라고 생각하면 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/10.JPG?raw=true" width="750px">

해당 프로젝트로 가보면 pooh앱과 프로젝트의 이름과 동일한 이름(hello)의 디렉토리가 하나 더 있음을 확인할 수 있는데, 이 hello 디렉토리는 기본적인 설정을 담고 있다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/6.JPG?raw=true" width="px">

이전 포스팅의 구성도를 보면 pooh앱의 hello 함수를 호출한다고 했으므로 pooh앱에 hello 함수 기능을 'hello world'를 띄워주는 것으로 만들어주면 된다. pooh앱의 views.py를 열어보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/11.JPG?raw=true" width="750px">

열면 맨 위 두 줄은 원래 있던 부분이고 빨간색 부분들은 추가해 준 부분이다. 추가된 부분을 보면 페이지 요청을 받았을 때 Hello world를 띄워주는 기능을 하는 것이다. 하지만 아까 구성도에서도 봤듯이 views.hello가 실행되기 위해서는 먼저 pooh앱이 실행되는 조건을 맞춰줘야 한다. 그 조건을 맞춰주기 위해 아까 기본적인 설정을 담고 있다고 했던 hello디렉토리의 urls.py를 열어보자(즉, 최종 경로는 ~/hello/hello/urls.py).

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/12.JPG?raw=true" width="750px">

urlpatterns는 서버로 요청이 들어오면누가 어떻게 처리할지 담당자를 지정하는 역할을 하고 urlpatterns의 [ ]의 url(a,b)에서 a는 주소, b는 a로 접속했을때 누가 처리해줄 것인지를 정해주는 역할을 한다. 즉, url(r'^admin/', admin.site.urls)를 해석해보면 /admin/으로 접속했을 때 admin이라는 녀석이 처리하라는 뜻이다. 일단 만들어져 있는 것이니 한 번 어떻게 처리되는지 확인해보자. manage.py가 있는 곳으로 가서

python manage.py runserver

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/13.JPG?raw=true" width="750px">

해당 서버로 들어가서 주소대로(127.0.0.1:8000/admin) 접속을 해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/14.JPG?raw=true" width="750px">

이렇게 장고 admin이 잘 실행됨을 알 수 있다. 즉, admin으로 지정해준 것처럼 특정 앱에서 처리하도록 지정해줄 수가 있다. 나중에 이런 식으로 admin에 대해서 그리고 다른 앱들에 대해서도 다뤄보자.<br>지금은 admin이나 다른 앱들 말고 그냥 주소(127.0.0.1:8000)만 입력했을 때 hello world가 나오도록 할 것이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/15.JPG?raw=true" width="750px">

아까와 같이 hello 디렉토리의 urls.py를 열어서(최종 경로는 ~/hello/hello/urls.py) urlpatterns에다가 추가해주면 된다. r'^' 즉, 뒤에 뭐가 따로 없기 때문에 그냥 주소만 입력한 경우를 의미하고 pooh앱이 자신의 views.py에서 hello 함수를 호출하는 것이므로 pooh을 담당자로 지정해준 것이다. 하지만 url 함수로 인해 pooh.views가 아닌 pooh.urls라고 지정해준 것이다.<br>그리고 pooh앱이 같은 디렉토리(hello)에 없기 때문에 include를 사용하였다. 단, include를 사용하도록 하기 위해서 import에 include를 포함시켜야 줘야한다.



종합해보면 127.0.0.1:8000로 접속할 때 pooh앱을 실행하도록 한 것이다. 즉, pooh앱이 어떠한 경우에 실행이 되는지를 지정해 줬다는 말이다. pooh앱이 실행되는 경우를 지정해줬으니 이제 어느 경우에 hello함수가 실행되는지를 지정해줘야 한다. 왜냐하면 우리는 최종적으로 views.py에 있는 hello함수가 실행되도록 해야 하기 때문이다. 이 hello함수가 실행되도록 하기 위해서 pooh앱에서(~/hello/pooh) urls.py를 하나 더 만들어주자.<br>(pooh앱에서 urls.py를 만들어줘야 하는 이유는 아까 urlpatterns에서 url 지정해줄 때 include(pooh.urls), 즉, urls.py에서 처리하라고 지정해줬는데 pooh앱에는 urls.py가 없기 때문이다)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/16.JPG?raw=true" width="750px">

이렇게 만들어주면 된다. url쪽에 ^$가 있는데 이게 바로 빈 경로 즉, 주소만 입력한 경우를 나타낸 것이다. 그리고 아까는 hello와 pooh가 경로가 달랐기 때문에 include를 이용했지만 이번엔 urls.py와 views.py가 pooh앱 경로에 같이 있기 때문에 include를 따로 쓰지 않고 views.index와 같이 바로 파일명과 함수를 사용해주면 된다.
대신 views.index를 사용하기 위해서 from . import views를 적어줘야 한다. 이를 해석해보면 .은 현재 디렉토리(~/hello/pooh)를 의미하고 현재 디렉토리에서 views라는 모듈을 가져다 쓰겠다라는 뜻이 된다.



이제 모든 설정이 끝났다. 이제 manage.py가 있는 디렉토리에서 서버를 실행해주고 확인해주면 된다.



python manage.py runserver

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/17.JPG?raw=true" width="750px">

해당 주소(127.0.0.1:8000)로 접속해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/18.JPG?raw=true" width="750px">

Hello world가 출력됨을 확인할 수 있다.

