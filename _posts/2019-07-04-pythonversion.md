---
layout: post
category: "linux"
title: "리눅스에서 Python 버전 변경 및 yum 에러 해결"
tags: ["linux"]
---

이번 포스팅에서는 리눅스의  Python 버전을 변경하는 법에 대해 포스팅해보려고 한다. 리눅스를 깔게 되면 보통 Python 2.7이 깔려있어 python 명령을 실행하게 되면 Python2.7이 실행된다. 하지만 필자는 Python3 이상이 필요해서 python 명령을 실행했을 때 Python3.x가 실행되기를 원해서 Python 버전을 변경해보기로 했다. 리눅스 환경은 CentOS 7이다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/43.JPG?raw=true" width="300px">

Python 버전을 확인해보면 역시나 Python 2.7.5로 되어 있다. Python3를 설치하기 전에 먼저 업데이트를 해준다.<br>

$ yum update -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/45.JPG?raw=true" width="350px">

Python3을 설치하기 위해 먼저  IUS Community Project의 repository를 yum의 repository에 추가해준다.<br><br>

$ yum install -y https://centos7.iuscommunity.org/ius-release.rpm

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/44.JPG?raw=true" width="750px">

추가했으면 yum의 repository에 Python3.6(필자가 Python3.6을 설치할 것이기 때문 -> Python3.5를 설치할거면 yum search Python35라고 하면 된다)이 잘 들어가있는지 확인한다.<br><br>

$ yum search Python36

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/46.JPG?raw=true" width="750px">

이 부분은 IUS Community Project의 repository를 yum의 repository에 추가가 잘 됐는지 확인하는 과정이다. 잘 설치가 됐다면 사실 안해도 되는데 yum의 repository에 어떤 이유로 추가가 안됐을 수도 있기 때문에 확인차 한 번 한다는 개념으로 생각하면 될 것 같다. 추가가 안된 경우 yum search를 하면 이런 화면이 나온다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/47.JPG?raw=true" width="350px">

이 상태에서 Python3.6을 설치해보면<br><br>

$ yum install -y python36u python36u-libs python36u-devel python36u-pip

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/48.JPG?raw=true" width="750px">

이렇게 설치가 되지 않는다.<br><br><br>다시 돌아와서 yum search로 추가가 된 것을 확인했으면 이제 Python3.6을 설치해주면 된다.

$ yum install -y python36u python36u-libs python36u-devel python36u-pip

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/49.JPG?raw=true" width="750px">

다 설치됐다면 버전을 확인해보자.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/50.JPG?raw=true" width="400px">

Python3.6 -V으로 버전을 확인해보면 Python3.6이 잘 설치됐음을 확인할 수 있다. 그런데, 리눅스에서는 아직 Python2.7.5로 되어 있다. 이를 Python3.6으로 바꿔보도록 하자. 일단 Python3.6이 어딨는지 확인해보면<br><br>

$ which python3.6

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/59.JPG?raw=true" width="400px">

Python3.6이 설치된 경로가 나온다. which 명령어가 없으면 yum install which로 설치해주면 되고 설치된 경로로 보아 /usr/bin/에 Python 파일들이 있을 것이라는 걸 알 수 있다. 이걸 왜 해주냐면 /usr/local/bin/에 있는 경우도 있고 /bin/에 있기도 하고 그렇다. 즉, 각 환경에 따라 다르기 때문에 자기의 환경에는 어디에 설치되어있는지 확인한 후 설정을 해주려는 것이다.<br><br>$ ls -l /usr/bin/python*

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/51.JPG?raw=true" width="750px">

여기를 보게 되면 왜 python -V를 입력했을 때 Python 2.7.5가 나오는지를 알 수 있다. python -V를 입력하게 되면 /usr/bin/python에서 정보를 불러오는데 /usr/bin/python -> /usr/bin/python2 -> /usr/bin/python2.7이어서 python2.7을 불러오기 때문이다. 즉, Python 버전을 3으로 바꾸려면 /usr/bin/python을 /usr/bin/python3.6으로 심볼릭링크를 걸어주면 된다.  /usr/bin/python -> /usr/bin/python3.6으로 하면 무슨 일이 일어나는지 보자.<br><br>

