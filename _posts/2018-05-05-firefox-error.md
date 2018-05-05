---
layout: post
category: "others"
title: "Mozilla Firefox Error : SEC_ERROR_OCSP_FUTURE_RESPONSE"
tags: ["others"]
---

VM에서 인터넷 브라우저를 켜야 할 상황이 생겼다. Ubuntu에 Mozilla Firefox가 있어 이거로 접속을 해봤는데, 되지 않았다. 그래서 특정 환경만 안 되는 건가 해서 네이버 사이트를 들어가 봤는데 역시 안 됐다. 그래서, 브라우저에 문제가 있다고 판단을 했고 에러를 잡아보기로 했다.

참고 : 환경은 VMware의 Ubuntu 16.04의 Mozilla Firefox이다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/firefoxerror/1.JPG?raw=true" width="750px">

일단 에러가 났으니까 Error code를 확인해봤더니 SEC_ERROR_OCSP_FUTURE_RESPONSE라고 나와 있다. 그래서 검색해봤더니 현재 시간과 맞지 않아서 나는 에러라고 한다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/firefoxerror/2.JPG?raw=true" width="px">

그래서 실제로 확인해봤더니 정말 현재 시간과 달랐다. 밑에 부분이 VM밖의 컴퓨터 시간(현재 시간)이고 위의 부분은 VM안의 컴퓨터 시간이다. 그럼 VM안의 컴퓨터 시간을 현재 시간을 맞춰주자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/firefoxerror/4.JPG?raw=true" width="750px">

설정그림 -> System Settings -> Time & Date 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/firefoxerror/3.JPG?raw=true" width="750px">

처음 설치할 때 Seoul 시간에 자동으로 맞춰지도록 해서 위 그림처럼 돼있었다. 그럼에도 불구하고 왜 이렇게 현재 날짜와 시간이 차이가 나게 됐는지는 모르겠다. 여튼 그래서 해당 시간으로 맞춰봤다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/firefoxerror/6.JPG?raw=true" width="750px">

그러고 나서 다시 네이버로 접속을 해보면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/firefoxerror/5.JPG?raw=true" width="750px">

이렇게 잘 접속이 됨을 확인할 수 있다.
