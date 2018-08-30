---
layout: post
category: "others"
title: "[VMware] This virtual machine appears to be in use"
tags: ["others"]
---
배터리가 없는 것을 확인 못 하고 VM을 작업하고 있다가 배터기가 다 되어 컴퓨터가 꺼졌다. 다시 배터리를 연결하고 VM을 켰더니 다음과 같은 오류가 발생했다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/121.JPG?raw=true" width="750px">

This virtual machine appears to be in use. 즉, 가상머신이 이미 사용 중이라는 뜻이다. 이는 비정상적으로 Virtual Machine이 종료되었을 때 발생하는데 잘못하면 가상 이미지 파일을 손상시킬 수도 있다.

해결 방법은 간단하다. 메시지 중간 부분을 보면 press the "Cancel" button to avoid damaging it. 즉, 파일이 손상되는 것을 피하려면 Cancel 버튼을 누르라고 한다. 일단 메시지대로 Cancel을 누른 다음 에러 메시지의 맨 밑에 나와 있는 경로로 들어가서 현재 서비스되고 있는 Virtual Machine과 관련된 임시 파일이 있는 .lck 폴더를 삭제해주면 된다. 삭제하고 난 후 다시 해당 VM을 실행시켜주면 된다.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/122.JPG?raw=true" width="750px">

에러 메시지에 나와있는 경로에 .lck 파일이 있다. 또한 날짜와 시간을 보면 VM을 실행시켰을 때 해당 오류 메시지가 떴던 날짜와 시간이다. 이 파일을 지워준 후 다시 VM을 실행시키면

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/Others/123.JPG?raw=true" width="750px">

VM이 잘 실행된다.

<br><br><br>참고 사이트 :

- https://blog.jooylee.com/p/20180411807

