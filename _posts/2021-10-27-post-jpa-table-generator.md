---
layout: post
title: "[SPRING/JPA] @TableGenerator로 ID 할당하기 "
categories: [SPRING, JPA]
tags: [spring, jpa]
comments: true
---


현재 회사에서 Mybatis로 되어있는 데이터접근을 JPA로 바꾸는 작업을 하고 있다.

Mybatis 이용 시 PK를 키 관리하는 테이블을 통해서 생성을 했고, JPA에서도 그 방식을 유지하기로 했다.

알아보니 여러 ID 생성 전략 중 Table은 성능이 딸린다는 단점이 있기는 한데, 현 상황에서는 여러 DBMS에서 공통적으로 쓰기 편하다는 장점이 더 커서 이 방법으로 유지했다.

기존에는 테이블에서 키를 조회하고 업데이트하는 쿼리를 직접 호출해서 PK를 생성했지만 JPA에서는 Entity객체에 어노테이션으로만으로 가능했다.



### 1. Key 관리 테이블 생성


간단하게 키를 관리할 테이블을 생성한다. (table_name이 PK)

```sql
-- oracle
create table tb_mgn_id(
  table_name varchar2(50) ,
  table_max_id number(10)   
)
```


### 2. Entity 객체에 TableGenerator 설정


해당 테이블을 통해서 ID를 생성하고 싶은 Entity 객체에 @TableGenerator를 이용해 정보를 입력해준다.

```java
@Entity
@TableGenerator(
        name = "tableIdGenerator",
        table = "tb_mgn_id",
        pkColumnName = "table_name",
        pkColumnValue = "myTable",
        valueColumnName = "table_max_id",
        initialValue = 1,
        allocationSize = 1
)
public class myTable{
}
```

| 속성 | 설명 | 기본값 |
|------|------|--------|
| name     |  매핑에 사용 될 명칭    |        |
| table     |  사용 할 테이블    |        |
| pkColumnName     |  table의 pk 컬럼명    |        |
| pkColumnValue     | pk컬럼의 값     |        |
| valueColumnName     | id로 사용 할 컬럼     |        |
| initialValue     | 시작값     |   0    |
| allocationSize     | 증가값     |   50    |



### 3. Id와 generator 매핑

ID로 사용 할 항목에 위에서 등록한 generator를 매핑해준다.

```java
@Entity
@TableGenerator(
        name = "tableIdGenerator",
        table = "tb_mgn_id",
        pkColumnName = "table_name",
        pkColumnValue = "myTable",
        valueColumnName = "table_max_id",
        initialValue = 1,
        allocationSize = 1
)
public class myTable{
  
  @Id
  @@GeneratedValue(strategy = GenerationType.TABLE, generator = "tableIdGenerator")
  private Long Id;
}

```

@TableGenerator 어노테이션 소스를 보면 주석으로 간략한 사용법이 설명되어 있으니 참고하면 좋을 듯 하다.

![image](https://user-images.githubusercontent.com/29051992/139189354-67c7c2a8-c5a9-4886-b275-877787d5b6d6.png)


### 정리

Id 생성 전략 중 성능이 안좋은편이라고 하는데 직접 느껴보기전까진 잘 모를 것 같다.

> The scope of the generator name is global to the persistence unit (across all generator types).

해당 문구가 주석 마지막에 있는데, generator의 명칭은 전략과 상관없이 유니크하게 명명해야하나보다. 
