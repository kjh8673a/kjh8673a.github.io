---
layout: post
title: "[스프링] [JPA] Querydsl 방언 사용하기"
date: 2023-07-12 16:00:00 +0900
categories: til
tags: spring
---

**Querydsl Dialect**

<br>

MySQL의 FullText Search를 Querydsl을 이용하여 적용하는 방법을 찾아봤다.

## Dialect

JPA를 사용하면 데이터베이스의 문법을 잘 알지 못해도 hibernate가 설정한 데이터베이스의 쿼리로 바꿔준다.

이 일을 해주는 것이 Dialect이다.

application.properties에 사용하는 DB의 dialect가 설정되어 있다.

```
spring.jpa.database-platform=org.hibernate.dialect.MySQL8Dialect
```

FullText Search는 Dialect에 등록된 함수가 아니었고, 따로 커스텀하여 사용해야 한다.

<br>

## 설정

1. CustomDialect 생성

```java
package com.roller.doc.config;

import org.hibernate.dialect.MySQL8Dialect;

public class MySQL8DialectCustom extends MySQL8Dialect {
}
```

2. application.properties 설정

```
spring.jpa.properties.hibernate.dialect=com.roller.doc.config.MySQL8DialectCustom
```

<br>

## Dialect 구현

사용할 SQL의 함수를 작성한다.

FullText Search 쿼리를 "match"라는 이름으로 작성했다.

다른 함수도 마찬가지로 적용하면 된다.

```java
public class MySQL8DialectCustom extends MySQL8Dialect {
    public MySQL8DialectCustom() {
        super();

        registerFunction(
                "match",
                new SQLFunctionTemplate(StandardBasicTypes.DOUBLE, "match (?1) against (?2)")
        );
    }
}
```

<br>

## 사용

FullText Search하는 부분을 BooleanExpression으로 구현해 BooleanBuilder에 조건으로 추가해주었다.

```java
public BooleanExpression keywordSearch(String word) {
    NumberTemplate booleanTemplate = Expressions.numberTemplate(Double.class,
            "function('match',{0},{1})", hospital.hospital_name, word);

    return booleanTemplate.gt(0);
}
```

위에서 설정한 match라는 이름의 쿼리를 불러와 첫 번째 값에 hospital_name라는 컬럼명을 넣었고, 두 번째 값에 메서드의 파라미터로 받은 값을 검색어로 넣어주었다.
