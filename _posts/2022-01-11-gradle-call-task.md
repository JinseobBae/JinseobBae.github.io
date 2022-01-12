---
layout: post
title: "[Gradle] fianlizedBy, dependsOn 이용해서 task 자동 실행시키기"
categories: [gradle]
tags: [gradle, finalizedBy, dependsOn]
---

gradle task를 작성하다보면 특정 task 이 후 다른 task를 실행시키거나

task를 실행하기 전에 특정 task 실행시킬 필요가 있다.

예를들면, 'bootWar를 실행하기전에 반드시 test를 수행한다. test가 성공해야만 bootWar를 수행한다.' 등이 있을 듯 하다.

이런 task의 관계를 `finalizedBy`, `dependsOn` 을 이용해 제어 할 수 있다.

(gradle 6.9(groovy) 버전 기준입니다.)

## finalizedBy

task 이 후 실행시킬 task를 설정해준다. 단, 해당 task의 **성공/실패 여부와 상관없이** 실행시킨다.

`TaskA.finalizedBy('TaskB')` 라고 쓰면 TaskA가 종료된 후 TaskB를 실행시킨다는 의미이다.

`TaskA.finalizedBy('TaskB')` 라고 task 밖에 정의해도 되고 task 내부에 정의 할 수도 있다.

다음과 같은 task 가 있을 때 TaskA가 실행되면 TaskB도 자동으로 실행이 된다.

```gradle

task TaskA {
    doLast{
        println 'TaskA!'
    }
    finalizedBy 'TaskB'
}

task TaskB {
    doLast{
        println 'TaskB!'
    }
}

```
TaskA가 정상적으로 실행되면서 TaskB도 자동으로 실행이 된다.
```
> Task :TaskA
TaskA!

> Task :TaskB
TaskB!

BUILD SUCCESSFUL in 2s
2 actionable tasks: 2 executed

```

TaskA가 실패를 해도 TaskB는 실행이 된다.
```
> Task :TaskA FAILED
TaskA!

> Task :TaskB
TaskB!

...

BUILD FAILED in 2s
2 actionable tasks: 2 executed


```

## dependsOn

task가 실행되기 전에 수행 될 task를 설정해준다. 이전 task가 성공해야 자신이 실행된다.

`TaskA.dependsOn('TaskB')` 라고 쓰면 TaskB를 먼저 실행시키고 성공 하면 TaskA를 실행시킨다는 의미이다.

`finalizedBy`처럼  `TaskA.dependsOn('TaskB')`로 task 밖에 정의해도 되고 task 내부에 정의 할 수도 있다.

```gradle

task TaskA {
    doLast{
        println 'TaskA!'
    }
    dependsOn 'TaskB'
}

task TaskB {
    doLast{
        println 'TaskB!'
    }
}

```

TaskB가 먼저 실행되고 정상적으로 실행면 TaskA가 실행이 된다.
```
> Task :TaskB
TaskB!

> Task :TaskA
TaskA!

BUILD SUCCESSFUL in 2s
2 actionable tasks: 2 executed
```

`finalizedBy`와는 다르게 이전 task가 실행이 되면 자기자신은 실행되지 않는다.
```
> Task :TaskB FAILED
TaskB!

FAILURE: Build failed with an exception.

...

BUILD FAILED in 2s
1 actionable task: 1 executed
```

선행작업인 TaskB가 실패하면서 TaskA는 실행되지 않았다.

1개의 task가 아닌 여러개의 task를 dependsOn으로 설정 할 수도 있다.

```
task TaskA {
    doLast{
        println 'TaskA!'
    }
    dependsOn(provider{
        tasks.findAll { execTask -> execTask.name.startsWith("TaskB")} // Task명 조건
    })
}

```

## 2개를 다 사용하면?

2개를 다 사용 할 수도 있다.

`TaskA.finalizedBy('TaskB')` , `TaskB.dependsOn('TaskA')`

둘 다 똑같이 TaskA -> TaskB 순으로 설정했으니 TaskA 다음 TaskB가 실행이 된다.

만약 TaskA가 실패한다면??

`dependsOn`에 의해서 TaskB는 실행되지 않는다.

---
참고

- [docs.gradle](https://docs.gradle.org/6.8.1/userguide/more_about_tasks.html)
- [stackoverflow](https://stackoverflow.com/questions/45824355/gradle-plugin-task-c-should-run-if-task-a-or-task-b?rq=1)
