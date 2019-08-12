---
layout: post
category: "python"
title: "TypeError: slice indices must be integers or None or have an __index__ method"
tags: ["python"]
---

Python 코드를 돌리다가 이런 에러가 나왔다. 

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/60.JPG?raw=true" width="750px">

해석을 해보면 조각 인덱스(indices = index의 복수형)는 정수 또는 없음이거나 \__index__ 메서드가 있어야 한다는 뜻이다. 더 자세히 말하면 left_array = arr[:mid]를 진행하는데 mid값이 integers나 None 타입이 아니기 때문에 에러가 난 것이다. 에러가 난 부분의 코드를 보도록 하자.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/63.JPG?raw=true" width="300px">

코드는 이러하다. merged_sorted함수가 arr을 매개변수로 받고 mid 변수가 arr의 크기를 2로 나눈 값을 저장한다. 필자는 mid 변수에 몫을 저장하기 위해 저렇게 표현했던 것인데 파이썬에서는 몫을 구하기 위해 '/'를 사용하면 안 된다. 왜 안되는지 직접 확인해보자. <br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/61.JPG?raw=true" width="200px">

보통 다른 언어들은 몫을 구하고자 할 때 '/'를 사용하지만 Python에서 해봤더니 소숫점까지 모두 출력한다. 또한 type을 확인해보면 float형임을 알 수 있다. 필자의 코드와 연관시켜 보면 Python에서 arr[:2]는 arr 요소 중 맨 앞에 2개만 자르라는 뜻(arr[2:]는 arr 요소 중 마지막 2개만 자르라는 뜻)인데, 필자의 코드대로 한다면 mid = 3/2 = 1.5 -> arr[:1.5] -> arr 요소 중 맨 앞 1.5개만 자르라는 뜻이 된다. 이래서 아까 integers나 None 타입이 아니기 때문에(integers나 None 타입이어야 arr[:mid] 계산이 되기 때문에) 에러가 난 것이다. Python에서는 몫을 구하려면 '/'가 아니라 '//'를 사용해야 한다. <br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/62.JPG?raw=true" width="100px">

이렇게 몫만 잘 나온다. 코드에서 '/'를 '//'로 바꿔줬더니 에러가 해결됐다.<br><br><br><br>참고 사이트 : 

- https://stackoverflow.com/questions/28272322/typeerror-slice-indices-must-be-integers-or-none-or-have-an-index-method/28272778