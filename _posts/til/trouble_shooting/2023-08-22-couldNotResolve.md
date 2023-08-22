---
layout: post
title: "[트러블슈팅] Could not resolve org.springframework.boot:spring-boot-gradle-plugin:3.1.2"
date: 2023-08-22 17:09:00 +0900
categories: til
tags: trouble_shooting
---

**Could not resolve org.springframework.boot:spring-boot-gradle-plugin:3.1.2**

### 에러 내용

```
A problem occurred configuring root project 'projectName'.
> Could not resolve all files for configuration ':classpath'.
   > Could not resolve org.springframework.boot:spring-boot-gradle-plugin:3.1.2.
     Required by:
         project : > org.springframework.boot:org.springframework.boot.gradle.plugin:3.1.2

```

### 해결

프로젝트를 클론받아 IntelliJ에서 빌드하였는데 위와 같은 에러가 발생했다.

spring boot 3.x 버전은 JDK 17 이상을 사용해야 한다고 한다.

설정을 확인해보니 JDK 11이 깔려있어 JDK 17을 설치하고 다시 빌드하니 성공적으로 빌드되었다.

**IntelliJ에서 해결 방법**

1. File -> Settings

2. 설정창에서 Build, Execution, Deployment -> Build Tools -> Gradle

3. 하단에 Gradle JVM 의 옵션을 JDK 17 이상으로 설정하고 적용한다.

4. JDK 17 이상이 설치되어 있지 않다면 JVM 옵션을 선택하는 창에서 Download JDK를 선택해 17 버전 이상을 받아준다.
