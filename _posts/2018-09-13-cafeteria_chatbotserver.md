---
layout: post
category: "chat-bot"
title:  "[Django 챗봇] 1. Django 설치 및 프로젝트 생성"
tags: [chat-bot]
---

저번에 간단한 버스 챗봇을 만들어봤다면 이번엔 학식 챗봇을 만들어 보려고 한다. 저번에 만든 버스 챗봇은 사실 너무나 미숙한 것들도 많았고 글자를 완전히 챗봇의 안내에 맞게 입력해야 하는 불편함도 있었다. 그래서 이번에는 모두 버튼으로 만들어봐야겠다는 생각을 했다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/7.JPG?raw=true" width="750px">

챗봇의 구조를 보게 되면 서버가 필요함을 알 수 있다. 필자는 따로 서버를 가지고 있지 않아서 VM을 서버로 두었다. 이 부분은 PHP 챗봇에 자세하게 적어두었기 때문에 모르겠으면 '[PHP 챗봇] 1. VM서버 셋팅' 편을 참고하길 바란다. 지금은 이러한 서버 환경을 셋팅하는 방법에 대해서는 다루지 않겠다. 환경은 PHP 챗봇일때와 마찬가지로 Ubuntu 16.04 LTS이며 셋팅이 다 되어 있는 상태라고 하고 진행할 것이다.
<br><br><br>
- <h3>Django 설치 및 프로젝트 생성</h3>

Django를 사용하기 위해서는 Django를 설치해줘야 하는데 설치하기 전에 먼저 pip가 최신 버전으로 되도록 업그레이드를 시켜준다.

$ sudo pip install –-upgrade pip<br><br>
pip를 최신 버전으로 해줬으면 이젠 Django를 설치해보자.
<br><br><br>
$ sudo pip install Django<br><br>
장고를 설치한다.
<br><br><br>
$ django-admin startproject pooh_chatbot .

pooh_chatbot이라는 이름의 프로젝트를 생성한다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/1.JPG?raw=true" width="750px">

해당 경로에 pooh_chatbot이 생성되었음을 확인할 수 있다.



$ python manage.py startapp pooh_app

프로젝트를 생성해줬으면 manage.py가 있는 경로로 가서 어플리케이션을 생성한다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/2.JPG?raw=true" width="750px">

pooh_app이 생성된 것을 볼 수 있다. 이제 pooh_chatbot의 settings.py 파일을 수정해야 한다. 왜냐하면 프로젝트에 pooh_app을 만들었다는 것을 알려줘야 하고 시간대도 한국으로 맞춰줘야 하기 때문이다.



$ vim ~/pooh_chatbot/settings.py

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/3.JPG?raw=true" width="px">

이렇게 INSTALLED_APPS에서 pooh_app을 추가해준다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/4.JPG?raw=true" width="px">

언어와 시간대를 한국으로 맞추기 위해 LANGUAGE_CODE와 TIME_ZONE을 수정해준다.

생성된 프로젝트의 구조를 보면 다음과 같다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/5.JPG?raw=true" width="px">

다음 포스팅에서는 카카오톡 API 테스트를 진행해보자.

<br><br><br>참고 사이트 : http://codeac.tistory.com/11?category=731564

,

