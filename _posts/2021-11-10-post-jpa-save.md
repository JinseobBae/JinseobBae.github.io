---
layout: post
title: "[SPRING/JPA] ID를 할당해서 save 할 때 왜 select 후에 insert를 할까?"
categories: [jpa]
tags: [jpa, spring]
---


새로운 프로젝트에서 JPA를 이용하기때문에 요즘 열심히 공부해보고 있다.

mybatis만 썼던 나에게는 너무나도 헷갈리는 개념;;

entity를 만들다보면 ID를 자동으로 생성할 때도 있고 직접 할당 할 때도 있다.

직접 할당 시 save를 할 때 혼란스러웠던 부분을 정리해본다.


## 혼란스러웠던 점

**엔티티에서 ID를 직접 할당해서 사용을 했다.**

save 메서드를 이용하면서 너무나도 혼란스러웠던 것이 있었다.

**새로운 entity를 save하는데 왜 insert 전에 select?**

id 자동 생성시에는 insert전에 select를 안하는데 직접 할당하면 insert전에 select를 하고있다. 왜?

## SAVE 

아래처럼 id가 2개인 엔티티로 진행을 했다.

```java

@Entity
@IdClass(MessagePk.class)
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Message {

    @Id
    @Column
    private String msgLang;

    @Id
    @Column
    private String msgCode;
    
    @Column
    private String msgType;

```

키는 직접 할당하는 방식으로 사용을 했다.


```java
public void insert(){
  Message message = Message.builder()
                      .msgLang("ko_kr")
                      .msgCode("message_code")
                      .msgType("test")
                      .build();
                      
 messageRepository.save(message);                     
```

save를 실행하면 로그에 select > insert > select 총 3개의 쿼리가 찍힌다.

왜그런지 과정을 보자.

### SimpleJpaRepository

repository에서 JpaRepository를 상속하면 SimpleJpaRepository를 사용하게 된다.

요청한 save 메서드는 다음과 같다.

![image](https://user-images.githubusercontent.com/29051992/141048159-b59be0ef-20b3-4e37-bf59-0275bf253e74.png)

새로 insert한다고 생각을 하면 `entityInformation.isNew` 가 true고 `em.persist()`로 이동해야한다.

하지만! ID를 직접 생성하면 `entityInformation.isNew`가 false로 떨어진다. 

그럼 `em.merge()` 메서드로 이동을 하고 merge에서 해당 데이터의 존재 여부를 판단한다.

그래서 insert전에 select가 한 번 호출되는 것이었다.

select해서 데이터가 없으면 insert 있으면 update를 실행해주는 것이다!!

근데 그럼 왜 isNew가 false로 나올까?

### AbstractEntityInformation

`entityInformation.isNew`를 타고 들어가면 해당 클래스에 도착한다.

거기서 isNew에 대한 값을 판단한다.

![image](https://user-images.githubusercontent.com/29051992/141049707-6aea63f9-3e75-446b-959c-b8afe1e9ca39.png)

idType이 Primitive 타입이 아닌지 확인을 한다. (JpaRepository 상속 시 입력한 ID 타입을 보고 판단을 한다.)

그리고 그게 null인지 아닌지 판단한다.

id를 직접 할당하면 저기서 무조건 false가 나온다. 이미 저 메서드를 타기전에 id컬럼에 값을 넣어버렸으니까!

id를 자동 생성을 하면 저 시점에서는 id가 null인 상태로 있다. 그래서 true가 반환이 된다.


### 결론

바로 insert인가? 데이터를 검색해보고 insert 혹은 update인가? 이 2가지 방법 중 어떤것으로 갈지는

내가 지정한 @Id 컬럼의 값 존재여부였다. 

사실 단건 insert를 할 때는 select문이 나오는게 큰 상관이 없지만 

대량 데이터를 insert할 때는 그 갯수만큼 select이 실행될거라고 생각을 하면...

ID 직접 할당 시에도 isNew가 true로 나오는 방법이 있는지 찾아봐야겠다.


















