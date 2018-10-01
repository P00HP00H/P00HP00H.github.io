---
layout: post
category: "chat-bot"
title:  "[Django 챗봇] 2-3. 버튼 만들기(3)"
tags: [chat-bot]
---

저번 포스팅에서 버튼을 대략 완성시켰는데

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/55.jpg?raw=true" width="px">

현재는 어떤 버튼을 눌러도 모두 '학식 메뉴입니다'라는 메시지만 뜬다. 하지만 최종적으로는 저 버튼에 대한 응답은 모두 달라야 한다. 현재까지 짠 코드들을 살펴보면서 다음 코드들을 어떻게 짤지 구상해보도록 하자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/50.JPG?raw=true" width="750px">

코드들을 보게 되면 keyboard 부분이 챗봇 화면에서 버튼으로 나타나는 형식이다. '도움말', '내일 메뉴'를 제외한 나머지 버튼들은 학식 정보를 크롤링하여 불러올 것이기 때문에 나중에 다루기로 하고 이번 포스팅에서는 '도움말', '내일 메뉴' 버튼만 다뤄보도록 하자. '도움말' 버튼을 누르면 필자가 만든 챗봇을 소개하는 문구, 즉, 텍스트만 넣을 것이고 '내일 메뉴' 버튼을 누르면 저 버튼들이 '내일 A동 학식', '내일 B동 학식' 이런 식으로 나오기를 원했다. 즉, 각 버튼에 대한 응답값이 달라야 한다는 말이다. 이를 위해 다음 부분을 추가해주자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/51.JPG?raw=true" width="750px">

추가해 준 변수들을 해석해보면

- json_str = (request.body).decode('utf-8')

  : request 파라미터를 한글 호환이 되는 'utf-8'로 바꾸어 json_str에 저장한다.

- received_json = json.loads(json_str)

  : json 문자열로 되어 있는 것을 Python 타입으로 변경시켜 recevied_json에 저장한다.

- content_name = received_json['content']

  : 사용자가 누른 버튼에서 필드명이 content인 항목을 저장하는데, 이를 통해서 각 버튼값을 구별한다.



그 밑의 코드들을 보게 되면 if문을 통해 각 버튼값에 대한 응답을 다르게 return 하도록 했다. 그럼 코드대로 잘 나오는지 챗봇에서 확인해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/52.JPG?raw=true" width="px">

잘 나온다. 하지만 필자는 '내일 메뉴' 버튼을 눌렀을 때 각 버튼들이 '내일 A동 학식', '내일 B동 학식' 이런 식으로 바뀌기를 원했다. 방법은 간단하다. 현재 if content_name = "내일 메뉴" 부분의 keyboard 부분이 "내일 메뉴" 버튼을 눌렀을 때 챗봇에서 보여지는 버튼들이다. 이 keyboard 부분을 수정하면 "내일 메뉴" 버튼을 눌렀을 때 버튼들이 바뀔 것이다. 직접 해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/53.JPG?raw=true" width="750px">

keyboard 버튼을 수정했다. 참고로 "뒤로"는 다시 오늘 메뉴 버튼으로 돌아올 수 있도록 하기 위해 만든 것이다. 구상한 대로 잘 동작하는지 챗봇을 실행시켜보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/54.jpg?raw=true" width="px">

"내일 메뉴" 버튼을 누르니 각 버튼들이 바뀌었음을 확인할 수 있다. 각 버튼들에 대한 응답값은 이런 식으로 if문을 통해 처리해주면 된다. 다음 포스팅에서는 크롤링에 대해 알아보자. 

<br><br><br>참고 사이트 : http://codeac.tistory.com/13?category=731564

,

