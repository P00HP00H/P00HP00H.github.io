---
layout: post
category: "others"
title: "[Ubuntu] chacha20-poly1305@openssh.com~~"
tags: ["others"]
---
해당 서버에서 작업을 하다가 코드가 길어져서 툴을 이용해 원격으로 작업을 하려고 했다. 좋은 툴들이 많지만 설치하기가 귀찮아 달랑 하나 있는 Edit plus로 서버에 붙으려고 했더니 이러한 에러가 났다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/124.JPG?raw=true" width="750px">

이 에러는 Edit plus 암호화 알고리즘이 일치하지 않는다는 에러로 검색해보니 ssh 버전이 높으면 나타나는 것이라고 한다. 이런 경우는 서버에서 설정을 해줘야 한다. Ubuntu에서는 /etc/ssh/sshd_config에다가 밑의 내용을 추가해주면 된다.

vim /etc/ssh/sshd_config

Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,blowfish-cbc,aes128-cbc,3des-cbc,cast128-cbc,arcfour,aes192-cbc,aes256-cbc

KexAlgorithms diffie-hellman-group1-sha1,diffie-hellman-group-exchange-sha1

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/125.JPG?raw=true" width="px">

그 다음에 ssh를 restart해준다.

service sshd restart

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/126.JPG?raw=true" width="px">

그러고 나서 다시 Edit plus로 서버에 접속해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/127.JPG?raw=true" width="750px">

이런 화면이 나오면 성공한 것이다(서버IP같은 것은 가렸습니다). '예' 버튼을 누르면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/128.JPG?raw=true" width="750px">

이렇게 서버에 있는 코드들이 나오게 된다.

<br><br><br>참고 사이트 :

- http://rootjs.tistory.com/27
- https://howcode.co.kr/bbs/board.php?bo_table=adm_server&wr_id=725

