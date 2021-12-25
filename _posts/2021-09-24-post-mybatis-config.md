---
layout: post
title: "[JAVA/SPRING] Mybatis xml config를 java config로 변경해보기"
categories: [spring]
tags: [java, mybatis, spring]
---


spring boot는 애플리케이션에 대한 설정을 java config로 하는 것을 추천한다.

그래서 기존에 xml로 되어있던 config를 java config로 변경하는 작업을 진행했다.

`mybatis v3.4.5 , mybatis-spring v1.3.1`  로 진행했다.



## 1. config 클래스 생성

```java

@Configuration
public class MyBatisConfig {

}
```

@Configuration 을 입력해서 config 클래스로 명시한다.




## 2. sqlSessionFactory 생성

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <qualifier value="main" />
        <property name="dataSource" ref="dataSource" />        
        <property name="configLocation" value="classpath:/spring/mybatis-config.xml" />
        <property name="mapperLocations" value="classpath:/com/my/mappers/**.xml" />
</bean>
```
mybatis-config.xml에는 typealias 지정, typeHandler를 지정하는 코드가 들어가있다. 해당 부분은 아직 java로 변경하지 않았다.

```java
@Bean
@Qualifier("main")
public SqlSessionFactoryBean sqlSessionFactory(ApplicationContext applicationContext){
  SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
  factoryBean.setDataSource(dataSource); // 데이터소스 설정  
  factoryBean.setConfigLocation(applicationContext.getResource("classpath:/spring/mybatis-config.xml")); // typeAlias, typeHandler 설정
  factoryBean.setMapperLocations(applicationContext.getResource("classpath:/com/my/mappers/**.xml")); // xml mapper 위치 설정
  return factoryBean;
}
```

bean 생성 시 applicationContext를 인자로 받아서 resources 를 사용하는 방법으로 구현했다.



## 3. mapper scan 설정

mapper scan 설정은 bean 생성 없이 어노테이션으로 간단하게 처리가 가능하다.

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.my" />
		<property name="annotationClass" value="org.springframework.stereotype.Repository" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
</bean>
```

```java

@Configuration
@MapperScan(
        basePackages = { "com.my" },
        annotationClass = org.springframework.stereotype.Repository.class,
        sqlSessionFactoryRef = "sqlSessionFactory"
)
public class MyBatisConfig {

}
```



mybaits-spring의 MapperScan.java을 보면 아래 그림처럼 주석으로 깔끔하게 예시를 보여주고 있다. 한번 쯤 확인을 해봐도 좋을듯하다.


![image](https://user-images.githubusercontent.com/29051992/134616478-a15953e6-698c-4d16-92be-ba147250e232.png)

