---
layout: post
category: "python"
title:  "IndentationError: unindent does not match any outer indentation level"
tags: [python]
---

Django에서 파이썬 코드를 돌리던 도중에 이런 에러가 났다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/1.JPG?raw=true" width="750px">

views.py 81번째 줄에서 에러가 나서 urls.py에도 에러가 발생한 상태이다. 에러가 발생한 views.py로 가보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/2.JPG?raw=true" width="750px">

파란색 부분이 에러가 발생한 위치이다. 하지만 코드상 아무리 봐도 잘못된 부분이 없는 것 같았다. 그래서 에러 IndentationError: unindent does not match any outer indentation level을 검색해보니 들여쓰기가 잘못돼서 나는 에러라고 한다. 리눅스의 vim으로 보니까 어느 부분에서 들여쓰기가 잘못됐는지 잘 안 보여서 Pycharm으로 코드를 보기로 했다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/3.JPG?raw=true" width="750px">

보니까 에러난 곳에 들여쓰기가 한 칸 더 되어있었다. 툴이 좋긴 좋은 것 같다. 위(파란색 표시)에도 빨간색 부분으로 잘못돼있다고 표시가 되어있었다. 들여쓰기를 제대로 맞춰준 후 다시 돌려보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/4.JPG?raw=true" width="px">

오류가 뜨지 않고 잘 된다. 

<br><br><br>참고 사이트 : 

- https://thebook.io/006855/xa/02_10/

,

