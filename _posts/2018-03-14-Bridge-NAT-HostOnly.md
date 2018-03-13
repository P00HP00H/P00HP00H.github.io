---
layout: post
category: "android"
title:  "Android开发环境搭建 "
tags: [Android]
---
VMware의 Bridge, NAT, Host-Only 방식에 대해 알아보자.
<br><br>
###1. Bridge 방식

![Bridge](https://github.com/P00HP00H/P00HP00H.github.io/blob/4f88b77a75c27f99b7b478f210814a31c7f77347/img/Bridge.JPG?raw=true)

Bridge 방식에서는 VMware 에서 Virtual Adapter로 vmnet0 을 사용하고, 공유기가 VM도 별개의 PC로 보기 때문에 개별적으로 IP를 할당 한다. 즉 VM은 PC가 물리적 네트워크에서 동작하는 것처럼 동작한다. 그래서, PC 1, PC 2, PC 3 모두 VM에 접근할 수가 있다(통신이 가능하다).
<br><br><br><br><br><br>
###2. NAT 방식

![NAT](https://github.com/P00HP00H/P00HP00H.github.io/blob/4f88b77a75c27f99b7b478f210814a31c7f77347/img/NAT.JPG?raw=true)

NAT 방식에서는 VMware 에서 Virtual Adapter로 vmnet8 을 사용하고, 공유기가 각 PC에게 IP를준다. 그리고 해당 PC(PC 1)가 VM에게 IP를 주면서 VMware 가 자체적으로 내부 네트워크대역 할당 및 자체 DHCP 서버를 띄워 통신한다. 

따라서, PC 1과 VM끼리통신이 가능하고 PC 1이 인터넷이 되면 자동으로 VM도인터넷이 가능하다. 하지만, PC 2와 PC 3는 VM에게 접근할 수 없다.만약 접근하고자 한다면, NAT 설정을 통해 포트 포워딩 함으로써 특정 PC에서 VM들에접근할 수 있다.
<br><br><br><br><br><br>
###3. Host-Only 방식

![Host-Only](https://github.com/P00HP00H/P00HP00H.github.io/blob/4f88b77a75c27f99b7b478f210814a31c7f77347/img/Host-Only.JPG?raw=true)

Host-Only 방식에서는 VMware 안에서 구성된 VM들끼리만 통신한다. 즉 VM은 해당PC (호스트
컴퓨터(PC1))를 넘어서 통신할 수 없다. 따라서, PC 1이 인터넷에 연결되어 있다고 해도 VMware는 인터넷이 되지 않는다. 





,

