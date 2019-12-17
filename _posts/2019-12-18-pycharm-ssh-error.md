---
layout: post
category: "python"
title: "[Pycharm] Could not verify 'ssh-rsa' host key with fingerfrint"
tags: ["python"]
---

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/65.JPG?raw=true" width="750px">

Pycharm에서 서버로 원격 접속을 하려고 했더니 이런 에러가 떴다. 해석해보면 'ssh-rsa' host key를 증명할 수 없다는 뜻으로 서버에서 필자의 Host Key를 믿을 수 없다며 거부한 것이다. 아래 메시지들에도 remote host identifcation has changed! 라고 해서 원격 호스트 증명이 바뀌었다고 나온다. 

이런 현상이 왜 발생했을까? 필자의 경우도 그렇고 보통은 서버의 이전 Host Key값을 가지고 있어서 그렇다. 필자의 경우를 설명해보겠다. <br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/75.JPG?raw=true" width="500px">

클라이언트와 서버가 각각의 Key를 가지고 서로 통신하고 있는 모습이다. 즉, 서버가 B를 가지고 있으면 클라이언트는 A를 가지고 있어야 해당 서버에 접속할 수 있고, 서버가 B'을 가지고 있으면 클라이언트는 A'을 가지고 있어야 해당 서버에 접속을 할 수 있는 것이다. <br>그런데, 필자의 경우 저 서버를 Docker Container로 돌리다 보니 컨테이너가 자주 바뀌어 Server IP는 그대로 설정되어 있지만 Server Key가 계속 바뀌었던 것이다. 즉, 그림을 예로 들면 처음에는 필자가 A를 가지고 있고 컨테이너 서버가 B를 가지고 있어 접속이 가능했으나 컨테이너가 바뀌면서 서버가 B'을 가지게 되었는데, 필자는 그대로 A를 가지고 서버에 접속하려고 했기 때문에 문제가 발생한 것이다.<br>따라서, 저 Host Key A를 제거해주면 된다. 아까 그림을 보면 Pycharm에서 저장하고 있는 Host Key가 어디에 위치해 있는지 알려준다 (Please contact your system administrator or add correct host key in C:\\Users\\Administrator\\\.ssh\\known_hosts to get rid of this message). C:\\Users\\Administrator\\\.ssh\\known_hosts 에 있다고 나와있으므로 해당 폴더로 가보자.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/66.JPG?raw=true" width="600px">

known_hosts 파일이 보인다. 이를 열어보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/67.JPG?raw=true" width="750px">

각 서버에 접속하기 위해 필요한 Host key들이 보인다 (서버 IP들은 모두 가렸습니다). 여기서 에러가 발생한 서버만 지워주면 된다. 그러고 나서 다시 Pycharm으로 돌아가 새로고침해주면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/70.JPG?raw=true" width="500px">

이런 화면이 뜨는데, Host Key가 바뀌어서 뜨는 창이므로 Yes 버튼을 눌러주면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/68.JPG?raw=true" width="750px">

디렉토리들이 나온다. 잘 연결된 것이다. 이 상태에서 아까 열었던 known_hosts 파일을 다시 열어보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/69.JPG?raw=true" width="750px">

해당 서버에 맞는 새로운 Host Key가 저장된 것을 확인할 수 있다. 즉, 서버로부터 새로운 Host Key를 받은 것이다 (아까 위의 에러 메시지 중에 The fingerprint for the ssh-rsa key sent by the remote host~ 부분이 있었기 때문에 서버로부터 받았을 것이라고 판단했습니다). 따라서, 원격 접속이 다시 잘 된다.<br><br><br><br>참고 사이트 : 

- <https://www.jetbrains.com/help/pycharm/creating-a-remote-server-configuration.html>