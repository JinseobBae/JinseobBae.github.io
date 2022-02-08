---
layout: post
title: "[JAVA] lombok의 @Superbuilder"
categories: [java]
tags: [java, lombok, superbuilder]
---

lombok을 사용하면서 객체 생성을 할 때 builder 패턴을 많이 사용한다.

아무래도 `setOOO()`도 안하고 `@Builder`선언 하나로 간단하게 사용이 가능해서 더 편하게 쓴다.

이런 `@Builder`가 상속관계에서는 원하는대로 작동하지 않는다.

자식 객체에서 `builder()`로 객체를 빌드할 때 부모의 필드는 사용 할 수가 없다.

바로 IDE에서 컴파일 오류로 판단해서 보여준다.

그럴 때 사용하는게 `@SuperBudiler`다.(lombok v1.18.2부터)

`@SuperBudiler`는 자식이 부모의 필드까지 빌더 패턴으로 사용하게 해준다.

`@SuperBudiler` 어노테이션을 보면 이렇게 설명되어있다. 

> The SuperBuilder annotation creates a so-called 'builder' aspect to the class that is annotated with SuperBuilder, but which works well when extending. It is similar to @Builder, except it is only legal on types, is less configurable, but allows you to extends other builder-able classes.  All classes in the hierarchy must be annotated with @SuperBuilder.

Builder간 extends가 가능하게 해주는 대신 계층에 있는 모든 클래스들은 `@SuperBuilder`를 선언해야한다.

만약 한쪽에만 `@SuperBuilder`를 선언하면 컴파일 오류가 발생한다.


부모 클래스에만 선언했을 경우에는 아래와 같은 오류가 난다.

```text
error: constructor Parent in class Parent cannot be applied to given types;
@Builder
^
  required: ParentBuilder<?,?>
  found: no arguments
  reason: actual and formal argument lists differ in length
```



자식 클래스에만 선언했을 경우에는 아래와 같은 오류가 난다.

```text
error: type ParentBuilder does not take parameters
@SuperBuilder
^
```

컴파일이 되면 `@Builder`와는 다르게 2개의 내부 클래스가 생성되고(builder는 1개)
생성자에는 super() 메서드가 추가된다.

```java
protected Child(final Child.ChildBuilder<?, ?> b) {
        super(b);
        this.subId = b.subId;
    }
```

내부 클래스는 abstract랑 final클래스가 만들어지는데 문서를 읽어보면 type-safety를 위한것이라고 한다. 그냥 봐서는 잘 모르겠다.. 

`@SuperBuilder`도 `@builer`처럼 buildMethodName, builderMethodName등 정의가 가능하다.

```java
@SuperBuilder(buildMethodName = "build", builderMethodName = "builder")
public class Child extends Parent{
  Long subId;
}

```

toBuilder도 `@Builder`처럼 true/false로 사용이 가능한데, 만약 true로 설정 시, 모든 부모 클래스도 true 해줘야 한다고 한다.


**참고**

- [stackoverflow](https://stackoverflow.com/questions/68047993/lombok-superbuilder-doesnt-take-parameters)
- [projectlombok.org](https://projectlombok.org/features/experimental/SuperBuilder)






