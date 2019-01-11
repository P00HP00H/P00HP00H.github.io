---
layout: post
category: "git"
title:  "소스트리(Source Tree)를 저장소(repository)와 연결하기"
tags: [git]
---

이번 포스팅에서는 소스트리(Source Tree)에 대해서 알아보고자 한다. 소스트리가 무엇인지부터 알아보자.
<br><br><br>
- 소스트리(Source Tree)란:

  : Atlassian에서 개발한 git을 GUI로 사용자가 더 쉽게 사용할 수 있도록 해주는 프로그램이다. 이 소스트리는 Windows와 Mac OS를 지원하고 있으며 라이센스는 무료이다.



설치는 https://www.sourcetreeapp.com/에서 할 수 있다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/27.JPG?raw=true" width="750px">

필자도 Windows를 사용하기 때문에 Windows용을 다운받았다. 설치방법은 검색하면 많이 나오기 때문에 생략하고 설치됐다고 가정하고 진행하겠다.

다 설치가 됐으면 이렇게 화면이 나와야 한다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/28.JPG?raw=true" width="750px">

저번 포스팅에서 로컬 저장소(repository)와 git 저장소를 서로 연결시켜 관리했는데 이 소스트리로 로컬 저장소와 연결시켜 git 저장소를 관리해준다고 생각하면 된다. 그럼 git 저장소를 소스트리로 관리해보자. 현재 필자의 git 저장소는 이러하다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/29.JPG?raw=true" width="750px">

필자는 여기서 backjoon_algorithm 저장소를 소스트리로 관리할 예정이다. 그럼 먼저 로컬 저장소와 git 저장소를 서로 연결시켜주자. git 저장소를 연결시킬 로컬 저장소에서 Git Bash를 연 다음

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/31.JPG?raw=true" width="750px">

이대로 해주면 로컬 저장소에 git 저장소의 파일들이 나타나게 된다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/32.JPG?raw=true" width="750px">

각 명령어에 대한 설명은 저번 포스팅에 다 있으므로 생략하겠다.

(참고 : git 저장소 URL은 'Clone or download'을 누르면 확인할 수 있다.)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/30.JPG?raw=true" width="750px">





다시 돌아와서, 이제는 소스트리를 로컬 저장소와 연결시켜보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/33.JPG?raw=true" width="750px">

여기서 Add 부분을 클릭한다.



<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/34.JPG?raw=true" width="750px">

그 다음 '탐색' 부분을 눌러 해당 로컬 저장소를 지정해주고 '추가' 버튼을 클릭하면



<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/git/35.JPG?raw=true" width="750px">

이렇게 과거부터 현재까지 했던 작업들이 쭉 나오게 된다. 이러면 성공한 것이다. 다음 포스팅에서는 작업을 한 후 해당 내용을 commit, push 해보자.<br><br><br><br>

참고 블로그 : 

- http://donbada.tistory.com/73
