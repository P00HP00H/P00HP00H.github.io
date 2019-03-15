---
layout: post
category: "python"
title: "IndexError: list assignment index out of range"
tags: ["python"]
---

이번엔 간단한 Python 에러에 대해 포스팅하려고 한다. 필자가 코딩을 하다가 자주 낸 에러인데 자꾸 같은 실수를 반복해서 포스팅해본다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/14.JPG?raw=true" width="400px">

리스트를 생성할 때 저런 실수를 계속 반복했다. 머릿속에서는 리스트 공간을 만들고(array = []) for문을 통해 a[0] = 1, a[1] = 2, ..., a[4] = 5 이렇게 값이 들어가서 array=[1, 2, 3, 4, 5]가 만들어진다고 생각해 저렇게 코드를 짠 것이지만 저렇게 짜면 위에도 보이듯이 에러를 발생시킨다. 에러 메시지를 해석해보면 "리스트 할당 index 범위 초과"는 뜻인데 array = [], 즉, 현재 할당된 index 번호가 없는데(맨 밑의 len(array) = 0을 보고 알 수 있음) for문으로 index 번호에 값을 넣고 있기 때문에 저런 에러가 나온 것이다.<br>따라서, 이렇게 하면 안되고 insert 함수나, append 함수를 사용해서 값을 넣어줘야 한다.

<br><br>(1) **append 메서드**

: append는 "덧붙이다, 첨부하다"라는 뜻으로 append(n)는 리스트의 맨 마지막에 n를 추가시키는 메서드이다.<br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/15.JPG?raw=true" width="250px">

이렇게 append 메서드를 이용한 숫자가 맨 마지막에 추가되는 것을 확인할 수 있다. n에는 어떤 자료형도 다 들어갈 수 있다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/17.JPG?raw=true" width="250px">

이렇게 str도 들어갈 수 있고 list도 들어갈 수 있다.<br><br><br>

(2) **insert 메서드**

: insert(a, b)는 리스트의 a번째 위치에 b를 삽입하는 함수이다. 단, 리스트에서 첫번째가 0부터 시작한다는 것을 잊지 말아야 한다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/16.JPG?raw=true" width="250px">

array = [1, 2, 5]가 있다. insert(0, 6)을 해줬으므로 맨 앞자리에 6이 추가되어 array = [6, 1, 2, 5]가 됐음을 확인할 수 있다. 그 다음 insert(3, 7)이 있는데 4번째 자리(0, 1, 2, 3 -> 4번째)에 7이 추가된다는 뜻이다. 그래서 보면 array = [6, 1, 2, 7, 5]가 됐음을 확인할 수 있다. insert 메서드 역시 append 메서드처럼 b자리에 어떤 자료형도 다 들어갈 수 있다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/18.JPG?raw=true" width="300px">

이렇게 str도 들어갈 수 있고 list도 들어갈 수 있다.<br><br><br>참고 사이트 : 

- https://wikidocs.net/14
- https://www.codeit.kr/questions/1599
- https://techbard.tistory.com/2945