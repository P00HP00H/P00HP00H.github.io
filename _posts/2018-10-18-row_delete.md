---
layout: post
category: "linux"
title:  "vi(m) 편집기로 여러 줄 삭제 및 커서 이동"
tags: [linux]
---

여태까지 리눅스에서 vi(m) 편집기로 파일을 다룰 때 대부분 짧은 내용들의 파일들만 다뤄서 내용을 수정할 일이 있으면 그냥 delete 버튼을 눌러서 지웠다가 다시 쓰고 그랬는데 코드를 짜다보니까 자연스레 길이가 길어지게 되었다. 이러다 보니 여러 줄을 삭제해야 되는 경우가 생겼고 delete 버튼으로 하나하나 지우기에는 너무 오랜 시간이 걸렸다. 그래서 공부도 하는 겸 간단하게 포스팅해보려고 한다. 여러 줄 삭제하는 방법 외에 간단한 명령어들도 몇 가지 다뤄보겠다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/98.JPG?raw=true" width="px">

현재 kkk.txt에는 이런 내용들이 담겨 있다. 

- Home : 한 줄에서 맨 앞으로 이동하는 명령어
- End : 한 줄에서 맨 끝으로 이동하는 명령어
- gg : 첫 행으로 이동하는 명령어
- G 또는 Shift + g : 마지막 행으로 이동하는 명령어
- '숫자' + Shift + g 또는 '숫자' + gg: 입력한 숫자에 해당하는 행으로 이동하는 명령어

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/87.JPG?raw=true" width="px">

눈으로 보기 편하게 :set number로 줄번호들이 나오도록 했다. 그 다음 필자는 9번째 줄로 이동하고 싶어서 '9+gg'을 입력했다. 그랬더니 그림처럼 9번째 줄로 잘 이동했음을 확인할 수 있다.



이제 여러 줄을 한 번에 삭제하는 방법에 대해 알아보자. 방법은 간단하다. 

- '숫자' + dd

이 명령어는 해당 커서가 위치한 곳에서부터 '숫자'에 해당하는 줄의 수만큼을 삭제한다. 숫자를 입력하지 않고 dd만 입력하면 해당 커서가 위치한 1개의 줄만 지운다. 다음 그림을 보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/100.JPG?raw=true" width="px">

여기도 이해하기 쉽게 :set number 명령어를 이용하였다. 필자는 5번 ~ 10번(총 6개) 줄에 해당하는 내용을 지우고 싶다. 그러면 5번으로 커서를 이동시킨 후 "6 + dd" 입력해주면 된다. 5번 ~ 10번을 삭제하면 그 다음 줄, 즉, 11번인 container가 5번자리로 와야 한다. "6 + dd"를 입력해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/101.JPG?raw=true" width="px">

맨 밑에 6 fewer lines 라는 메시지가 뜨면서 6줄이 지워졌음을 확인할 수 있다. 예상대로 5번째 줄에는 container 단어가 위치하게 된다. 

그럼 만약 해당 커서가 위치한 곳에서부터 그 아래 내용들을 모두 지우려면 어떻게 해야할까?? 이렇게 하면 된다.

- dG

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/102.JPG?raw=true" width="px">

커서를 linux 단어에다가 놓고 dG를 입력하면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/103.JPG?raw=true" width="px">

맨 밑에 14 fewer lines라는 메시지와 함께 linux라는 단어를 포함해서 그 밑의 내용들이 모두 지워졌다.
<br><br><br>
이번엔 커서 이동 명령어에 대해서 알아보자. 코드가 끊기지 않고(줄바꿈 되지 않고) 길어지면 n줄이 되더라도 줄번호는 하나로 인식된다. 그런 경우 그 줄의 코드 중간으로 가려면 한칸 한칸씩 이동해야 하는데 너무 귀찮다. 이런 경우는 특정 단어를 검색하면 빠르다. 방법은

- /'단어'

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/92.JPG?raw=true" width="750px">

이렇게 코드가 길어지면 육안상 6줄인데도 불구하고 줄번호는 이 전체가 155번째 줄이다. 이런 경우는 6줄 전체를 코드 1줄로 인식하기 때문에 아래 방향키가 먹히지도 않는다. 그래서 같은 줄이라고 인식되는 파란색으로 표시된 곳을 수정하고 싶은 경우 오른쪽 방향키로 커서를 하나하나 움직여야 하는데 너무 귀찮다. 이 때 검색을 이용하면 검색된 단어가 있는 곳으로 커서가 이동되기 때문에 바로 수정해 줄 수 있다. 현재 커서 위치는 155번째 줄의 맨 첫 번째에 위치해 있는데 이 상태에서 초록색 부분을 검색하면(다음 그림 맨 밑 - /4,300원)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/99.jpg?raw=true" width="750px">

커서(연두색)가 검색한 단어 있는 곳으로 옮겨짐과 동시에 검색한 부분이 노란색으로 표시되었음을 확인할 수 있다. i(INSERT)를 눌러 바로 수정해주면 된다.
<br><br><br>
이번엔 화면 단위 이동 명령어에 대해 알아보자.

- Page Down,  Ctrl + f : 한 화면 밑으로 이동하는 명령어


<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/94.JPG?raw=true" width="px">

한 화면 밑으로 이동하게 되면 이전 화면의 맨 밑에서 두 번째 줄이 첫 줄로 시작된다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/95.JPG?raw=true" width="px">

또한 이는 화면 크기에 따라 달라질 수 있다. 예를 들어 화면을 최대화해서 20번째 줄까지 보였다면 다음 화면으로 넘어갔을 때는 18번째 줄이 첫 줄로 시작된다. 하지만 화면을 작게 해서 10번째 줄까지 보였다면 다음 화면으로 넘어갈 때는 8번째 줄이 첫 줄로 시작된다는 뜻이다. 

- Page Up, Ctrl + b : 한 화면 위로 이동하는 명령어


위의 Page Down과 Ctrl + f와 반대라고 생각하면 된다. 처음 화면에서 맨 첫 줄이 한 화면 위로 가게 되면 맨 마지막에서 2번째 줄이 된다. 이 역시 화면 크기에 따라 달라질 수 있다.

- Ctrl + d : 반쪽 화면 밑으로 이동하는 명령어

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/96.JPG?raw=true" width="px">

반쪽 화면 줄이 첫 줄로 이동된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/97.JPG?raw=true" width="px">

이 역시 화면 크기에 따라 달라질 수 있다.

- Ctrl + u : 반쪽 화면 위로 이동하는 명령어

위의 Ctrl + d와 반대라고 생각하면 된다. 처음 화면에서 맨 첫 줄이 다음 화면의 중간 줄로 가게 된다. 이 역시 화면 크기에 따라 달라질 수 있다.




지금까지 배운 것들을 이용하면 코드가 길어져도 수정해야 될 부분들을 빠르게 수정할 수 있을 것이다.

<br><br><br>참고 사이트 : 

- http://www.leafcats.com/115
- http://blog.syszone.co.kr/196
- https://tip.daum.net/openknow/3701363

,

