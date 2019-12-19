---
layout: post
category: "python"
title: "[Pycharm] 파일 view 유형 바꾸기"
tags: ["python"]
---

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/76.JPG?raw=true" width="400px">

필자는 Pycharm으로 파이썬 파일 말고도 여러가지 유형의 파일들을 다룬다(그냥 Pycharm의 스타일이 좋아서...). 그러다보니 Pycharm에서도 여러 유형의 파일 view를 제공하는데 Pycharm 자체에서 파이썬 파일 말고는 사용자들이 어떤 유형의 파일 view를 원하는지 모르기 때문에 위 그림처럼 선택하게 한다. 즉, 필자의 경우, ejs 파일을 Pycharm에서 제공하는 유형의 파일 중 어떤 것으로 보여줄까라고 묻는 것이다.<br>일단 어떤 유형의 파일과 맞는지 잘 모르기 때문에 그냥 Typescript 파일로 해봤다. 그랬더니<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/71.JPG?raw=true" width="600px">

이렇게 나왔다. 딱 봐도 빨간 글씨가 많고 보기에 전혀 예쁘지가 않다. 즉, 유형을 잘못 택했다는 소리다. 따라서, 이를 다른 유형의 view 파일로 바꿔보자. 먼저 File -> Settings -> Editor -> File Types로 간다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/72.JPG?raw=true" width="650px">

가면 이런 화면이 나오는데, 여기서 위쪽부분의 Recognized File Types에서 내가 선택한 view 타입 (필자는 Typescript) 을 누르면 Registered Pattern에 내가 지정했던 파일이 나온다 (필자는 ejs). 이를 오른쪽에 "-"를 눌러 삭제한다. 그러고 나서 ejs 파일을 다시 열어보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/76.JPG?raw=true" width="400px">

이렇게 아까처럼 다시 view를 선택하라고 나온다. 여기서 다시 선택해주면 되고 또 잘못 선택했으면 위 과정을 반복하면 된다. 아래는 필자의 잘 선택된 경우이다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/74.JPG?raw=true" width="600px">

아까보다 훨씬 보기 좋아졌다.<br><br><br><br>참고 사이트 : 

- <http://ko.voidcc.com/question/p-qrecvzim-dp.html>