---
layout: post
title: "[SPRING/JPA] JPA Example 사용 할 때 날짜 조건 포함하기"
categories: [jpa]
tags: [error, jpa]
---


Spring data의 Example을 이용해서 검색을 주로 했는데 날짜를 조건으로 넣어야 하는 상황이 생겼다.

검색을 해보니 Example은 날짜에 대한 제어는 불가능해 보였고

Spring data의 Specification을 이용해서 구현하면 가능하다는걸 보았다.


## Specification

Spring 블로그에 가보면 이렇게 설명이 되어있다.

> To be able to define reusable Predicates we introduced the Specification interface that is derived from concepts introduced in Eric Evans’ Domain Driven Design book. It defines a specification as a predicate over an entity which is exactly what our Specification interface represent


재사용이 가능한 서술(?)이며, 서술형태로 스펙을 정의한다고 하는데, 그냥 사용자가 정의한대로 동적으로 쿼리를 생성해주는걸로 보면 될 듯 하다.

Specification을 사용하려면 repository에서 `JpaSpecificationExecutor<T>` 를 상속하면 된다.
  
  
```java 
  
  public intterface MyRepository extends JpaRepository<MyEntity, Long>, JpaSpecificationExecutor<MyEntity>{
  }
  
```  
  
`JpaSpecificationExecutor<T>` 를 상속받으면 총 5개의 메서드를 사용 할 수 있다.
  
![image](https://user-images.githubusercontent.com/29051992/145795405-2c818bfb-e8bd-44b5-b632-be27c721caa4.png)
  

## 날짜 조건 포함시키기  
  
조건을 추가하려면 repository에 Specification<T>의 `toPredicate`를 구현하면 된다.
 
클래스를 생성해서 구현하려면 JpaRepository까지 구현해야하니까 repository에 default로 만들었다.  
  
```java
  
  //lambda
  default Specification<T> searchWithDate(Map<String,Object> condition, Example<T> example){
        return (root, query, builder) -> {
            final List<Predicate> predicates = new ArrayList<>();
            
            if(condition.get("fromDate") != null){
              predicates.add(builder.greaterThanOrEqualTo(root.get("date"), condition.get("fromDate")));
            }
  
            if(condition.get("toDate") != null){
              predicates.add(builder.lessThanOrEqualTo(root.get("date"), condition.get("toDate")));
            }
            // Example을 조건에 추가
            predicates.add(QueryByExamplePredicateBuilder.getPredicate(root, builder, example));

            return builder.and(predicates.toArray(new Predicate[predicates.size()]));
        };
    }
  
   // new 
   default Specification<T> searchWithDate(Map<String,Object> condition, Example<T> example){
        return new Specification<T>() {
            @Override
            public Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                final List<Predicate> predicates = new ArrayList<>();

                if(condition.get("fromDate") != null){
                    predicates.add(builder.greaterThanOrEqualTo(root.get("date"), condition.get("fromDate")));
                }

                if(condition.get("toDate") != null){
                    predicates.add(builder.lessThanOrEqualTo(root.get("date"), condition.get("toDate")));
                }
                // Example을 조건에 추가
                predicates.add(QueryByExamplePredicateBuilder.getPredicate(root, builder, example));

                return builder.and(predicates.toArray(new Predicate[predicates.size()]));
            }
        };
   }

```  

먼저 원하는 조건을 predicates list에 추가하면 된다.

`predicates.add(builder.특정조건(root.get("field"), 원하는 조건값));` 이다.

builder는 `CriteriaBuilder`라는 인터페이스로 greaterThanOrEqualTo, lessThanOrEqualTo, greaterThan 등등 다양한 조건들이 있다.

root는 entity 타입이라고 보면 된다. `root.get(entitiy의 필드)`로 하면 해당 필드에 대한 조건을 생성한다.

주의할 점은 조건에 추가하려는 entity의 필드 타입과 조건값의 타입이 일치해야한다는 점이다. 

위와같이 스펙을 만들어놓고 `JpaSpecificationExecutor`의 메서드를 태우면 된다.

```java

 default List<T> findAllWithSpec(Map<String,Object> condition, Example<T> example){
    return findAll(searchWithDate(condition, example));
}

```

add한 순서대로 where 조건이 만들어지는듯하니 순서변경을 원한다면 add하는 순서를 변경하면 될 듯 하다.


참고

 - [Spring.io blog](https://spring.io/blog/2011/04/26/advanced-spring-data-jpa-specifications-and-querydsl)
 - [hojak99님의 Spring JPA Specification](https://hojak99.tistory.com/503)

  
  
  
