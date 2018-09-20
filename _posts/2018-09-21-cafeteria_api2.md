---
layout: post
category: "chat-bot"
title:  "[Django 챗봇] 2-2. 버튼 만들기(2)"
tags: [chat-bot]
---

저번 포스팅에서 keyboard 함수를 통해 버튼을 만들어 로컬에서 띄우는 것까지 해봤다. 하지만 카카오톡으로 서비스를 하려면 외부에서 접근이 가능해야 한다. 그러면 외부에서 접속되게 해보자.<br>이전 포스팅에서도 언급했지만 필자는 VM을 서버로 두었다고 했다. 따라서 외부에서 필자가 사는 건물의 IP로 접속할 때 필자의 VM으로 접속되도록 포트포워딩을 시켜주면 된다. 필자의 VM은 bridge 방식으로 설정을 해주었고, VM에서 Django 서버를 띄운 다음, 건물의 IP의 특정 포트를 Django 서버를 띄워준 포트로 연결시켰다. 좀 더 자세히 말하면, 건물의 IP에 8000포트로 접속하면 VM의 8000포트로 접속하도록 포트포워딩을 시켜줬다(이에 대한 내용은 'Django 서버 외부접속 허용 및 포트번호 변경' 포스팅을 참고해주세요!! ).

이제 카카오톡 앱을 만들 차례이다. 만드는 방법은 '[PHP 챗봇] 플러스 친구 가입하기'에 자세하게 나와있다. 여기서 그 과정들은 생략하고 바로 API 테스트로 진행하겠다. API를 테스트하려면 Django 서버를 띄워줘야 한다.

$ sudo python manage.py runserver 0.0.0.0:8000

그러고 나서 앱 URL창에 'http://서버IP:포트번호'를 입력해 준 후 'API 테스트' 버튼을 누른다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/42.JPG?raw=true" width="750px">

설정이 잘 됐다면 이렇게 버튼이 잘 나오는 것을 확인할 수 있다. 외부에서 접속 허용을 해주지 않으면 저렇게 버튼이 나오지 않기 때문에 꼭 해줘야 하고 나머지 설정들을 해 준 다음 'API형 저장하기'를 눌러주면 끝이다. 그러면 이제 만든 앱을 추가해서 버튼이 나오는지 확인해보자. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/43.jpg?raw=true" width="px">

이렇게 버튼이 잘 나옴을 확인할 수 있다. 하지만 지금은 버튼만 나오게 했지, 버튼을 눌렀을 때 응답이 나오도록 구현하지는 않았다. 이제 그 부분을 구현해야 한다. 이 때 사용하는 것이 message 함수이다.<br>API 가이드에 보면 전송방법은 이렇게 나와있다.

```
{
    'message': {
        'text': '응답내용'
    },
    'keyboard':{
        'type': 'buttons',
        'buttons': ['A동 학식(교직원 식당)', 'B동 학식', '신기숙사(새로암학사) 식당', '구기숙사(두루암학사) 식당', '내일 메뉴']
    }
}

```

~/pooh_app/views.py를 열어 message 함수를 추가해주자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/44.JPG?raw=true" width="750px">

그 다음 keyboard 함수 때와 마찬가지로 message 함수 역시 보내야 하기 때문에 urls.py에서 url을 설정해줘야 한다. pooh_chatbot의 urls.py를 열자.

vim ~/pooh_chatbot/urls.py

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/45.JPG?raw=true" width="px">

아래에 표시된 내용을 추가해 준 다음 챗봇에서 버튼을 눌러 응답을 살펴보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/46.jpg?raw=true" width="px">

분명 맞게 만들어 준 거 같은데 에러가 뜬다. 왜 에러가 뜨나 봤더니

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/49.JPG?raw=true" width="px">

/message에 CSRF cookie not set이라고 한다. 이를 해결해주기 위해 다음을 추가해주면 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/47.JPG?raw=true" width="750px">

이러고 다시 챗봇을 실행을 시켜보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/48.jpg?raw=true" width="px">

버튼을 누를 때마다 응답이 나온다. 지금은 하나의 응답만 만들었기 때문에 모두 같은 응답이 나오지만 다음 포스팅에서는 각 메뉴에 대한 응답이 다르도록 코드를 짜보겠다.

<br><br><br>참고 사이트 : 

- http://codeac.tistory.com/12?category=731564

,

