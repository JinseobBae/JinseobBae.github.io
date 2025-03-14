---
layout: post
title: "[JAVA] Lombok 호환 버전 정리"
categories: [java]
tags: [java, lombok]
---

현재 업무에서 4가지의 자바 버전을 사용하고 있어 그에 따른 Lombok 버전을 정리한다.

(2024. 12. 05 현황)

| Java 버전 | Lombok 버전 |
|-----------|-------------|
| 23        | 1.18.36     |
| 22        | 1.18.32     |
| 21        | 1.18.30     |
| 20        | 1.18.28     |
| 19        | 1.18.26     |
| 18        | 1.18.24     |
| 17        | 1.18.22     |
| 16        | 1.18.20     |
| 15        | 1.18.16     |
| 14        | 1.18.12     |
| 13        | 1.18.12     |
| 12        | 1.18.10     |
| 11        | 1.18.4      |
| 10        | 1.18.4      |
| 9         | 1.16.20     |
| 8         | 1.16.0      |
| 7         | 0.9.2       |
| 6         | 0.9.2       |


8 같은 경우는 `1.12.6` 버전부터 사용이 가능하지만 버그 수정이 된건 `1.16.0`으로 보인다.

하위호환이 되는것으로 알기 때문에 큰 문제가 없으면 최신버전을 쓰면 될 듯 하다.

참고

- [projectlombok.org/changelog](https://projectlombok.org/changelog)
