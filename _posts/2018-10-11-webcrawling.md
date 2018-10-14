---
layout: post
category: "chat-bot"
title:  "[Django 챗봇] 3-1. 웹 크롤링(1)"
tags: [chat-bot]
---

저번 포스팅에서 버튼까지 완성을 시켰고 이제 각 버튼에 대한 응답을 처리해야 하는데, 이 챗봇은 '학식 메뉴'를 알려주는 챗봇이기 때문에 학식 메뉴에 대한 정보를 가져와서 사용자에게 응답해줘야 한다. 필자는 학식 메뉴를 홈페이지에서 크롤링하여 가져오려고 한다.



- <h3>웹 크롤링</h3>

  : 크롤링(crawling) 혹은 스크래이핑(scraping)이라고도 하며, 웹 페이지를 그대로 가져와서 원하는 데이터를 추출해내는 행위를 말한다. 크롤링하는 소프트웨어는 크롤러(crawler)라고 하며, 웹 크롤러(web crawler)는 world wide web(www)을 탐색하는 컴퓨터 프로그램이다.

  ​



현재는 views.py에 keyboard함수와 message함수만 구현되어 있는 상태이다. views.py에 라이브러리 추가한 다음 함수 만들어서 구현해도 되지만 필자는 따로 parsing.py 파일을 만들어서 진행하려고 한다. 크롤링 할 때는 BeautifulSoup 라이브러리를 이용할 것이다. BeautifulSoup은 html을 파싱하는데 사용하는 Python 라이브러리이다.

그 전에 먼저 http request 라이브러리인 requests를 설치해줘야 한다. requests 라이브러리는 파이썬에 내장된 urllib 모듈을 편하게 사용하도록 만든 것으로 설치는 pip를 이용해서 할 수 있다.

$ sudo pip install requests

requests도 정말 좋은 라이브러리지만, 필자는 BeautifulSoup을 사용하려고 한다. 그 이유는 BeautifulSoup가 Python이 이해하는 객체구조로 변환해주기 때문이다. 무슨 말이냐면 위의 req.text는 HTML 소스를 가져오는 부분인데 이 부분은 Python의 문자열(str) 객체를 반환할 뿐이기 때문에 원하는 정보를 추출하기가 어렵다. 좀 더 쉽게 말하면, Python 입장에서는 단지 'hihello안녕' 같은 문자열이라고밖에 인식하지 못한다는 뜻이다. 그래서 이를 BeautifulSoup 라이브러리를 이용해 html 코드를 Python이 이해하는 객체 구조로 변환한 다음 Python으로 원하는 정보를 추출하려고 하는 것이다. BeautifulSoup 역시 pip를 통해 설치하면 된다.

$ sudo pip install bs4

이제 parsing.py를 만들자.

$ vim parsing.py

경로는 지정해주고 싶은대로 해주면 되지만 필자의 경우 pooh_app이 있는 즉, views.py와 같은 경로에다가 만들었다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/57.JPG?raw=true" width="px">

- import requests<br>from bs4 import BeautifulSoup

  : BeautifulSoup의 기능들을 사용하기 위한 설정이다.


- req = requests.get(http://sj.hongik.ac.kr/site/food/food_menu.html)

  : 크롤링 할 홈페이지를 지정하고 요청하는 부분이다(HTTP GET Request). 

- html = req.text

  : HTML 소스를 가져오는 부분이다. 실제로 print해보면 모든 웹페이지를 즉, html 태그까지 모조리 다 보여준다. 

  <img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/58.JPG?raw=true" width="750px">

  하지만 이거는 html 문법이지 Python 문법이 아니다. 즉, Python 입장에서는 그냥 어떤 문자열이라고밖에 인식하지 못한다. 

- soup = BeautifulSoup(html, 'html.parser')

  : BeautifulSoup으로 HTML 소스를 Python 객체로 변환하는 부분으로 html 부분은 html 소스코드, html.parser는 어떤 parser를 이용할지 명시해주는 부분이다. 이따가 뒤에 내용들을 추가하면서 다시 설명하겠다. 이 부분으로 인해 Python이 문자열로만 인식했던 HTML 소스를 우리가 이해하는 HTML 태그나 구조들처럼 인식할 수 있는 것이다. 




이제 기본적인 구성이 끝났다. 여기에 내가 하고자 하는 것을 추가해주면 된다. 필자는 홈페이지에서 학식 메뉴를 가져오려고 한다. 학식 메뉴는 웹 페이지에서 어떻게 구성되어 있는지 살펴보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/59.JPG?raw=true" width="750px">

이런 식으로 나와 있다. 각 메뉴가 어떻게 되어 있는지 확인해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/60.JPG?raw=true" width="px">

각 메뉴들은 이렇게 \<div class="foodmenu"> 메뉴들 \</div> 이런 식으로 구성되어 있다. 그러면 BeautifulSoup에서 제공하는 기능인 select를 이용해 \<div class="foodmenu">로 이루어진 문구들을 추출하면 메뉴들이 보이게 될 것이다. 이런 조작이 가능한 것도 BeautifulSoup가 Python이 HTML 소스를 이해하도록 객체구조를 변환해주기 때문이다.

(참고 : select는 CSS Seletor를 이용해 조건과 일치하는 모든 객체들을 List로 반환해준다.)

이를 코드로 작성하면

```
pooh_menu = soup.select(
    'div[class=foodmenu]'
    )
```

이 부분을 추가해주면 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/61.JPG?raw=true" width="px">

```
for menu in pooh_menu:
    print(menu.text)
```

이 부분은 크롤링이 잘 나오는지 눈으로 확인하기 위해 넣은 것이다. menu.text를 한 이유는 글자들만 뽑아내기 위함이다. 만약에 text를 쓰지 않고 print(menu)를 하면 태그까지 다 나온다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/63.JPG?raw=true" width="px">

이게 print(menu)를 한 상태의 parsing.py를 실행한 화면이다. 하지만 우리는 태그까지는 필요없기 때문에 저렇게 menu.text를 사용한 것이다. 다시 parsing.py를 실행시켜보면

$ sudo python3 parsing.py

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/vmserver-setting/62.JPG?raw=true" width="px">

이렇게 메뉴들이 잘 나오게 된다. 다음 포스팅에서는 이 메뉴들을 각 버튼에 맞게 어떻게 보여줄 것인지에 대해 포스팅하겠다.

<br><br><br>참고 사이트 : 

- https://beomi.github.io/2017/01/20/HowToMakeWebCrawler/
- https://medium.com/@mjhans83/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-%ED%81%AC%EB%A1%A4%EB%A7%81-%ED%95%98%EA%B8%B0-908e78ee09e0

,

