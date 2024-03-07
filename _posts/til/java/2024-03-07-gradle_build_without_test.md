---
layout: post
title: "[자바] Gradle 테스트 코드 제외하고 빌드하기"
date: 2024-03-07 11:45:00 +0900
categories: til
tags: java
---

**테스트 코드 제외하고 빌드하기**

<br>

프로젝트를 진행하며 사이트를 운영하고 있었다.

새벽시간에 자동으로 빌드를 하게 해놨었는데 빌드되는데 시간이 너무 오래걸려서 ec2서버가 뻗어버리는 일이 있었다.

ec2서버를 더 좋은 등급으로 업그레이드해도 돼겠지만...

빌드할 때 test코드를 실행하는 것이 배포 시간중 많은 양을 잡아먹고있었기 때문에 이를 해결하기로 했다.

gradle을 사용하고 있었고, gradle에서 테스트코드 없이 빌드하는 방법을 찾아 적용했다.

1. build.gradle에 설정 추가하기

```java
// build.gradle
tasks.withType(Test) {
    enabled = false
}
```

2. -x 옵션을 사용해 test 태스크 제외하기

```bash
gradle build -x test
```

두 옵션 중 하나를 적용하면 빌드 시 test태스크가 제외되고, 배포 시간이 단축된다.
