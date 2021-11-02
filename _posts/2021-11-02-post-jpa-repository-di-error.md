---
layout: post
title: "[SPRING/JPA] JPA Repository가 정상적으로 DI 되지 않는 현상"
categories: [error, jpa]
tags: [jpa, error, spring]
---

## 오류

```
Description:

Parameter 0 of constructor in com.my.service.MyService required a bean of type 'com.my.jpa.repository.MyRepository' that could not be found.

Action:

Consider defining a bean of type 'com.my.jpa.repository.MyRepository' in your configuration.
```

entity 클래스와 repository가 생성했는데도 불구하고 데이터 주입이 되지 않는 현상이 발생했다.


```java
//entity 클래스
@Entity
@Getter
@AllArgsConstructor
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class MyEntity {
  @Id
  private Long id;
  
  private String name;
}
```

```java
//repository 인터페이스
public interface MyEntityRepository extends JpaRepository<MyEntity, Long> {

}
```

## 원인

JPA에 대한 설정을 제대로 하지 않은게 문제였다.

개인적을 공부 할 때는 모듈 개념없이 진행을 해서 별다른 설정이 필요 없는것으로 잘못 알고 있었다.

gradle에서 boot-data-jpa 의존성만 가져오면 모든게 다 되었기 때문...ㅠㅠ

현재 하는 프로젝트는 간단하게 몇개의 모듈로 나눠져있는데 프로젝트 구조는 대략적으로 다음과 같다.

```
my-starter ---> my-mvc ---> my-config
```
 
starter에 sptring boot starter가 있었고, mvc쪽에 entity와 repository가 있다.

starter가 mvc를 참조하고 있기 때문에 mvc에 있는 entity와 repository가 자동으로 인식 될줄 알았지만 아니었다.



## 해결

어노테이션 2개로 간단하게 해결 가능하다.

@ComponentScan 처럼 직접 패키지를 scan하도록 설정해주면 된다.

1. @EnableJpaRepositories : jpa repository를 사용하겠다는 설정
2. @EntityScan : entitiy를 scan

각 어노테이션에 직접 패키지를 입력해주면 된다.

```java
@SpringBootApplication
@EnableJpaRepositories(basePackages = {"com.my.jpa.repository"}) // com.my.jpa.repository 하위에 있는 jpaRepository를 상속한 repository scan
@EntityScan(basePackages = {"com.my.jpa.entity"}) // com.my.jpa.entity 하위에 있는 @Entity 클래스 scan
public class MyApplication {
  public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

패키지를 명세해주면 정상적으로 작동한다 ㅎㅎ 
       
       
       

참고
- [http://daplus.net/](http://daplus.net/spring-spring-boot%EC%97%90%EC%84%9C-repository-%EC%A3%BC%EC%84%9D%EC%9D%B4-%EB%8B%AC%EB%A6%B0-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-autowire-%ED%95%A0-%EC%88%98-%EC%97%86%EC%8A%B5/)






