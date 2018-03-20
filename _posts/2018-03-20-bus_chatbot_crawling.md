---
layout: post
category: "chat-bot"
title:  "[PHP 챗봇] 3. 웹 크롤링"
tags: [chat-bot]
---

![chatbot-arc](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/chatbot-arc.JPG?raw=true)



VM 서버 셋팅 때 있었던 그림인데 3번을 보게 되면 크롤링이라는 것이 있다. 크롤링에 대해서 살펴보자.
<br><br><br><br>
<h2>웹 크롤링이란?</h2>

크롤링(crawling) 혹은 스크래이핑(scraping)이라고도 하며, 웹 페이지를 그대로 가져와서 원하는 데이터를 추출해내는 행위를 말한다.  크롤링하는 소프트웨어는 크롤러(crawler)라고 하며, 웹 크롤러(web crawler)는 world wide web(www)을 탐색하는 컴퓨터 프로그램이다.

필자의 경우 PHP로 구현을 했기 때문에  PHP로 작성된 스누피 클래스(Snoopy class)를 이용하였다.
<br><br>
- <h3>스누피 클래스(Snoopy class)</h3>

: PHP로 작성된 외부 소켓 클래스로 웹 사이트 내용을 쉽게 크롤링해 올 수 있도록 해주는 클래스

![snoopy](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/snoopy.JPG?raw=true)

https://sourceforge.net/projects/snoopy/files/latest/download 이 링크를 따라가면 zip파일이 다운받아진다. zip파일의 압축을 풀어보면

![snoopyfile](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/snoopyfile.JPG?raw=true)

이런식으로 나오게 된다. 이를 그대로 서버에 넣어주면 되고 경로는 어디로 하던 상관없다. 필자의 경우 keyboard.php와 message.php가 있는 경로에 다 넣었다.

![directory](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/directory.jpg?raw=true)



저번 포스팅에서 keyboard.php를 살펴봤으니 이번엔 message.php를 살펴보자.

![message](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/message.JPG?raw=true)

이게 맨 처음 만든 message.php이다. 밑에 부분은 각 명령어에 대한 응답처리이고, 3번째 줄을 보게 되면  json방식으로 들어온 요청값이 json_decode 함수에 의해 배열 또는 객체로 변환된 후 \$data 변수에 저장된다. 4번째 줄을 보면,  \$data변수에 저장된 배열 또는 객체에서 content 항목인 부분만 다시 \$content 변수에 저장한다. 쉽게 말해, \$content 변수에는 사용자가 입력한 명령어가 들어가게 된다고 생각하면 된다. 이 message.php에 크롤링 기능이랑 각 명령어 응답처리들을 추가해줄 것이다.

먼저 크롤링 기능을 추가해주기 위해 message.php에서 스누피 클래스를 이용해보자. 2,5,6번째 줄만 추가해주고 나머지는 그대로 두면 된다.

![snoopy추가](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/snoopy%EC%B6%94%EA%B0%80.JPG?raw=true)

현재 snoopy클래스 파일들을 message.php와 같은 경로에 넣었기 때문에 저렇게 해준 것이지, 다른 경로에 넣어도 전혀 상관이 없다. 6번째 줄에는 크롤링할 URL을 넣어주는 부분이 있다. 지금 써져 있는 URL은 학기 중 버스 시간표가 있는 URL이다. 

![시간표](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/%EC%8B%9C%EA%B0%84%ED%91%9C.JPG?raw=true)

처음에 생각한 것은 특정 시간을 입력하면 해당 시간표만 뽑아오도록 하는 것이었다. 그래서 정규식을 통해 뽑아야겠다는 생각을 했는데, html 중에 숫자가 들어가 있는 경우 이것도 뽑아낼 거 같아 좀 더 확실하게 구별해주기 위해서 ':' 까지 같이 뽑아내도록 했다. 하지만 문제가 생겼다.

![주석](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/%EC%A3%BC%EC%84%9D.JPG?raw=true)

처음에 버스 시간 페이지를 보고 학기 중 시간표만 나와 있어 학기 중 버스 시간들만 작성돼있을거라고 생각했는데 이런 식으로 방학 시간표들이 주석처리되어 같이 들어가 있었다. 아무래도 방학 때 시간표를 다시 작성하기 귀찮아 저런 식으로 주석처리 해놓고 방학이 되면 학기 시간표를 주석처리하고 방학 시간표의 주석처리를 없애는 식으로 운영을 하는 것 같았다. 이 때문에 크롤링할 때 방학 버스 시간도 같이 불러오게 되어 원하는 응답이 나오지 않게 되는 상황이 벌어졌다. 

그래서 학기 중 시간표만 불러오도록 하기 위해 주석처리를 제외한 나머지 값들만 불러오도록 해봤지만 실패해서 어쩔 수 없이 따로 웹파일 하나를 만든 후에 주석처리를 제외한 나머지 부분들만 싹 긁어서 복사했다(실력이 부족해서...ㅠㅠ) . 그게 바로 밑에 보이는 bus.php이다. bus.php에는 학기 중 운행되는 현재 버스 시간표만이 담겨 있다.     

![bus](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/crawling/bus.JPG?raw=true)

아까 위의 그림과 비교했을 때 URL부분이 달라졌음을 확인할 수 있는데 결국 학기 중 버스시간표만 있는 bus.php를 크롤링할 것이라는 말이다. 
다음 포스팅에서는 이어서 소스코드에 대한 설명을 하겠다.

 

,

