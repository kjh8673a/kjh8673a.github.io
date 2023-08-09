---
layout: post
title: "[트러블슈팅] More than one row with the given identifier was found"
date: 2023-06-25 18:55:00 +0900
categories: til
tags: trouble_shooting
---

**More than one row with the given identifier was found**

### 에러 내용

```
ERROR 1092 --- [nio-8080-exec-1] c.r.d.api.service.drug.DrugServiceImpl   : More than one row with the given identifier was found: 18304, for class: com.roller.doc.db.entity.DrugDesc; nested exception is org.hibernate.HibernateException: More than one row with the given identifier was found: 18304, for class: com.roller.doc.db.entity.DrugDesc
```

### 해결

OneToOne으로 매핑 시켜놨는데 테이블 조회 시 연관된 테이블에 중복 데이터가 있어서 발생한 에러였다.

OneToMany를 사용하여 해결할 수도 있지만, DB 설계상 OneToOne으로 하는 것이 맞았다.

DB에서 중복된 데이터를 찾아 삭제해 주었다.

아래는 DB에서 중복된 데이터를 조회하는 쿼리이다.

```
SELECT distinct count(*), drug_id FROM drug_desc GROUP BY drug_id HAVING count(*) > 1 ;
```

중복된 데이터를 찾아 삭제해 주었다.
