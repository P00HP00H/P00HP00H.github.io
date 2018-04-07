---
layout: post
category: "django"
title: "Django로 hello world 띄우기[1]"
tags: ["django"]
---
<br>

- <h3>Django란?</h3>

Django는 파이썬으로 만들어진 무료 오픈소스 웹 애플리케이션 프레임워크(web application framework)로, 쉽고 빠르게 웹사이트를 개발할 수 있도록 해준다.<br><br><br>
Django를 사용하기 위해서는 먼저 Django를 설치해줘야 하는데 필자는 Ubuntu 16.04 LTS에서 진행하였다. 설치하기 전에 pip가 최신 버전으로 되도록 업그레이드를 시켜준다.

$ sudo pip install --upgrade pip
<br>
pip를 최신 버전으로 해줬으면 이젠 Django를 설치해보자
<br><br>
$ sudo pip install Django
<br>
설치가 끝났으면 먼저 프로젝트를 생성해줘야 한다.
<br><br>
$ django-admin startproject hello
<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/1.JPG?raw=true" width="px">

ls를 통해 보면 hello라는 이름의 프로젝트가 생성되었음을 확인할 수 있다. 필자는 ~경로에 했기 때문에 저런 것이지만, 따로 새 디렉토리를 만든 후 해당 디렉토리에서 프로젝트를 생성해줄 수도 있다. 어떤 것들이 만들어졌는지 확인해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/2.JPG?raw=true" width="px">

이를 한 눈에 보기 쉽게 나타내보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/3.JPG?raw=true" width="px">

- `hello/` 디렉토리 바깥의 디렉토리는 단순히 프로젝트를 담는 공간입니다. 이 이름은 원하는대로 지정해줄 수 있다.

- `manage.py`: Django 프로젝트와 다양한 방법으로 상호작용 하는 커맨드라인의 유틸리티로 이에 대한 자세한 정보는 https://docs.djangoproject.com/ko/2.0/ref/django-admin/ 에서 확인할 수 있다.

- `hello/` 디렉토리 내부에는 프로젝트를 위한 실제 Python 패키지들이 저장되고, 이 디렉토리 내의 이름을 이용하여, (`mysite.urls` 와 같은 식으로) 프로젝트 어디서나 Python 패키지들을 import 할 수 있다.

- `hello/__init__.py`: Python 으로 하여금 이 디렉토리를 패키지처럼 다루라고 알려주는 용도의 단순한 빈 파일이다. 자세한 내용은 https://docs.python.org/3/tutorial/modules.html#tut-packages 를 참고하기 바란다.

- `hello/settings.py`:  Django project의 환경/구성을 저장하는 곳으로 https://docs.djangoproject.com/ko/2.0/topics/settings/ 에서 환경 설정이 어떻게 동작하는지 확인할 수 있다.

- `hello/urls.py`: Django project 의 URL 선언을 저장한다. https://docs.djangoproject.com/ko/2.0/topics/http/urls/ 에 URL에 대한 자세한 내용들이 나와있다.

- `hello/wsgi.py`: 현재 프로젝트를 서비스 하기 위한 WSGI 호환 웹 서버의 진입점이라고 생각하면 된다. https://docs.djangoproject.com/ko/2.0/howto/deployment/wsgi/ 에 자세하게 나와있다.

  ​

  이에 대한 내용은 django사이트(https://docs.djangoproject.com/ko/2.0/intro/tutorial01/)에서 퍼왔습니다.

  ​

 프로젝트를 생성했으니 해당 프로젝트에 대한 서버가 잘 동작하는지 확인해보자.

manage.py가 있는 경로로 가서

$ python manage.py runserver

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/4.JPG?raw=true" width="px">

보면 http://127.0.0.1:8000/ 에서 실행이 됨을 확인할 수 있다. 실제로 들어가보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/5.JPG?raw=true" width="px">

잘 실행되고 있음을 확인할 수 있다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/6.JPG?raw=true" width="px">

우리는 위와 같이 만들어 줄 것이다. 프로젝트를 만들어줬기 때문에 manage.py가 있는 경로로 가서 앱을 만들어주자.

$ python manage.py startapp pooh

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/7.JPG?raw=true" width="px">

pooh라는 디렉토리가 생겼다. 한 번 들어가보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/8.JPG?raw=true" width="px">

앱이 잘 생성되었음을 확인할 수 있다. 앱을 추가했을 때의 전체구조를 다시 보게 되면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/hello1/9.JPG?raw=true" width="px">

다음 포스팅에는 환경설정 등을 통해 hello world를 띄워보자. 
