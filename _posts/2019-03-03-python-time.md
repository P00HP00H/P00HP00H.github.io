---
layout: post
category: "python"
title: "날짜 및 시간 다루기"
tags: ["python"]
---

Python을 쓰다보니 날짜나 시간을 나타내야 하는 경우가 상당히 많았고 10초 전, 20분 뒤, 3시간 뒤 등 다양하게 시간을 조작해야 했다. 물론 검색하면 너무나도 많이 나오지만 이 기회에 나도 간단하게나마 정리해보고자 한다.<br><br>방법은 간단하다. Python에서 제공하는 기본 모듈인 datetime을 이용해주면 된다. 그래서, 사용할 때 반드시 import datetime을 해줘야 한다.

```
import datetime

datetime.datetime.today()  or  datetime.datetime.now()	# 현재 날짜 가져오기
datetime.datetime.today().year		# 현재 연도 가져오기
datetime.datetime.today().month		# 현재 월 가져오기
datetime.datetime.today().day		# 현재 일 가져오기
datetime.datetime.today().hour		# 현재 시간 가져오기
datetime.datetime.today().weekday() # 오늘 날짜를 숫자로 표현하기(월~일 : 0~6) 
```

이런 식으로 전체 날짜 및 시간을 뽑아낼 수도 있지만 해당년도나 월/일/시간 모두 각각으로 뽑아낼 수 있다.<br><br>

```
import datetime

now = datetime.datetime.today()  or  datetime.datetime.now()	# 현재 날짜 가져오기

one_minutes_ago = now - datetime.timedelta(minutes=1)		# 1분 전
one_minutes_later = now + datetime.timedelta(minutes=1)		# 1분 후

one_hour_ago = now - datetime.timedelta(hours=1)		# 1시간 전
one_hour_later = now + datetime.timedelta(hours=1)		# 1시간 후

yesterday = now - datetime.timedelta(days=1)		# 어제
tomorrow = now + datetime.timedelta(days=1)			# 내일

tomorrow_one_hour_later = now + datetime.timedelta(days=1, hours=1)		# 다음날 1시간 후
```

이렇게 datetime.timedelta로 시간을 증가 / 감소시킬 수 있다. 또한 맨 아래처럼 여러 개를 동시에 사용할 수도 있다.<br><br><br>이번엔 strftime, strptime에 대해 알아보자.

- **strftime 메서드**

  : 현재의 날짜와 시간을 문자열로 출력해주는 메서드이다. datetime.datetime.today() or datetime.datetime.now() 모두 초 단위까지 출력이 되는데, strftime 메서드를 이용해 원하는 범위만큼 자유롭게 출력이 가능하다(예를 들어, 시간 빼고 날짜만 출력한다던지, 날짜 빼고 시간만 출력한다던지 등).<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/5.JPG?raw=true" width="600px">

여기를 보면 datetime.datetime.today()는 datetime 형식(쉽게 말해 시간 자료형)이다(type(now)). 이렇게 시간 형식을 strftime 메서드를 이용해  문자열로 출력할 수 있다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/6.JPG?raw=true" width="400px">

strftime 메서드를 통해 이렇게 원하는 형태만 뽑아내는 게 가능하고 type(변수)에서도 알 수 있듯이 자료형은 변수 now와 다르게 모두 str인 것을 확인할 수 있다. strftime은 특히 다른 프로그램과 연결할 때 종종 쓰인다. 무슨 말이냐면 예를 들어, DB에 있는 날짜 데이터를 불러와서 비교해야 할 때 DB에서 불러온 날짜 데이터(데이터 형식이 list일수도 있고 되게 다양함)를 str으로 만들고 현재시간을 strftime 메서드를 통해 문자열로 만들어서 비교해주면 된다는 뜻이다. "if str(DB에서 불러온 날짜 데이터) == datetime.datetime.now().strftime('%Y-%m-%d'):" 이런 식으로 말이다. Python에서 실제 시간(datetime.datetime.today() --> datetime 형식)과 DB에서 불러온 str 데이터는 자료형이 달라서 비교 자체를 할 수가 없다. 코딩을 하다보면 이렇게 자료형을 자유자재로 바꿔 서로 같게 만들어줘야 할 때가 많다.<br><br>

