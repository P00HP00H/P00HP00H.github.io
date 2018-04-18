---
layout: post
category: "django"
title: "Django에서 'port already in use' 에러가 뜰 때"
tags: ["django"]
---
저번 포스팅에서 python manage.py runserver을 할 때 아무런 탈 없이 잘 실행됐지만 저 상태에서(즉, hello 프로젝트가 있는 상태에서) 다른 프로젝트를 만들어 실행해보니 저렇게 'port already in use' 라는 에러가 발생했다. 

참고 : 환경은 Ubuntu 16.04 이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/17.JPG?raw=true" width="750px">

아마 hello 프로젝트에서 이미 해당 포트를 사용하고 있어서 그런 것 같다. 이럴 때마다 포트를 바꿔서 서버를 실행해줄 수도 있지만 더 간단하게 기존 서버를 죽이는 방법을 알아보자. 정말 간단하다. <br> 
터미널 창에 ps aux \| grep -i manage 라고 입력하면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/18.JPG?raw=true" width="750px">

이렇게 프로세스들을 볼 수 있다. 우리는 python manage.py runserver에서 에러가 났기 때문에 이 부분을 죽여야 한다. 2번째를 보면 62942라고 써져있는데 이는 process_id 즉, pid이다. 다음과 같이 kill 명령어를 써주면 된다.

kill -9 62942

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/19.JPG?raw=true" width="750px">

-9 옵션은 강제 종료를 뜻한다. 그러고 나서 다시 python manage.py runserver를 입력했는데 또  'port already in use' 에러가 떴다. 
pid = 62942인 프로세스가 안 죽었나 ps aux \| grep -i manage 로 다시 확인해봤는데

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/20.JPG?raw=true" width="750px">

62942가 없다. 그런데, 보면 /usr/bin/python manage.py runserver가 있었다. 이거도 영향을 미치나 해서 똑같은 방법으로 죽였다.

kill -9 62946

그러고 나서 python manage.py runserver를 해봤더니

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/21.JPG?raw=true" width="750px">

잘 동작한다.
