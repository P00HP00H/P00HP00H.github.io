---
layout: post
category: "chat-bot"
title:  "[PHP 챗봇] 2. 플러스 친구 가입하기"
tags: [chat-bot]
---



### 카카오톡 챗봇

챗봇이란 채팅과 로봇의 합성어로 인공지능(AI)을 기반으로 사람과 대화를 나누는 프로그램으로 카카오톡 챗봇의 경우, 카카오톡 자동응답 API를 통하여 사용자가 입력한 input값에 대해 json방식으로 답변을 해준다. 

카카오톡 플러스 친구에서 지원해주는 API들이 있는데, 입력값을 넣었을 때 응답만 해주면 되는 챗봇이라서 keyboard와 message기능만 구현해주면 된다. 

![챗봇](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%EC%B1%97%EB%B4%87.jpg?raw=true)

이번에 만들어 본 챗봇이고, 원하는 시간대에 버스시간을 입력하면 해당 버스시간표를 조회해준다. 
<br><br><br><br>
### 플러스 친구 가입하기

![플러스친구](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC.JPG?raw=true)

먼저 앱을 등록해야 하는데, 그러기 위해선 플러스 친구에 가입을 해야 한다. https://center-pf.kakao.com 여기로 들어가서 '플러스친구 만들기'를 눌러 카카오톡 아이디로 로그인을 하면 이런 화면이 나온다.

![플러스친구2](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC2.JPG?raw=true)

여기서 '새 플러스친구 만들기'를 클릭한다.

![플러스친구3](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC3.JPG?raw=true)

필요한 부분들을 채워넣고 확인을 누르면

![플러스친구4](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC4.JPG?raw=true)

왼쪽을 보면 '스마트채팅' 버튼이 있다. 이를 클릭해보면

![플러스친구5](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC5.JPG?raw=true)

연필 아이콘 모양이 있는데, 이를 클릭하면

![플러스친구6](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC6.JPG?raw=true)

여기를 보면, 다른 거는 그냥 입력해주면 되는데 앱URL 부분이 문제이다. 오른쪽을 보게 되면 'API테스트'가 있는데 이를 통과해야만 한다. 앱 URL주소는 해당서버에 keyboard.php가 존재하는 경로이면 된다. 필자의 경우, /var/www/html/busbot/keyboard.php였기에 앱 URL에 'http://서버주소/busbot'를 입력해줬다.

다음은 keyboard.php의 소스이다.

![keyboard](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/keyboard.JPG?raw=true)

하지만 해보면, 이런 오류가 발생한다.

![플러스친구7](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC7.JPG?raw=true)

이 오류는 서버에 설정을 해주지 않아서이다. 먼저 아파치 기본설정 파일(/etc/apache2/apache2.conf)에 들어가서 화면처럼 설정을 해준다. 

![apache2](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/apache2.JPG?raw=true)

그리고 mime.conf에서도 php옵션을 설정해준다.

![mime](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/mime.JPG?raw=true)

설정을 다 해줬으면 아파치를 restart한다. -> /etc/init.d/apache2 restart 

- 참고 블로그 : http://kugancity.tistory.com/entry/%EC%B9%B4%EC%B9%B4%EC%98%A4%ED%86%A1-%ED%94%8C%EB%9F%AC%EC%8A%A4-%EC%B9%9C%EA%B5%AC-%EA%B0%9C%EB%B0%9C-2%ED%83%84 
<br>

그림대로 다 해주게 되면 이런 화면이 나온다.

![플러스친구성공](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC%EC%84%B1%EA%B3%B5.JPG?raw=true)

맨 밑으로 가면 'API 저장하기'가 있는데 이를 클릭한다. 이러면 API설정은 끝났다.

![플러스친구8](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC8.JPG?raw=true)

'시작하기' 버튼을 눌러주면 API가 실행된다.

![플러스친구9](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%ED%94%8C%EB%9F%AC%EC%8A%A4%EC%B9%9C%EA%B5%AC9.JPG?raw=true)

이러면 잘 실행이 되는 것이다.

 

![bus_test검색](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/bus_test%EA%B2%80%EC%83%89.jpg?raw=true)

여기서 친구추가를 한 후에 1:1채팅을 해보자.

![시작](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/kakao/%EC%8B%9C%EC%9E%91.jpg?raw=true)

채팅방을 보면 '시작' 버튼이 있다. 아직은 message부분이 제대로 구현돼있지 않아서 이 시작버튼을 눌러보면 완성본처럼 다양한 응답값이 나오지 않는다. 다음 포스팅에서는 message부분을 만들어보자.
