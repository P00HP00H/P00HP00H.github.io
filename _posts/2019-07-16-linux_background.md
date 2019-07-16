---
layout: post
category: "linux"
title: "리눅스에서 백그라운드로 실행하기"
tags: ["linux"]
---

이번 포스팅에서는 리눅스의  백그라운드 실행에 관해 알아보고자 한다. 어려운 내용은 아니지만 나중에 사용할 수도 있을 것 같아서 간단하게 정리해본다.<br><br><br>**(1) Ctrl + Z**

: 프로그램 실행 중 CTRL+Z를 통하여 현재 프로그램을 백그라운드에서 실행되도록 한다. 정확히는 실행된다기보다는 중지시켜놓고 백그라운드로 넘긴다는 표현이 맞다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/130.JPG?raw=true" width="300px">

현재 vim 프로그램을 실행하고 있다. 이를 종료하지 않고 백그라운드로 넘기려면 Ctrl + Z키를 누르면 된다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/131.JPG?raw=true" width="500px">

Ctrl + Z키를 누르니까 Stopped 라는 메시지와 함께 vim에서 나와졌다. 백그라운드로 간 것이지 vim이 종료된 것은 아니다. 백그라운드(background)에 있는 것을 포그라운드(foreground)로 가져오려면 fg 명령어를 입력하면 된다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/132.JPG?raw=true" width="300px">

이렇게 fg 명령어를 입력하면 백그라운드에서 실행중이었던 vim kkk.txt를 포그라운드에서 실행한다. 그럼 여러 실행 프로그램들이 있는 경우는 어떨까? 직접 해보자.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/133.JPG?raw=true" width="500px">

jobs 명령어를 통해 현재 백그라운드에서 실행되고 있는 프로그램들을 볼 수 있다. 왼쪽을 보면 각각의 번호가 매겨져 있고 맨 밑이 '+', 그 위에는 '-' 로 되어 있다. fg 명령어로 포그라운드로 가져올 때 +로 되어 있는 것부터 가져온다. 즉, 가장 최근에 실행된 백그라운드 프로그램을 가져오는 것이다. '-' 는 '+'의 다음 순서로 실행된다는 뜻이다. 주의할 점은 '+'([4])가 fg 명령어에 의해 포그라운드로 실행됐다가 Ctrl + Z키에 의해 다시 백그라운드로 넘어가면 결국 또 가장 최근에 실행된 것이기 때문에 fg를 하면 그 다음 차례인 [3]이 아니라 계속 [4]가 불러와 진다는 것이다. 따라서, [4]가 종료되어야만 '-' 였던 [3]이 '+'로 되고 그 위에 있는 [2]가 현재는 아무 표시가 안되어 있지만 '-'로 바뀐다. 이런 식으로 계속 진행된다. 이렇게 순서대로 할 수도 있고 순서와 관계없이 원하는 백그라운드 실행 프로그램을 포그라운드로 가져올 수도 있다. "fg %[stopped 앞의 대괄호 숫자]" 로 써주면 된다. 주의해야 할 것은 "fg % [숫자]"가 아니라 "fg %[숫자]"라는 것이다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/147.JPG?raw=true" width="400px">

보면 필자는 [2]인 vim kkk2.txt를 포그라운드로 가져오고 싶다. 그래서 "fg % 2"를 입력했더니 가장 최근(마지막)에 실행된 vim kkk6.txt를 포그라운드로 가져온다. "fg %2"라고 해줘야 vim kkk2.txt를 가져온다.<br><br>지금까지는 Ctrl + Z로 중지시켜놓고 포그라운드로 가져와서 실행했는데 중지한 상태에서 포그라운드로 가져오지 않고 백그라운드에서 실행하는 방법은 없을까? 아니다. 있다. fg 대신에 bg라고 하면 된다. 위의 예제들은 vim으로 구성해서 bg로 해도 어차피 Stopped 되기 때문에 밑에 &을 설명하면서 bg 명령어에 대해 다시 설명하겠다.<br><br><br>**(2) &**

: 프로그램 실행 시 끝에 &를 붙여 백그라운드로 실행시킬 수 있다. 앞서 살펴본 (1) Ctrl + Z 과 다른 점은 Ctrl + Z는 해당 작업을 중지시켜놓고 백그라운드로 보내는 반면 이 &은 중지시키지 않고 백그라운드로 실행한다는 것이다. 간단한 예제를 통해 차이점을 명확히 해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/151.JPG?raw=true" width="400px">

