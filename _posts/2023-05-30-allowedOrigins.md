---
layout: post
title:  "[트러블슈팅]CORS 설정 시 allowedOrigins 에러 - JAVA"
date:   2023-05-30 09:40:00 +0900
categories: til
---

트러블슈팅

### 에러 내용
```
When allowCredentials is true, allowedOrigins cannot contain the special value "*" 
since that cannot be set on the "Access-Control-Allow-Origin" response header. 
To allow credentials to a set of origins, list them explicitly or consider 
using "allowedOriginPatterns" instead.
```

### 해결

스프링부트 버전 2.4.0부터 allowedOrigins에 "*"을 추가할 수 없다.

allowedOrigins는 특정 도메인만 받을 수 있다.

<code>.allowedOrigins</code> 대신 <code>.allowedOriginPatterns</code>를 사용해야 한다.

allowedOriginPatterns는 "*"같은 와일드카드를 사용할 수 있다.