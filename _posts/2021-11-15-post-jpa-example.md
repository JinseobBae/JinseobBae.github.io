---
layout: post
title: "[SPRING/JPA] Spring Data의 Example 활용하기"
categories: [SPRING, JPA]
tags: [spring, jpa]
comments: true
---


Spring data jpa를 사용하면 Example 이라는 객체를 이용해 데이터 조회(find, count, exist)를 할 수 있다.

example과 exampleMatcher라는 전략설정(?)을 이용해 해당 조건에 만족하는 데이터를 조회하는 기능이다.

## Example 

### Example 정보

Example은 interface로 구현체는 TypedExample가 있다.

사용 할 객체는 제네릭<T>로 설정되어있다.

선언 시 Example.of(객체)로 쉽게 생성이 가능하다.

![image](https://user-images.githubusercontent.com/29051992/141815133-b770b0a9-ec3a-40f2-a7ae-d86058a10fb3.png)

기본적으로 설정되는 ExampleMatcher가 있지만, 사용자가 원할 경우 직접 생성해서 선언이 가능하다.

```java
 // 간단하게 생성 가능
 Example<User> userExample = Example.of(new User());
```

Example을 이용해 조회를 하려면 Repository가 `QueryByExampleExecutor`를 구현해줘야 하는데 

Spring data jpa를 사용한다면 Respoitory가 JpaRepository를 상속해주기만 하면 된다.

JpaRepository에서 이미 QueryByExampleExecutor를 상속받고 있으며, 기본 구현체인 SimpleJpaRepository에 구현이 되어있다.

![image](https://user-images.githubusercontent.com/29051992/141815189-eeb1351b-9ca7-4e59-9f4e-b53a869d2aac.png)

![image](https://user-images.githubusercontent.com/29051992/141815242-f8ee2dfd-2025-4f28-ba54-fda61e6a87bb.png)


### Example 활용

Example을 ExampleMatcher 없이 생성했을 경우 ExampleMatcher는 기본값으로 생성이 된다. 

`Example.of(user)` 일 때 user 객체에서 값이 null인 필드를 제외하고 조회 시에 사용을 한다. 

**문자열인 경우 값이 "" 일 때에도 조건에 적용 시켜버리니 주의가 필요하다.**   

조건절에 값이 있는 필드들을 and로 엮어서 select 구문을 생성하고 실행한다.  

예를 들어보면 다음과 같다. 

```java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
@Builder
public class User {

    @Id
    @GeneratedValue
    Long id;

    String name;

    Long age;

    String address;

    String phone;
}
```

위와 같이 엔티티 클래스가 있을 때 해당 클래스로 example을 만들어보면 다음과 같다.

```java
@Test
void example(){

    User user = User.builder()
            .name("TEST")
            .age(0L)
            .phone("")
            .build();

    userRepository.findAll(Example.of(user));
}
```

`userRepository.findAll(Example.of(user))` 실행을 해보면 쿼리가 다음과 같이 생성된다.

 
```sql
select
        user0_.id as id1_0_,
        user0_.address as address2_0_,
        user0_.age as age3_0_,
        user0_.name as name4_0_,
        user0_.phone as phone5_0_ 
    from
        user user0_ 
    where
        user0_.phone=? 
        and user0_.name=? 
        and user0_.age=0
```

값을 넣은 name, age, phone이 조건에 들어가고 값을 안넣어 null인 address와 id는 조건에서 제외된다.

Example 생성 시 기본값으로 생성하는것 외에도 직접 ExampleMatcher를 생성해서 조회가 가능하다.


## ExampleMatcher

ExampleMatcher는 Example로 조회 시 어떤 방식으로 조회 할 것인지 설정을 해준다.

ExampleMatcher는 총 3개 matching(기본값), matchingAll, matchingAny로 구분된다.

Example 생성 시 별도로 설정하지 않으면 matching이 기본값으로 설정된다.

**사실 matching과 matchingAll은 똑같다. 소스를 확인해보면 matching으로 생성 시 matchingAll로 설정을 해준다. 아직 만드는 중인지..?**

![image](https://user-images.githubusercontent.com/29051992/141815354-c42390c8-5353-4f5e-952a-dea60121f03c.png)

matching(matchingAll)은 null이 아닌 필드를 **and**로 엮어주고, matchingAny는 **or**로 엮어준다.

### matching(matchingAll)

위에 있는 예시처럼 and로 조건이 생성된다.

```java
@Test
void example(){

    User user = User.builder()
            .name("TEST")
            .age(0L)
            .phone("")
            .build();

    ExampleMatcher matcher = ExampleMatcher.matchingAll();
    userRepository.findAll(Example.of(user, matcher));
}
```

sql 생성 결과

```sql
select
        user0_.id as id1_0_,
        user0_.address as address2_0_,
        user0_.age as age3_0_,
        user0_.name as name4_0_,
        user0_.phone as phone5_0_ 
    from
        user user0_ 
    where
        user0_.age=0 
        and user0_.name=? 
        and user0_.phone=?
```
 
### matchingAny

matchingAny는 말그대로 아무거나 하나만 일치하면 된다. 때문에 or로 조건문을 생성해준다.

```java
@Test
void example(){

    User user = User.builder()
            .name("TEST")
            .age(0L)
            .phone("")
            .build();

    ExampleMatcher matcher = ExampleMatcher.matchingAny();
    userRepository.findAll(Example.of(user, matcher));
}
```

sql 생성 결과

```sql
select
        user0_.id as id1_0_,
        user0_.address as address2_0_,
        user0_.age as age3_0_,
        user0_.name as name4_0_,
        user0_.phone as phone5_0_ 
    from
        user user0_ 
    where
        user0_.name=? 
        or user0_.phone=? 
        or user0_.age=0

```


### withIgnorePath

기본적으로 matching이나 matchingAny에서는 null이 아닌 필드는 무조건 조건으로 생성해준다.

만약 값이 있지만 조건을 생성하고싶지 않은 필드가 있다면 ExampleMatcher 생성 시 withIgnorePath로 제외가 가능하다.

withIgnorePath에 필드명을 입력해주면 된다. 필드명이 일치하지 않으면 제외되지 않는다. 오류도 안난다. 그냥 실행된다.

값이 ""인 phone 필드를 검색에서 제외해보자.

```java 
@Test
void example(){

    User user = User.builder()
            .name("TEST")
            .age(0L)
            .phone("")
            .build();

    ExampleMatcher matcher = ExampleMatcher.matchingAll()
       .withIgnorePaths("phone");
    userRepository.findAll(Example.of(user, matcher));
}
```

sql 생성 결과

```sql
select
        user0_.id as id1_0_,
        user0_.address as address2_0_,
        user0_.age as age3_0_,
        user0_.name as name4_0_,
        user0_.phone as phone5_0_ 
    from
        user user0_ 
    where
        user0_.age=0 
        and user0_.name=?
```

phone 필드는 값이 있지만 무시하기로 설정했으므로 조건에 phone이 제외된다.

### withIgnoreCase

검색을 할 때 대소문자를 무시하는것도 가능하다.

name 필드를 대소문자 관계없이 조회해보자.

```java 
@Test
void example(){

    User user = User.builder()
            .name("TEST")
            .age(0L)
            .phone("")
            .build();

    ExampleMatcher matcher = ExampleMatcher.matchingAll()
       .withIgnoreCase("name");
    userRepository.findAll(Example.of(user, matcher));
}
``` 

sql 생성 결과

```sql
select
        user0_.id as id1_0_,
        user0_.address as address2_0_,
        user0_.age as age3_0_,
        user0_.name as name4_0_,
        user0_.phone as phone5_0_ 
    from
        user user0_ 
    where
        user0_.phone=? 
        and lower(user0_.name)=? 
        and user0_.age=0
```

설정한 필드를 lower 함수로 강제로 소문자로 만든다. 그리고 ?에 들어갈 데이터도 소문자로 만들어서 조회를 한다.

뒤에 로그를 보면 파라미터로 입력한 "TEST"가 아닌 "test"로 들어가는 것을 확인 할 수 있다.

```
[    Test worker] o.h.type.descriptor.sql.BasicBinder      : binding parameter [1] as [VARCHAR] - []
[    Test worker] o.h.type.descriptor.sql.BasicBinder      : binding parameter [2] as [VARCHAR] - [test] // 소문자로 변경!!
``` 



### Like 검색

Like검색도 가능하다.

ExampleMatcher 생성 시 withStringMatcher, withMatcher를 이용해서 Like 조건을 만들 수 있다.

withMatcher는 원하는 필드 1개에 대해서 조건을 설정하는 것이고

withStringMatcher는 모든 String 필드에 대해 조건을 설정하는 것이다.

**설정 순서에 상관없이 withMatcher의 설정이 우선된다.(필드에 직접 설정한 것이 우선된다.)**

숫자는 따로 제어가 가능한지는 아직 모르겠다..


```java 
@Test
    void example(){

        User user = User.builder()
                .name("TEST")
                .age(0L)
                .address("Seoul")
                .build();


        ExampleMatcher matcher = ExampleMatcher.matching()
                .withStringMatcher(ExampleMatcher.StringMatcher.CONTAINING)
                .withMatcher("name", ExampleMatcher.GenericPropertyMatcher.of(ExampleMatcher.StringMatcher.ENDING));
        
        userRepository.findAll(Example.of(user, matcher));
}
```

sql 생성 결과

```sql
select
        user0_.id as id1_0_,
        user0_.address as address2_0_,
        user0_.age as age3_0_,
        user0_.name as name4_0_,
        user0_.phone as phone5_0_ 
    from
        user user0_ 
    where
        user0_.age=0 
        and (
            user0_.name like ? escape ?
        ) 
        and (
            user0_.address like ? escape ?
        )
```

파라미터

```
[    Test worker] o.h.type.descriptor.sql.BasicBinder      : binding parameter [1] as [VARCHAR] - [%TEST]
[    Test worker] o.h.type.descriptor.sql.BasicBinder      : binding parameter [2] as [CHAR] - [\]
[    Test worker] o.h.type.descriptor.sql.BasicBinder      : binding parameter [3] as [VARCHAR] - [%Seoul%]
[    Test worker] o.h.type.descriptor.sql.BasicBinder      : binding parameter [4] as [CHAR] - [\]
```

기본적으로 contain 전략을 이용해서 모든 문자열은 Like `%문자열%`로 조회된다.

name의 경우 직접 ending으로 설정해서 `%문자열`로 파라미터가 바인딩 된걸 확인 할 수 있다.

Like 전략은 Contain, Starting, Ending, Exact, Default, Regex가 있다.

default로 하면 equal(=) 로 조회를 하는데 exact랑 어떤 차이가 있는지는 잘 모르겠다.


## 결론

사실 간단한 조회에서는 직접 쿼리를 생성하거나 repository를 이용해서 하는게 더 편한 것 같다.

하지만 여러가지 값을 통해서 조회를 할때에는 Example을 사용하는게 더 깔끔하다고 생각한다.

repository에서 `findByAAndBAndCAndDAnd....(Long A, String B,......)` 이렇게 만들면 너무 지저분 할 듯한 느낌이...

Exapmle을 잘 이용하면 깔끔하게 구현할 수 있을 듯 하다.

ExampleMatcher는 언급한 기능 외에도 withIgnoreNullValues, withNullHandler 등 여러가지 기능을 제공하고 있다.

아직 실제로 사용은 안해봐서 얼마나 사용성이 있을지는 모르겠다.

 
 
 참고
 
 - [ExampleMatcher spring docs](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/ExampleMatcher.html)
