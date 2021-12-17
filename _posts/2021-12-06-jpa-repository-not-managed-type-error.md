---
layout: post
title: "[JPA] Respository 만들 때 Not a managed type: class java.lang.Object 오류"
categories: [error, jpa]
tags: [error, jpa]
---


## 오류
```
 java.lang.IllegalArgumentException: Not a managed type: class java.lang.Object
```

JPA에서 공통으로 사용 할 repository를 만드려고 했을 때 해당 메세지가 발생했다. 


공통 repository를 만들어서 특정 기능을 공유하려고 했다.
```java
public interface CommonRepository<T, ID> extends JpaRepository<T, ID>, JpaSpecificationExecutor<T> {

  default List<T> something(){
    ...
  }
  
}
```

entity repository는 공통 repository를 상속받게 했다.
```java
public interface MyRepository extends CommonRepository<MyEntity, Long>{

}
```

공통 repository는 어떤 타입이 들어올지 모르기때문에 제네릭으로 T, ID를 선언했었다.

이렇게 코드를 작성하니 `Not a managed type: class java.lang.Object`가 발생했다.


## 원인

**Object 라는 타입의 entity는 등록이 되어있지 않기때문. Object는 entity 객체가 아니니까!**

**Repository를 생성 할 때 명시한 entity 객체가 내부에서 entity 객체로 인식되지 않은 문제다.**

내 케이스는 제네릭을 이용한것이 문제였다.

```
Error creating bean with name 'commonRepository' defined in com.my.jpa.repository.CommonRepository defined in @EnableJpaRepositories declared on MyApplication ....
```

Spring에서 repository의 구현체를 만들 때 해당 repository가 관리해야하는 Entity의 타입을 찾지 못해서 발생하는 에러다.

![image](https://user-images.githubusercontent.com/29051992/144835128-4e75b32f-6406-4c2b-9829-9565f4061114.png)

spring-data-jpa에서 entity information을 생성할 떄 @Entity로 등록한 Jpa entity 목록을 통해서 ManagedType을 결정한다.

하지만 그냥 `제네릭(T)`로 구현 시 `T(Object)` 라는 entity가 없기 때문에 관리할 수 없는 타입이라고 나오게 된다.


직접 entity 클래스를 입력했음에도 불구하고 발생한다면, 설정한 Entity가 정상적으로 Entity객체로 등록이 안된거라고 본다.

 - @Entity 누락 
 - EntityScan이 안됨 (직접 @EntityScan으로 패키지 지정 시)
 - 잘못된 클래스 입력..?

다음과 같은 경우가 있을 듯 하다.

## 해결

해당 repository를 구현하지 않으면 된다. 사실 공통 repository라서 직접 사용되는 경우는 없도록 설계해서 구현 할 필요가 없다.

이렇게 구현 할 필요가 없는 repository는 **@NoRepositoryBean** 을 명시해주면된다. 

```java
@NoRepositoryBean 
public interface CommonRepository<T, ID> extends JpaRepository<T, ID>, JpaSpecificationExecutor<T> {

  default List<T> something(){
    ...
  }
  
}
```

말그대로 이 클래스는 repository bean이 아니라는 명시다.

실제로 spring에서 제공해주는 JpaRepository, PagingAndSortingRepository, CrudRepository 등을 보면 해당 어노테이션이 명시되어있다.

@NoRepositoryBean 을 보면 설명이 잘 되어있다.

![image](https://user-images.githubusercontent.com/29051992/144836499-7ef38d7c-b700-44c6-bd53-41e5e3392b08.png)



참고

- [Stackoverflow (Spring Data: Not an managed type: class java.lang.Object) ](https://stackoverflow.com/questions/37124425/spring-data-not-an-managed-type-class-java-lang-object)






