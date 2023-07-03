---
layout: post
title:  "[MySQL] 전문 검색(FullText Search)"
date:   2023-07-03 22:50:00 +0900
categories: til
---

**FullText Search**

<br>

### 전문 검색(FullText Search)

컬럼에서 키워드로 검색하기위한 방법이다. 단어에서 일부만 일치하는 내용을 검색할 수 있다.

일부만 검색하는 경우에도 FullText인덱스를 사용할 수 있기 때문에 Like문을 이용한 조회보다 더 빠른 검색이 가능하다.

<br><br>

### 인덱싱 방식

인덱싱 방식으로는 Stopword방식과 N-gram방식이 있다.

<br>

#### Stopword

Stopword방식은 내용을 공백, Tab, 문장 기호를 구분자로 하며, 사용자가 정의한 문자열을 구분자로 할 수도 있다.

공백을 기준으로 나눈 단어들을 저장하여 완전히 일치한 단어만 결과에 포함된다.

예를 들어 테이블에 다음과 같이 저장이 되어있다고 하자.

1. 우리 집 강아지는 복슬강아지

2. 우리집 강아지는 복슬강아지

3. 우리집강아지는복슬강아지

"우리"이라는 키워드가 포함된 내용을 조회하려고 하면 첫 번째 문장만 결과로 나오게 된다.

1. 우리 집 강아지는 복슬강아지

공백을 기준으로 단어를 나누기 때문에 완전히 일치한 결과만 나오게 된다.

"우리"으로 검색했을 때 "우리집"이라는 결과도 나오게 하기 위해서는 N-gram방식을 사용해야 한다.

<br>

#### N-gram

N-gram방식은 내용을 무조건적으로 몇 글자씩 잘라 저장하는 방식이다.

일반적으로 2글자 단위로 쪼개는 2-Gram방식이 많이 사용된다.

이를 토큰 사이즈라고 하는데 토큰 사이즈의 default값이 2로 설정되어 있다.

"우리집 강아지는 복슬강아지" 라는 내용에 2-Gram방식을 적용하여 처리하면

"우리", "리집", "강아", "아지", "지는", "복슬", "슬강", "강아", "아지" 와 같이 2글자 씩 잘려서 저장된다.

이렇게 되면 "우리"라는 키워드로 검색했을 때도 해당 내용이 검색 결과로 나오게 된다.

<br><br>

### 인덱스 설정

전문 검색을 이용하기 위해서는 FullText인덱스를 설정해주어야 한다.

테이블 생성 후 ALTER문을 이용해 설정할 수도 있고,

테이블 생성 시 CREATE INDEX문을 이용해 설정할 수도 있다.

```SQL
ALTER TABLE table ADD FULLTEXT INDEX ft_index (column) WITH PARSER ngram;
```

```SQL
CREATE FULLTEXT INDEX ft_index ON table (column) WITH PARSER ngram;
```

<br><br>

### 검색 모드

검색 모드로는 자연어 모드(natural language mode)와 불린 모드(boolean mode)가 있다.

<br>

#### 자연어 모드

자연어 검색은 입력된 검색어에서 키워드를 추출하여 키워드를 포함하는 결과를 검색하는 방법이다.

검색어의 키워드가 얼마나 많이 포함돼 있는지에 따라 매치율이 결정된다.

```SQL
SELECT * FROM table WHERE MATCH(column) AGAINST ('word' IN NATURAL LANGUAGE MODE);
```

<br>

### 불린 모드

불린 모드 검색은 각 키워드의 포함, 불포함 여부를 TRUE, FALSE로 반환하여 최종 일치 여부를 판단하는 방식이다.

각 키워드의 포함 여부를 AND, OR, NOT 연산자로 연산하여 최종적으로 TRUE인 결과만 검색 결과로 출력된다.

연산자로는 "+", "-"가 있는데 각각 AND, NOT을 의미한다.

```SQL
SELECT * FROM table WHERE MATCH(column) AGAINST ('+word1 +word2' IN BOOLEAN MODE);

SELECT * FROM table WHERE MATCH(column) AGAINST ('+word1 -word2' IN BOOLEAN MODE);

SELECT * FROM table WHERE MATCH(column) AGAINST ('word1 word2' IN BOOLEAN MODE);
```

연산자를 사용하지 않았다면 OR 연산이 적용되어 word1이나 word2 중 하나라도 포함하는 결과를 가져온다.






