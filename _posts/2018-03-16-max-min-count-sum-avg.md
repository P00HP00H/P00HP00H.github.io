---
layout: post
category: "database"
title: "[Mysql] 집계함수 (MAX, MIN, COUNT, SUM, AVG)"
tags: ["database"]
---


- <h3>MAX로 최대값, MIN으로 최소값 구하기</h3>
<br>

![MAX](https://raw.githubusercontent.com/P00HP00H/P00HP00H.github.io/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX.JPG)

member라는 테이블에 정보들이 나와 있고 각 사람들 중에 최대의 나이와 최소의 나이를 조회해보자.

mysql> select max(member_age), min(member_age) from member;

![MAX2](https://github.com/P00HP00H/P00HP00H.github.io/blob/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX2.JPG?raw=true)

뒤에 조건을 붙여서 할 수도 있다. 사람들 중에 이메일이 있는 사람 중에 최대의 나이와 최소의 나이를 조회해보자.

mysql> select max(member_age), min(member_age) from member where member_email is not null;

![MAX3](https://raw.githubusercontent.com/P00HP00H/P00HP00H.github.io/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX3.JPG)

<br><br><br>

- <h3>COUNT로 행의 개수 구하기</h3>
<br>

![MAX](https://raw.githubusercontent.com/P00HP00H/P00HP00H.github.io/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX.JPG)

똑같이 member 테이블이 있다. 이 그림만 보고도 대부분의 컬럼을 카운트했을 때 5개일 것이라고 생각할 수 있다. member_age를 카운트해보자. 

mysql> select count(member_age) from member;

![MAX10](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/MAX10.JPG?raw=true)

하지만 주의해야 될 것이 있다. COUNT함수는 NULL값을 세지 않는다.

mysql> select count(member_email) from member;

![MAX4](https://raw.githubusercontent.com/P00HP00H/P00HP00H.github.io/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX4.JPG)

NULL을 2개 포함하면 5개라고 생각할 수 있지만 COUNT함수를 사용하면 NULL값 2개를 세지 않았음을 확인할 수 있다. 그리고 member_phone을 보게 되면 010-1111-2222이 2개 있는데, 이를 중복되지 않게 세려면 distinct를 써주면 된다.

mysql> select count(distinct member_phone) from member;

![MAX11](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/MAX11.JPG?raw=true)

 그러면 전체를 반환하는 *를 사용했을때는 어떻게 되는지 살펴보자.

 mysql> select count(*) from member;

![MAX5](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/MAX5.JPG?raw=true)

member_email에 NULL값이 있어 어떻게 나오는지 궁금했는데 다른 컬럼들에는 값들이 다 존재하므로 *를 사용했을 때는 모두 카운트한 값인 5가 반환이 됨을 확인할 수 있었다.
<br><br><br>

- <h3>SUM으로 합계 구하기</h3>
<br>

![MAX](https://raw.githubusercontent.com/P00HP00H/P00HP00H.github.io/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX.JPG)

이번엔 각 사람들의 나이의 합을 구해보자

mysql> select sum(member_age) from member;

![MAX6](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/MAX6.JPG?raw=true)

이거 역시 조건을 붙여서 사용할 수도 있다. 이번엔 이메일이 없는 사람들의 나이 합을 구해보자

mysql> select sum(member_age) from member where member_email is null;

![MAX7](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/MAX7.JPG?raw=true)
<br><br><br>
- <h3>AVG로 평균 구하기</h3>
<br>

![MAX](https://raw.githubusercontent.com/P00HP00H/P00HP00H.github.io/e61bf18c50b7d8229f11a97372e19092dcb2e629/img/MAX.JPG)

이번엔 각 사람들의 나이의 평균을 구해보자. 평균을 구하는 방법은 2가지가 있다.

1. AVG 함수 사용
2.  SUM함수 / COUNT함수

mysql> select avg(member_age), sum(member_age)/count(member_age) from member;

![MAX8](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/MAX8.JPG?raw=true)

똑같은 값이 나옴을 확인할 수 있다. AVG함수 역시 뒤에 조건을 붙여서 사용이 가능하다.


