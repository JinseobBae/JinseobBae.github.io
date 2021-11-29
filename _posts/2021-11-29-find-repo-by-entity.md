---
layout: post
title: "[SPRING/JPA] Generic을 이용해 entity의 repository 찾아보기"
categories: [jpa]
tags: [jpa, spring]
---

기능 개발 중 reflection을 통해서 특정 entity객체를 생성하고

해당 entity의 repository를 찾아서 select를 해야하는 상황이 있었다.

모든 repository는 JpaRepository를 상속받고 있었고, 개별적으로 만든 쿼리는 다행히 없었다.

단순하게 해당 repository의 구현체를 찾으면 되는것이라 JpaRepository 상속 시 입력한 generic을 통해 찾기로 했다.

### 1. JpaRepository list 받기

JpaRepository 목록을 한 번에 입력 받는건 List로 autowired 해주면 된다. 모든 타입을 받기 위해 와일드카드를 사용했다.

```java

@Service
public class Common {

   private final List<JpaRepository<?, ?>> repositories;

    public Common(List<JpaRepository<?, ?>> repositories){
        this.repositories = repositories;
    }

}

```

### 2. Entity로 repository 찾기

주입받은 Repository list를 for문으로 돌려서 각 repository의 entity Type을 체크했다.

그리고 해당 entity에 매칭되는 repository가 있으면 그 repository를 반환하도록 했다.

```java

 private <T, ID> JpaRepository<T, ID> getEntityRepository(T entity){
        JpaRepository<T, ID> entityRepository = null;
        for(JpaRepository<?, ?> repository : repositories){
            Type[] types = repository.getClass().getGenericInterfaces();
            Optional<Type> result = Arrays.stream(types)
                    .filter(type -> { 
                        boolean isMyRepository = false;
                        Type[] generics = ((Class<?>) type).getGenericInterfaces();
                        for(Type generic : generics){
                            if(generic instanceof ParameterizedType){
                                // getActualTypeArguments의 값이 Generic.  첫번 째 -> entity type , 두번 째 -> key type
                                if(((ParameterizedType) generic).getActualTypeArguments()[0].equals(entity.getClass())){ 
                                    isMyRepository = true;
                                    break;
                                }
                            }
                        }
                        return isMyRepository;
                    })
                    .findFirst();

            if(result.isPresent()){
                entityRepository = (JpaRepository<T, ID>) repository;
                break;
            }
        }

        return entityRepository;
    }

```

### 3. 결론

빠르게 해야해서 급하게 사용한 방법이라 제약사항이 많다. 무조건 JpaRepository를 상속받아야하고

각 repository에서 선언 한 쿼리들은 사용이 불가능하다. 성능에 대한 부분도 확신이 없다 ㅠㅠ

시간있을 때 좀 더 개선해봐야겠다.