$ unlink /usr/bin/python

$ ln -s /usr/bin/python3.6 /usr/bin/python

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/52.JPG?raw=true" width="750px">

심볼릭링크를 바로 python3.6에 연결해줬고 python -V로 확인해봐도 Python3.6.7이 나온다. 전혀 문제가 없어보이지만 그렇지가 않다. 이러면 yum에 문제가 생기게 된다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/53.JPG?raw=true" width="450px">

이렇게 yum 명령어가 먹히지 않는다. 그 이유는 yum 명령어가 Python2.x로 작성되어 있기 때문이다. 따라서, 다시 Python2.x와 연결시켜주던지($ unlink /usr/bin/python 한 후 \$ ln -s python2(또는 /usr/bin/python2) /usr/bin/python 명령어를 입력해주면 된다) 또는 다음과 같이 해결할 수 있다.<br><br>

$ vim /usr/bin/yum

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/56.JPG?raw=true" width="550px">

파란색 글씨라 잘 안 보이겠지만 yum 파일의 맨 위를 보면 #!/usr/bin/python으로 되어 있는데 이를 #!/usr/bin/python2.7로 수정해주면 된다.<br><br>$ yum install net-tools -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/57.JPG?raw=true" width="750px">

yum install net-tools -y 말고 다른 것을 설치해도 상관없다. 필자는 그냥 yum으로 net-tools를 설치하고 싶어서 한 것일 뿐 yum이 정상작동하는지 못하는지에만 초점을 두면 된다. 보면 아까처럼 yum 명령어가 막히지는 않는데 성공하지는 못했다. 패키지를 다운받는 과정에서 문제가 발생한 것이다. 이 부분도 해결해줘야 한다.<br><br>$ vim /usr/libexec/urlgrabber-ext-down 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/58.JPG?raw=true" width="600px">

아까 /usr/bin/yum에서 했던 것과 동일하다. 역시 파란색 글씨라 잘 안 보이겠지만  /usr/libexec/urlgrabber-ext-down 파일의 맨 위를 보면 #!/usr/bin/python으로 되어 있는데 이를 #!/usr/bin/python2.7로 수정해주면 된다. 이제 다시 yum을 실행해보자.<br><br>$ yum install net-tools -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/54.JPG?raw=true" width="750px">

드디어 성공했다. 이제는 패키지도 잘 다운받아진다.<br><br>지금까지 Python 버전 변경 및 그에 대한 yum 에러까지 해결해봤는데 사실 이는 별로 좋은 게 아니라고 한다. 왜냐면 애초에 yum을 포함한 이런 시스템들이 python2.x로 작성돼있어서 현재는 에러가 딱히 나지 않았지만 분명 다른 내장패키지들이라던지 이런 부분에서 에러가 날 수 있기 때문이다. 그래서, 불편해도 symlink를 변경해서 사용하기보다는 python은 python으로 python3.x는 python3.x라는 명령어로 사용하기를 권고한다고 한다.<br><br><br>P.S : /usr/bin/python -> /usr/bin/python3.6으로 하지 않았으면 초기 상태는 /usr/bin/python -> /usr/bin/python2이므로 python 명령을 실행하면 Python2.x가 실행된다. 즉, Python3.x 모듈을 쓰고 싶으면 python3.x test.py 이런식으로 써야하는데(/usr/bin/python3.6에서 모듈을 불러와야 Python3.6을 쓸 수 있기 때문) 3.x라고 매번 쓰기가 좀 귀찮을 수 있다. 이럴 때는 /usr/bin/python3 -> /usr/bin/python3.6으로 해주면 된다.

$ ln -s /usr/bin/python3.6 /usr/bin/python3

이러면 python3 test.py로 쓰더라도 Python3.6 모듈을 불러오게 되어 Python3.6을 쓸 수 있다.<br><br><br><br>참고 사이트 : 

- <https://depository.tistory.com/2>
- <https://this-programmer.tistory.com/entry/CentOS%EB%A1%9C-Python3x%EC%93%B0%EA%B8%B0-%EB%B0%8F-yum%EC%97%90%EB%9F%AC-%ED%95%B4%EA%B2%B0>