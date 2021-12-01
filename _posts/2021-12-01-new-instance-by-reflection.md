---
layout: post
title: "[JAVA] Reflection을 이용해서 객체 인스턴스 생성하기"
categories: [java]
tags: [java]
---

공통 기능 개발 중 불특정 객체에 대한 인스턴스를 생성해야 했다.

자바의 Reflection을 이용해서 인자값으로 들어온 타입의 인스턴스를 생성해주도록 했다.

해당 부분에 대해 정리해본다.


## 코드

기본 생성자로만 생성하도록 만들었다.

public도 isAccessible이 false로 항상 나오는데 이 부분은 더 확인이 필요하다.

```java

 private <T> T getNewInstance(Class<?> clazz){
        try {
            Constructor<?> constructor = clazz.getDeclaredConstructor();
            if(!constructor.isAccessible()){
                constructor.setAccessible(true);
            }

            return (T) constructor.newInstance();
        } catch (NoSuchMethodException | InstantiationException | IllegalAccessException | InvocationTargetException | IllegalArgumentException e){
            throw new RuntimeException("Can't generate no args constructor. Class : " + clazz.getName(), e);
        }
    }

```


## Constructor

Reflection에서도 new와 동일하게 Class에 선언한 생성자를 통해서 인스턴스를 생성한다.

Class객체에 선언한 생성자들의 정보가 담겨 있다.



### class.getConstructor()

인자값으로 넣은 클래스에 대한 생성자를 반환해준다.

기본 생성자로 찾을 시 인자값 없이 호출하면 된다.

접근이 가능한 생성자만 반환해준다.

```java

  // 기본 생성자
  User user = new User(); 
  User user = ((Class<?>) User.class).getDeclaredConstructor().newInstance();

  // 인자값이 있는 생성자
  String name = "jin";
  User user = new User(name); 
  User user = ((Class<?>) User.class).getDeclaredConstructor(String.class).newInstance(name); 

```



### class.getDeclaredConstructor()

getConstructor와 같이 생성자를 반환해주는데 

선언한 `모든` 생성자를 반환해준다. 따라서 접근 제한이 걸릴 수 있다. (IllegalAccessException)

접근 제한이 되어있는 경우 `constructor.setAccessible(true);` 로 접근이 가능하도록 변경하면 된다.




### class.getConstructors()

접근이 가능한 모든 생성자를 `배열`로 반환해준다.



### class.getDeclaredConstructors()

접근 여부와 상관없이 선언한 모든 생성자를 `배열`로 반환해준다.

### Constructor 객체 데이터들

해당 생성자의 파라미터, 예외종류, annotation 등 다양한 정보가 들어있다.

필요에 따라 사용하면 좋을듯하다.

![image](https://user-images.githubusercontent.com/29051992/144217381-b43c38bf-19b6-46db-a7d6-b6b5dab1900d.png)