- **strptime 메서드**

  : strptime 메서드는 strftime 메서드와 반대라고 생각하면 된다. 문자열로 된 날짜 / 시간 을 datetime 형식으로 바꿔주는 메서드이다. <br><br>



<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/7.JPG?raw=true" width="600px">

아래쪽에도 보이듯이 time은 str형식, realtime은 datetime 형식이다. 또한, strptime 메서드의 경우는 문자열과 단위가 일치해야 한다. 문자열(변수 time)이 "년/월/일 시/분/초"가 다 있으므로 똑같이 "%Y-%m-%d %H:%M:%S" 라고 해줘야 한다는 것이다. 만약 다르게 하면 오류가 난다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/8.JPG?raw=true" width="600px">

"년/월/일 시/분/초"(변수 time)를 넣었는데 "년/월/일 시/분"(%Y-%m-%d %H:%M)까지만 변환이 돼서 unconverted data remains, 즉, 변환되지 않은 데이터가 남아있다는 에러 메시지를 낸 것이다. 그러면 날짜만 뽑아서 datetime 형식으로 만들어 날짜만 비교하는 코드를 짤 수는 없는 걸까? 아니다. 이렇게 해주면 된다.<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/9.JPG?raw=true" width="600px">

strftime으로 원하는 날짜 및 시간만 뽑아낸 후 이를 다시 strptime으로 바꾸어 시간 연산이 가능하게끔 해주는 것이다. strptime 메서드로 변환된 realdate을 출력해보면 날짜만 뽑았음에도 불구하고 00:00:00 이렇게 시/분/초가 나오는데 어차피 날짜끼리만 비교한다고 했을 때 다 저렇게 00:00:00이 붙어서 시간은 똑같으므로 의미가 없어져 날짜끼리만 비교하는게 되는 것이다. <br>물론 이 예제만 보면 변수 time에 값을 넣을 때 처음부터 날짜만 넣으면 되지 뭐하러 strptime으로 변환했다가 이를 다시 strftime을 이용해서 날짜만 자르는지 이해하지 못할 것이다. 하지만 위 상황은 우리가 입력하는 값이 아닌 우리가 수정하지 못하는 어떤 받아오는 값일 때를 가정한 것이다(예를 들어 DB에 저장된 값). 다른 시스템에서 값 받아오는 예제 만들기가 귀찮아서 그냥 time 변수 만들고 어떤 시스템에서 받아온 값이라고 가정했다는 말이다. 이렇게 받아오는 값 중에 내가 원하는 부분만 비교연산할 때 사용할 수 있다는 뜻이다.<br>대충 이런 식으로 쓰인다. 

```
# DB 코드는 생략(DB의 데이터 : 날짜 및 시간) -> s_query_date는 DB 쿼리로 인해 출력된 값이 저장
time = datetime.datetime.now().strftime('%Y-%m-%d %H:%M')
db_time_data = datetime.datetime.strptime(str(s_query_data), '%Y-%m-%d %H:%M')
        db_one_hour_later = db_time_data + datetime.timedelta(hours=1)
        now_time = datetime.datetime.strptime(str(time), '%Y-%m-%d %H:%M')
        if now_time > db_one_hour_later:
            print("1시간이 지난 후에는 실행할 수 없습니다")

```

