---
layout: post
category: "database"
title: "[Mysql] foreign key"
tags: ["database"]
---
<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/insert/1.JPG?raw=true" width="750px"><br>

ㅋㅋ

MYSQL 외래키(Foreign key) 지정(RESTRICT, CASCADE, NO ACTION, SET NULL)

**MYSQL 외래키(Foreign key) 지정 시** 

**on delete rule(삭제 시),** **on update rule(변경 시) 옵션 지정**

**RESTRICT, CASCADE, NO ACTION, SET NULL**

1. **RESTRICT** : 개체를 변경/삭제할 때 다른 개체가 변경/삭제할 개체를 참조하고 있을 경우 **변경/삭제가 취소**됩니다.(**제한**)

2. **CASCADE** : 개체를 변경/삭제할 때 다른 개체가 변경/삭제할 개체를 참조하고 있을 경우 **함께 변경/삭제**됩니다.

3. **NO ACTION** : 개체를 변경/삭제할 때 다른 개체가 변경/삭제할 개체를 참조하고 있을 경우 변경/삭제할 개체만 변경/삭제되고 **참조하고 있는 개체는 변동이 없습니다.**

4. **SET NULL** : 개체를 변경/삭제할 때 다른 개체가 변경/삭제할 개체를 참조하고 있을 경우 **참조하고 있는 값은 NULL로 세팅**됩니다.

 [이준빈은 호박머리]

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/insert/2.JPG?raw=true" width="750px"><br>

데이터가 잘 들어갔는지 확인해보자.

![각입력값](https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/insert/3.JPG?raw=true)<br>

잘 들어가 있다. 그런데, 이렇게 한 명 넣을 때마다 하나 하나 입력해주기가 너무 귀찮다. 이렇게 할 필요없이 한 번에 넣는 방법이 있다.

mysql> insert into 테이블(컬럼1, 컬럼2, 컬럼3) values('값1', '값2', '값3'), ('값4', '값5', '값6'), ('값7', '값8', '값9'), ('값10', '값11', '값12');

<img src="https://github.com/P00HP00H/P00HP00H.github.io/blob/master/img/insert/4.JPG?raw=true" width="750px"><br>

이렇게 넣고 싶은 값들을 쭉 나열해서 적어주면 된다.