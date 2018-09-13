---
layout: post
category: "chat-bot"
title:  "[Django 챗봇] 2-1. 버튼 만들기(1)"
tags: [chat-bot]
---

PHP 챗봇에서도 다루었지만 카카오톡 플랫폼을 이용하기 위해서는 카카오톡 플러스 친구에서 앱을 등록해야 한다. 이 역시 '[PHP 챗봇] 2. 플러스 친구 가입하기' 포스팅을 참고하기 바란다. 가입을 했다고 가정하고 API 부분부터 시작하겠다. 카카오톡 플러스 친구에서 지원해주는 여러 API들이 있지만 이번 챗봇도 입력값을 넣었을 때 응답만 해주면 되기 때문에 keyboard와 message기능만 구현해주면 된다. 따라서, keyboard API와 메시지 수신 및 자동응답(message) API에 대해서만 다룰 것이다.<br>카카오톡 서버에서 keyboard API는 http://서버주소/keyboard, 메시지 수신 및 자동응답 API는 http://서버주소/message 에 요청하게 되는데 이에 맞춰 개발자가 해당 응답을 return하도록 Django로 구현해주면 된다.



카카오톡 플러스 친구는 http://서버주소/keyboard 에서 아래와 같이 받는다.

{

​        "type" : "buttons",

​        "buttons" : ["선택 1", "선택 2", "선택 3"]

} 

따라서, 우리는 위와 같이 동작하도록 pooh_app의 views.py 파일에 해당 내용을 추가해주면 된다.

그 전에 카카오톡은 REST API이기 때문에 Json 방식으로 통신을 해서 views.py에 해당 모듈을 import해줘야 한다.

vim ~/pooh_app/views.py

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/8.JPG?raw=true" width="px">

이제는 버튼을 추가해주자. 필자 학교의 경우 각 식당과 도움말 그리고 내일 메뉴도 보여주기 위해 '내일 메뉴' 버튼까지 포함시켰다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/9.JPG?raw=true" width="px">

이런 식으로 작성해주면 된다.

이제 http://서버주소/keyboard 에 접속할 때 방금 작성한 내용들을 보내야 하므로 url을 설정해줘야 한다. pooh_chatbot의 urls.py를 열어보자.

vim ~/pooh_chatbot/urls.py

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/10.JPG?raw=true" width="px">

아래 표시된 부분들을 추가해주면 된다. views.py의 keyboard 함수를 이용할 것이기 때문에 pooh_app의 views.py를 import 해줘야 한다(from pooh_app import views).

해당 내용들이 잘 전달되는지 확인하기 위해 실행을 해보자. manage.py가 있는 경로로 가서

$ sudo python manage.py runserver

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/11.JPG?raw=true" width="750px">

빨간색 글자들을 보면 migrate를 하지 않았다고 뜨는데 우리는 지금 keyboard가 잘 되는지만 확인하려는 것이기 때문에 신경쓰지 않아도 된다. 127.0.0.1:8000/keyboard에 접속해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/12.PNG?raw=true" width="750px">

이런 식으로 나오면 성공이다. 왜 한글이 나오지 않고 이런 글자가 나오냐면 한글이 유니코드로 저장이 되기 때문이다. 대신 JSON 부분을 클릭하면 한글이 잘 나온다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/13.PNG?raw=true" width="750px">

물론 카카오톡에서는 정상적으로 한글이 나온다.

python manage.py runserver를 하게 되면 디폴트로 8000번 포트가 지정이 된다. 또한 이렇게만 입력하면 로컬에서만 접속이 가능하고 외부에서는 접속을 할 수 없다. 이에 관한 자세한 설명은 '[Django] 서버 외부 접속 허용 및 포트 번호 변경' 포스팅을 참고하길 바란다.

<br><br><br>참고 사이트 : http://codeac.tistory.com/11?category=731564

,

