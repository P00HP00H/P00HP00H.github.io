---
layout: post
category: "spark"
title: "sbt 설치하기"
tags: ["spark"]
---

이번 포스팅에서는 sbt를 설치하는 방법에 대해 알아보고자 한다. 필자는 Spark를 Scala로 하려고 하기 때문에 SBT를 써야만 한다. 그래서 이를 다루는 법에 대해 공부해 볼 예정이다.

일단, SBT의 정의에 대해서 보면, SBT란 'Simple Build Tool'이라고 해서 Java에서의 ant나 Maven처럼 프로젝트를 셋팅해주고 자동으로 빌드 및 실행하는 등의 역할을 한다. 필자는 추후에 Scala코드들을 SBT를 이용해 jar파일로 만들어 Spark-submit로 Spark 프로그램을 실행시킬 것이다. 그러기 위해선 먼저 설치를 해야 한다. 설치 환경은 VM의 Ubuntu 16.04와 CentOS 7 이렇게 2가지로 나눠서 진행했다.<br><br><br>**(1) Ubuntu 16.04**

: 먼저 Ubuntu일 때이다. http://www.codebind.com/linux-tutorials/install-scala-sbt-java-ubuntu-18-04-lts-linux/ 여기에 너무나도 잘 나와있다. 필자 역시 글을 쓰고는 있지만 사실 여기에 있는 것 그대로 한 것이다. 이 사이트는 Ubuntu 18.04를 기준으로 했기 때문에 Ubuntu 18.04에서 해도 전혀 무리가 없다.

$ sudo add-apt-repository ppa:webupd8team/java

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/1.JPG?raw=true" width="750px">

먼저 PPA를 추가해준다. PPA란 'Personal Package Archive'라고 해서 '개인 패키지 저장소'라는 뜻이고 좀 더 자세히 말하면, 런치패드(유명한 패키지 저장소 중 하나)에서 제공하는 Ubuntu의 공식 패키지 저장소에 없는 개인용 소프트웨어 패키지 저장소이다. 우리는 이 PPA 저장소에 SBT 프로그램을 설치할 것이다. 아래쪽을 보면 보안을 위한 GPG(GNU Privacy Guard)도 생성되었음을 확인할 수 있고, 맨 아래 OK까지 나온 것으로 보아 PPA가 잘 설치되었다.<br><br>$ sudo apt-get update -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/2.JPG?raw=true" width="750px">

업데이트를 해준다.<br><br>$ sudo apt-get install openjdk-8-jdk -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/3.JPG?raw=true" width="750px">

 http://www.codebind.com/linux-tutorials/install-scala-sbt-java-ubuntu-18-04-lts-linux 에서는 installer 설치하고 Oracle JDK8 설치하고 그랬는데, 필자는 오류가 떠서 그냥 이렇게 설치했다. 여튼 JDK8을 설치한다.<br><br>$ javac -version

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/4.JPG?raw=true" width="400px">

Java 컴파일러 버전을 확인해보면 잘 나오는 것을 확인할 수 있다.<br><br>\$ echo "export PATH=/usr/local/anaconda2/bin:\$PATH" >> /etc/bash.bashrc<br>\$ echo "export JAVA_HOME=/usr/lib/jvm/java-8-oracle/" >> /etc/bash.bashrc<br>\$ echo "export PATH=\$PATH:\$JAVA_HOME/bin" >> /etc/bash.bashrc

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/5.JPG?raw=true" width="750px">

그 다음 Java의 환경설정을 해주는 부분이다. 여기는 root로 해야한다. root가 아니면 권한 문제 때문에 안 된다(sudo로 될 수도 있지만 전 안됐습니다 ㅠㅠ). 여기까지가 Java를 설치하는 과정이고 이제는 Scala를 설치할 차례이다.<br><br>$ sudo apt-get remove scala-library scala -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/6.JPG?raw=true" width="600px">

이 부분은 기존에 설치되어 있는 Scala를 제거하는 과정이다. 아예 처음부터 설치되어 있지 않았다면 사실 필요없는 부분이지만 사이트에 있었으므로 그냥 짚고 넘어간다. 필자처럼 아예 설치되어 있지 않다면 Scala가 설치되어 있지 않아 제거하지 않았다고 메시지가 뜬다.<br><br>$ sudo wget www.scala-lang.org/files/archive/scala-2.11.8.deb

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/7.JPG?raw=true" width="750px">

해당 사이트에서 .deb 파일을 받는다. 이제 .deb 패키지를 리눅스에 설치해줘야 한다. <br><br>$ sudo dpkg -i scala-2.11.8.deb

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/8.JPG?raw=true" width="600px">

설치는 dpkg 명령어에 -i 옵션(install)을 주면 된다. dpkg란 데비안 패비지 관리 시스템의 기초가 되는 소프트웨어로 dpkg 명령어는 .deb 패키지의 설치, 삭제, 정보 제공을 위해 사용된다. 해당 패키지(Scala)가 잘 설치되었는지 확인하기 위해 버전을 확인해보면<br><br>$ scala -version

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/9.JPG?raw=true" width="600px">

잘 설치가 된 것을 확인할 수 있다. 이제 SBT 설치하는 일만 남았다.<br><br>\$ echo "deb <https://dl.bintray.com/sbt/debian> /" | sudo tee -a /etc/apt/sources.list.d/sbt.list<br>\$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823<br>\$ sudo apt-get update -y<br>\$ sudo apt-get install sbt -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/10.JPG?raw=true" width="750px">

이렇게 그대로 설치해주면 된다. 잘 설치가 되었는지 확인하기 위해 버전을 확인해보면<br><br>$ sbt -v

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/12.JPG?raw=true" width="600px">

잘 나오는 것을 확인할 수 있다.<br><br><br>**(2) CentOS**

: CentOS는 훨씬 간단하다. 저장소 만들어 준 다음에 바로 설치해주면 끝이다.

$ curl https://bintray.com/sbt/rpm/rpm | sudo tee /etc/yum.repos.d/bintray-sbt-rpm.repo

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/13.JPG?raw=true" width="750px">

tee 명령어는 화면 출력과 동시에 파일로도 출력을 해준다. 즉, 화면에 출력된 내용이 그대로 /etc/yum.repos.d/bintray-sbt-rpm.repo 파일로 들어가게 되는 것이다. 실제로 cat으로 확인해보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/14.JPG?raw=true" width="600px">

잘 들어가있음을 확인할 수 있다. 즉, SBT가 설치될 저장소가 구성된 것이다. 이제 SBT를 설치해주면 끝이다.<br><br>$ sudo yum update -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/15.JPG?raw=true" width="600px">

그 전에 먼저 업데이트를 해준다.<br><br>$ sudo yum install sbt -y

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/16.JPG?raw=true" width="750px">

이제 SBT를 설치해준다. 잘 설치되었는지 버전을 확인해보면<br><br>$ sbt -v

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/spark/17.JPG?raw=true" width="600px">

Ubuntu와 똑같이 잘 나온다.<br><br><br><br>참고 사이트 : 

- https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_tee,_%ED%99%94%EB%A9%B4%EA%B3%BC_%ED%8C%8C%EC%9D%BC%EC%97%90_%EB%8F%99%EC%8B%9C_%EC%B6%9C%EB%A0%A5%ED%95%98%EA%B8%B0
- http://www.codebind.com/linux-tutorials/install-scala-sbt-java-ubuntu-18-04-lts-linux/
- https://kogun82.tistory.com/163