---
layout: post
category: "linux"
title: "[CentOS] another app is currently holding the yum lock; waiting for it to exit..."
tags: ["linux"]
---

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/77.JPG?raw=true" width="px">


CentOS에서 yum 사용 시 이런 에러가 발생할 때가 있다. 위의 메시지가 출력되는 원인은 yum이 이미 다른 프로세스로 진행되고 있는 상황에서 다시 yum을 진행하려고 하기 때문이다.

위에 나온대로 보면 waiting for it to exit라고 나와있는 것으로 보아 해결책은 해당 프로세스가 끝날 때까지 기다리는 것인데 언제 끝날지도 모르고 한없이 기다릴 수가 없다. 그래서 2가지 해결방법을 제시한다.<br><br><br>

- 해당 yum 프로세스 정보를 담고 있는 파일을 삭제

먼저 root로 로그인을 한 후에

rm /var/run/yum.pid

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/74.JPG?raw=true" width="px">

이러고 나서 다시 yum을 사용하면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/73.JPG?raw=true" width="px">

명령어가 잘 수행된다.
<br><br><br><br>
- 해당 yum 프로세스 죽이기

먼저 root로 로그인을 한 후에

ps -aef \| grep yum	--> yum 프로세스들 중에 yum이 진행되는 것을 확인

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/78.JPG?raw=true" width="750px">

kill -9 [pid]로 yum이 사용하고 있는 프로세스 번호를 죽인다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/79.JPG?raw=true" width="px">

그러고 나서 다시 yum을 사용하면 명령어가 잘 수행된다.
<br><br><br><br>
참고 블로그 : http://blog.net2free.net/43
