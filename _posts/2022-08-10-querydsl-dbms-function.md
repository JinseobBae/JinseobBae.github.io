---
layout: post
title: "[QueryDsl] QueryDsl에서 DBMS 내장함수 사용하기(to_char 등)"
categories: [querydsl]
tags: [QueryDsl, dbms-function]
---

## Template

쿼리를 만들 때 가끔 DBMS에서 제공하는 내장함수를 이용해야 할 때가 있다.
(to_char, to_date 등등)

QueryDsl도 객체기반이다보니 내장함수를 method 호출해서 사용하는 방법은 없다.

그런 부분을 해결하기위해 **Template** 기능을 제공한다.

Template에 사용 할 함수를 직접 입력하고 객체값을 바인딩해서 쿼리에 함수를 추가시킨다.

Template는 반환 타입에 따라 몇가지로 나뉜다. **(5.0.0 기준)**

- StringTemplate
- NumberTemplate
- BooleanTemplate
- DateTemplate
- EnumTemplate
- ComparableTemplate

StringTemplate을 제외한 다른 template들은 인자값으로 반환 할 타입을 명시해줘야한다.

```java

//NumberTemplate 사용 시
Expressions.numberTemplate(Long.class, "to_number({0})", entity.age);

//StringTemplate 사용 시
Expressions.stringTemplate("to_char({0}, '{1s}')", entity.createAt, "YYYY-MM");

```

__template로 함수를 만들 때 값을 바인딩하는데 문자열인 경우 자동으로 ''가 안붙는것같다. 
그냥 {n}으로 하면 dbms에서 오류가 나는데 '{n}'으로 넣으니까 정상적으로 문자열로 바인딩되었다.__


Template 객체들은 `ComparableExpressionBase`을 상속하기때문에 `eq`, `as`, `and`, `like` 등 전부 사용이 가능하다.

그래서 select, where, groupBy, orderBy 등에 전부 사용이 가능하다.


## 예시
```java

    QMyEntity myEntity = QMyEntity.myEntity;
    
    StringTemplate dateAsString = Expressions.stringTemplate("TO_CHAR({0}, '{1s}')", myEntity.createAt, "YYYY-MM");
    
    return jpaQueryFactory.select(Projections.fields(ResultDto.class,
                                               dateAsString.as("date"),
                                               myEntity.count().as("cnt")
                                               )
                           )
                           .from(myEntity)                       
                           .groupBy(dateAsString)
                           .orderBy(dateAsString.asc())
                           .fetch();

```

## 쿼리 생성 결과

```sql
 select
        count(myentity0_.id) as col_0_0_,
        to_char(myentity0_.createAt,
        'YYYY-MM') as col_1_0_ 
    from
        myentity myentity0_ 
    group by
        to_char(myentity0_.createAt,
        'YYYY-MM') 
    order by
        to_char(myentity0_.createAt,
        'YYYY-MM') asc
```



**참고**

- [stackoverflow](https://stackoverflow.com/questions/36542833/querydsl-group-by-hours-in-a-time-range)
- [https://dev-racoon.tistory.com/39](https://dev-racoon.tistory.com/39)