이에 대한 설명을 해보자면, 변수 time은 해당 DB의 데이터("년/월/일 시/분" 형태라고 가정)와 비교하기 위해 형식을 맞춰준 것이다(%Y-%m-%d %H:%M). 형식이 맞지 않으면 비교를 할 수 없기 때문이다. s_query_date는 DB로 부터 불러온 데이터이다. strptime이 문자열을 datetime 형식으로 바꿔주는 메서드이므로 str형식을 붙여준 것이고(어떤 시스템에서 불러온 데이터가 str형식이라면 저렇게 str()을 붙여줄 필요가 없지만 아닌 경우에는 꼭 str()을 붙여줘야함 -> 하지만 받아온 값이 어떤 자료형이던 str()을 붙여버리면 무조건 자료형이 str이고 코드 볼때도 바로 자료형이 인식되기 때문에 어떤 자료형이 오던간에 그냥 붙여주는 것도 나쁘지 않음)  strptime 메서드에 의해 db_time_data는 datetime 형식이 됐다. 따라서, 저렇게 datetime.timedelta로 시간 연산이 가능하다. now_time은 time이 str형식이므로 db_one_hour_later와 시간 연산이 가능하도록 strptime 메서드를 사용한 것이다. 이렇게 해주면 이런 식으로 비교연산이 가능하게 된다.<br><br><br>

이제 마지막으로 replace 메서드와 combine 메서드에 대해 알아보자.

- **replace 메서드**

  : replace 메서드는 날짜나 시간을 변경해주는 메서드이다. 바로 예시를 보자.



<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/10.JPG?raw=true" width="500px">

replace로 메서드로 각각으로도 날짜 및 시간을 변경할 수 있고, 맨 아래처럼 한 번에 해줄 수도 있다.<br><br>

- **combine 메서드**

  : combine 메서드는 날짜와 시간을 합쳐주는 메서드이다. 날짜만을 관리하기 위해서는 **datetime.date**, 시간만을 관리하기 위해서는 **datetime.time**을 이용하면 되는데, 이 둘을 합쳐 날짜와 시간을 표시해주는 메서드가 combine 메서드이다. 2개의 예제를 보도록 하겠다.<br><br>


ex1)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/11.JPG?raw=true" width="600px">

현재 시간(변수 today)을 출력해보면 "날짜 + 시간" 형태로 나오게 된다.  date()와 time()으로 날짜와 시간만을 각각 뽑아냈고 이를 combine 메서드로 합친 예제이다. 여기서 주의할 것은 다음 예제에도 나오지만 combine 메서드의 인자값은 둘 다 datetime 형식이어야 한다. 좀 더 정확히는 첫 번째 인자로 datetime.date을, 두 번째 인자로는 datetime.time을 받는다. 둘 다 datetime 형식이라고 서로 인자값을 바꿔서 combine 메서드에 넣어보면<br><br>

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/12.JPG?raw=true" width="600px">

이렇게 에러가 뜬다. 에러 내용도 첫 번째 인자값으로는 datetime.time이 아닌 datetime.date여야 한다고 나와 있다. 여튼 제대로 combine 메서드를 사용하면 합쳐진 결과값은 datetime.datetime 형식이 된다.

물론 이 예제 역시 datetime.datetime.today() 자체가 "날짜 + 시간" 형식인데 뭐하러 날짜만 뽑고 시간만 뽑아서 다시 합치나 생각할 수도 있지만 combine 메서드의 기능을 보여주기 위해서 했다고 생각하면 될 것 같다.<br><br>

ex2)

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/python/13.JPG?raw=true" width="500px">

datetime.date를 이용해서 날짜를, datetime.time을 이용해서 시간을 만든 후 이를 combine 메서드를 이용해 합쳐 datetime.datetime 형식으로 합친 예제이다.<br><br>지금까지 Python으로 시간 및 날짜를 다루는 법에 대해 알아보았다. 이 외에 또 자주 쓰는 것들이 있다면 추후에 포스팅하겠다.<br><br><br><br>참고 사이트 : 

- https://datascienceschool.net/view-notebook/465066ac92ef4da3b0aba32f76d9750a/
- https://ourcstory.tistory.com/99
- https://yujuwon.tistory.com/entry/%ED%98%84%EC%9E%AC-%EB%82%A0%EC%A7%9C-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0
- https://godoftyping.wordpress.com/2015/04/19/python-%EB%82%A0%EC%A7%9C-%EC%8B%9C%EA%B0%84%EA%B4%80%EB%A0%A8-%EB%AA%A8%EB%93%88/
- https://minus31.github.io/2018/07/28/python-date/