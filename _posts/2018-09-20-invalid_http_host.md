---
layout: post
category: "django"
title:  "Invalid HTTP_HOST header: 'IP'. You may need to add u'IP' to ALLOWED_HOSTS."
tags: [django]
---

$ sudo python manage.py runserver 0.0.0.0:8000

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/41.jpg?raw=true" width="750px">

로컬에서만 접근이 가능하도록 Django 서버를 실행할 때는 문제가 없었으나 이를 외부에서 접속 가능하게끔 했더니 Invalid HTTP_HOST_ header: 'IP'. You may need to add u'IP' to ALLOWED_HOSTS라는 에러가 발생했다. 이를 해석해보면 필자의 IP에 대한 HTTP_HOST_header가 이용가능하지 못하므로 ALLOWED_HOST에 해당 IP를 추가할 필요가 있다는 뜻이다. ALLOWED_HOST는 장고 프로젝트의 settings.py에 있으므로 settings.py에서 설정을 해주면 된다.

vim ~/pooh_chatbot/settings.py

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/38.JPG?raw=true" width="px">

필자의 경우 ~에 프로젝트를 생성해서 경로가 이렇게 된다. settings.py를 열어보면 ALLOWED_HOSTS 부분이 있는데 2가지 방법 중 하나를 택해서 바꿔주면 된다. 

ALLOWED_HOSTS = ['*'] 또는 ALLOWED_HOSTS = ['해당 IP', 'localhost', '127.0.0.1']

이렇게 해주면 된다. 필자는 주로 전자를 많이 쓰지만 이번엔 후자의 방법으로 해보겠다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/39.JPG?raw=true" width="px">

저장을 하고 다시 Django 서버를 띄워보자.

$ sudo python manage.py runserver 0.0.0.0:8000

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/40.JPG?raw=true" width="px">

이제 해당 에러가 나지 않고 잘 동작한다.

<br><br><br>참고 사이트 : 

- https://stackoverflow.com/questions/40582423/invalid-http-host-header

,