그냥 실행시키면 0.5초마다 1부터 1씩 증가된 수를 더하는 즉, 1~100까지를 더한 총합을 kkk.txt에 저장해주는 코드이다. 이런 예제를 하는 이유는 머신러닝 학습을 시킨다던지 시간을 측정하는 프로그램을 돌릴 때의 상황을 가정하기 위함이다. 만약 백그라운드에서 실행을 하지 않고 포그라운드에서 실행하면 내 컴퓨터가 서버와 계속 연결되어 있어야 하고 또한 다른 작업을 하려면 해당 ssh 창에서는 작업이 진행중이기 때문에 다른 ssh창을 켜거나 작업이 끝날 때까지 기다려야 한다. 이런 상황에 백그라운드로 작업을 진행하면 굳이 또 다른 ssh창을 켤 필요 없이 해당 ssh창에서 다른 작업을 진행할 수도 있고 장시간 자리를 비워야 하는 경우 백그라운드로 돌려놓고 내 컴퓨터를 종료시켜도 된다. 어차피 작업이 다 끝나면 kkk.txt파일에 결과값이 저장되기 때문이다. <br><br>다시 돌아와서 먼저 결과값을 저장할 kkk.txt 파일을 만들고 예제 파일을 실행한다.

$ touch kkk.txt

$ python test3.py > kkk.txt

다음 Ctrl + Z를 해보자.

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/152.JPG?raw=true" width="500px">

Ctrl + Z를 한 후 jobs명령을 통해 현재 백그라운드에서 동작하고 있는 프로그램을 확인해보면 Stopped 되어 있는 것을 확인할 수 있다. 즉, 현재 중지 상태인 것이다. 반면 &으로 실행해보면<br><br>$ python test3.py > kkk.txt &

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/153.JPG?raw=true" width="600px">

아까와는 달리 Stopped가 아닌 Running이라고 표시되어 있다. 즉, 중지되지 않고 계속 실행중인 것이다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/154.JPG?raw=true" width="500px">

다 끝나면 이렇게 Done이라고 표시되고 kkk.txt에 결과값이 잘 저장되어 있는지 확인해보면 값이 잘 들어가있다.<br><br>아까 (1) Ctrl + Z할 때 잠깐 언급했었는데 Ctrl + Z로 중지시켜놓고 bg 명령어로 중지된 것을 백그라운드에서 실행시킬 수 있다고 했다. bg 명령어를 실행해보자. 사용법은 fg와 동일하다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/155.JPG?raw=true" width="500px">

bg를 실행하니까 &으로 실행되는 것을 확인할 수 있다. 작업이 다 끝나면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/154.JPG?raw=true" width="550px">

아까 &으로 실행했을 때와 동일하다.<br><br><br>※ 참고 : 출력문이 나오는 프로그램을 백그라운드로 실행시키면 어떻게 될까(예를 들어, update 실행)? 백그라운드로 넘어갔기 때문에 백그라운드에서 출력되어 포그라운드에는 아무것도 나타나지 않을까? 아니다. 백그라운드에서 실행은 되지만 포그라운드로 출력된다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/148.JPG?raw=true" width="400px">

0.5초마다 0 ~ 99까지 출력해주고 99까지 출력하면 그에 대한 총합까지 출력해주는 코드이다. 이를 &으로 백그라운드에서 실행해보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/150.JPG?raw=true" width="400px">

이렇게 포그라운드에서 실행하는 것처럼 숫자들이 출력된다. 포그라운드에서 보여지기 때문에 포그라운드에서 실행되는 것으로 착각할 수가 있지만 백그라운드에서 실행되는 것이 맞다. 2번째 줄에 [1] 375라는 메시지가 그 증거다. 이렇게 되면 실행은 백그라운드로 되지만 포그라운드에서 출력이 계속해서 이루어지기 때문에 어차피 다른 작업을 할 수가 없다. 이러한 경우 아까처럼 파일 하나를 만들어놓고 그 파일로 리다이렉션시키면 백그라운드에서 실행하는 효과를 얻을 수 있다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/156.JPG?raw=true" width="500px">

해당 출력문이 kkk.txt로 리다이렉션됐기 때문에 화면(포그라운드)에 출력되지 않고 kkk.txt라는 파일에 출력된다. 해당 작업이 Done되고 나서 cat으로 kkk.txt를 확인해보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/157.JPG?raw=true" width="70px">

이렇게 모든 출력문이 나오는 것을 확인할 수 있다.<br><br><br>**(3) kill**

백그라운드에서 실행 중인 프로그램을 정상적으로 종료할 수도 있지만 굳이 종료될 때까지 기다릴 필요가 없는 경우라면 강제로 종료시킬 수가 있다. 'kill -9 %[숫자]' 명령어를 사용하면 된다.

$ kill -9 %4

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/linux/134.JPG?raw=true" width="450px">

이렇게 [4]를 제거했다. kill만 입력하면 Stopped만 나오고 엔터키를 한 번 더 누르면 Killed라는 메시지가 나온다. jobs를 통해 보면 [4]가 제거되고 [3]이 '+'로 바뀌었으며 [2]가 '-'로 바뀌었다. 이제 fg를 누르면 [3]이 실행될 것이다. 다른 명령이 딱히 안 떠올라서 예시를 모두 vim으로 했지만 다른 여러 실행 프로그램이라고 생각하기 바란다.<br><br><br>

참고 사이트 : 

- https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%B0%B1%EA%B7%B8%EB%9D%BC%EC%9A%B4%EB%93%9C_%EC%8B%A4%ED%96%89
- https://pragp.tistory.com/entry/Linux%EB%A6%AC%EB%88%85%EC%8A%A4-fg-bg-kill-CtrlZ