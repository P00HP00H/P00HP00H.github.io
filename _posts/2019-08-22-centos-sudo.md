---
layout: post
category: "linux"
title: "[CentOS] sudo 명령어 사용하기"
tags: ["linux"]
---

이번 포스팅에서는 sudo 명령어에 대해서 포스팅하려고 한다. sudo란 'superuser do'라고 해서 쉽게 말해, '최고 관리자(root)가 내리는 명령'이라고 보면 된다. 특히 이 sudo 명령어는 권한 문제로 인해 많이 사용하고 데비안, 레드헷 계열의 리눅스에서 모두 사용할 수 있는데, 이번 포스팅에서 레드헷 계열인 CentOS만 하는 이유는 Ubuntu는 root 비밀번호만 알면 일반 계정에서 sudo를 바로 이용할 수 있지만 CentOS의 경우는 일반 계정에서 root 비밀번호를 알고 있어도 sudo를 바로 이용할 수 없기 때문이다. 검색해보니 약간의 설정을 해줘야 했다. 그래서, 이번 기회에 공부할 겸 포스팅해보려고 한다. 실습 환경은 VM CentOS 7이다.<br><br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/158.JPG?raw=true" width="600px">

이렇게 root 비밀번호를 잘 입력해도 'pooh is not in the sudoers file.'이라고 나온다. 해석해보면, sudoers 파일에 'pooh'가 없다는 뜻이다. 즉, sudo를 이용하려면 sudoers 파일에 pooh라는 이름을 넣어야 함을 알 수 있다. sudoers 파일은 /etc에 있는데, root 권한이므로 root로 접속한 후 sudoers 파일에 대한 설정을 해줘야 한다.<br><br>$ visudo -f /etc/sudoers

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/160.JPG?raw=true" width="750px">

여기서 root 밑에 아래처럼 해당 계정을 추가해주면 끝이다. 그런데, 필자는 꼭 visudo를 써야만 하는지 의문이 들었다. 어차피 root이니까 다 되지않을까해서 vim으로 한 번 해봤더니<br><br>$ vim /etc/sudoers

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/159.JPG?raw=true" width="750px">

아니나 다를까 readonly, 즉, 읽기만 가능하다고 나온다. 이렇게 root 계정이지만 기존 vi(m) 편집기로는 읽기 전용 파일이라고 수정 못하게 막아놓았기 때문에 visudo로 편집(수정)해야 한다.<br><br>이렇게 하고 다시 원래 계정으로 해보면 이제 sudo 명령어가 동작하게 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/161.JPG?raw=true" width="750px">

아까와는 달리 sudo 명령어가 잘 동작한다.<br><br><br><br>참고 사이트 : 

- https://jhyecheol.tistory.com/422
- https://shaeod.tistory.com/922