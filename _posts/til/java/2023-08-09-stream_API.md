---
layout: post
title: "[자바] Stream API"
date: 2023-08-09 16:19:00 +0900
categories: til
tags: java
---

**Stream API**

<br>

## Stream API

자바8부터 Stream API와 람다식 등을 지원하면서 자바를 이용해 함수형으로 프로그래밍할 수 있게 API를 제공해주고 있다. Stream API는 데이터의 종류에 상관없이 같은 방식으로 데이터를 처리할 수 있어 재사용성을 높일 수 있다.

Stream API는 원본 데이터를 변경하지 않고, 일회용으로 한번 사용하면 재사용이 불가능하다. 또한, 내부 반복으로 작업을 처리하는 특징이 있다.

Stream API를 사용하지 않은 경우에는 다음과 같이 배열과 리스트를 만들고 사용했다.

```java
String[] arr = {"Dog", "Cat", "Penguin", "Lion"};
List<String> list = Arrays.asList(arr);

// 정렬: 원본 데이터가 정렬됨
Arrays.sort(arr);
Collections.sort(list);

// 출력
for(String str : arr) {
	System.out.println(str);
}
for(String str : list) {
	System.out.println(str);
}
```

Stream API를 사용하면 코드를 간략하게 작성할 수 있고, 원본 데이터의 변경없이 정렬이 가능하다.

```java
String[] arr = {"Dog", "Cat", "Penguin", "Lion"};
List<String> list = Arrays.asList(arr);

// 정렬: 별도의 Stream을 생성하여 정렬함
Stream<String> arrStream = Arrays.stream(arr);
Stream<String> listStream = Arrays.stream(list);

// 출력
arrStream.sorted().forEach(System.out::println);
listStream.sorted().forEach(System.out::println);
```

### Stream API 연산 과정

Stream API는 3가지 단계로 구성된다. Stream을 만드는 생성과정과 중간 연산, 최종 연산으로 이루어진다.

- **생성**
  Stream 객체를 생성해준다. 배열, 컬렉션 등 거의 모든 것을 스트림으로 생성할 수 있다. 다만 Stream은 재사용이 불가능하다. 연산이 끝나면 Stream이 닫리기 때문에, 다시 Stream을 생성해야한다.
- **중간 연산**
  원본 데이터를 별도의 데이터로 가져와 가공한다. 연산 결과를 Stream으로 반환하여 추가적인 중간연산을 진행할 수도 있다.

- **최종 연산**
  중간연산으로 부터 받은 가공된 Stream을 원하는 결과로 만들기 위한 최종 연산이다. Stream을 소모하여 1번만 처리 가능하다.

### 생성

Stream API를 사용하기 위해서 Stream을 먼저 생성해야 한다. 타입에 따라 Stream을 생성하는 방법이 다르다.

- **Collection의 Stream 생성**

  Collection 인터페이스에는 stream()이 정의되어있다. 따라서 이 메서드를 이용해 Stream을 생성할 수 있다.

  ```java
  List<String> list = Arrays.asList("Dog", "Cat", "Penguin", "Lion");
  Stream<String> stream = list.stream();
  ```

- **배열의 Stream 생성**

  배열을 Stream으로 생성하려면 Stream의 of 메서드나 Arrays의 stream 메서드를 사용한다.

  ```java
  Stream<String> stream = Stream.of("Dog", "Cat", "Penguin", "Lion");
  Stream<String> stream = Stream.of(new String[] {"Dog", "Cat", "Penguin", "Lion"});
  Stream<String> stream = Arrays.stream(new String[] {"Dog", "Cat", "Penguin", "Lion"});

  // 범위지정: 1~2요소 선택
  Stream<String> stream = Arrays.stream(new String[] {"Dog", "Cat", "Penguin", "Lion"}, 1, 3);
  ```

- **원시 Stream 생성**

  int, long, double과 같은 원시 타입의 자료형들을 위한 특수한 Stream들이 있다.(IntStream, LongStream, DoubleStream) IntStream의 경우 range()를 이용하여 기존의 for문을 대체할 수 있다.

  ```java
  IntStream stream = IntStream.range(4, 10);
  ```

### 중간 연산

중간 연산을 거쳐 생성한 Stream들이 가공된다. 중간 연산은 Stream으로 반환되며 여러 개를 적용할 수도 있다.

- **필터링 - Filter**

  Filter는 Stream 요소들을 조건에 맞게 걸러내어 더 작은 컬렉션을 만드는 작업이다. 인자로 함수형 인터페이스 Predicate를 받고 있기 때문에 boolean을 반환하는 람다식으로 filter 함수를 작성할 수 있다.

  ```java
  Stream<String> stream = list.stream().filter(name -> name.contains("a"));
  ```

- **데이터 변환 - Map**

  Map은 Stream의 요소들을 하나씩 변환하여 새로운 Stream을 형성한다. 저장된 값을 특정한 형태로 바꾸는데 사용된다.
  예를 들어, String을 요소로 갖는 Stream을 대문자로 변환할 수 있다.

  ```java
  Stream<Stream> stream = names.stream().map(s -> s.toUpperCase());
  Stream<Stream> stream = names.stream().map(String::toUpperCase);
  ```

  또한, 요소에 들어있는 이름, 수량같은 정보들을 모아 Stream으로 만들 수도 있다.

  ```java
  Stream<Integer> amountStream = list.stream().map(Product::getAmount);
  Stream<Integer> nameStream = list.stream().map(Product::getName);
  ```

- **정렬 - Sorted**

  Stream의 요소들을 정렬하기 위해 sorted를 사용한다. 인자로 Comparator를 넘길 수도 있고, 인자없이 호출한 경우 오름차순으로 정렬된다.

  ```java
  // 오름차순 정렬
  Stream<String> stream = list.stream().sorted();

  // 내림차순 정렬
  Stream<String> stream = list.stream().sorted(Comparator.reverseOrder());
  ```

- **중복 제거 - Distinct**

  Stream의 요소들 간 중복을 제거하기 위해 distinct를 사용한다. Object의 equals() 메서드를 이용하여 비교한다. 기본 타입의 경우 값(value)으로 비교한다.

  ```java
  List<String> list = Arrays.asList("Dog", "Lion", "Cat", "Lion", "Penguin", "Lion");

  Stream<String> stream = list.stream().distinct();
  // [Dog, Lion, Cat, Penguin]
  ```

  생성한 클래스를 Stream으로 사용하기 위해서는 equals, hashCode를 오버라이드해야 ditinct()를 제대로 사용할 수 있다. 오버라이드하지 않을 경우 중복된 데이터로 체크되지 않는다.

- **특정 연산 수행 - Peek**

  Stream에 영향을 주지 않고 특정 연산을 수행하기 위한 함수 peek이다. 원본 Stream에서 요소를 소모하지 않기 때문에 중간 연산 사이의 결과를 확인할 때 유용하다. 다만, 중간 연산이기 때문에 최종 연산이 수행되지 않으면 처리되지 peek도 처리되지 않는다.

  ```java
  int sum = IntStream.of(1, 3, 5, 6, 9)
  		.peek(System.out::println)
  		.sum();
  ```

### 최종 연산

중간연산이 반환한 Stream을 받아 최종적으로 결과를 만든다

- \***\*최댓값/최솟값/총합/평균/개수 - Max/Min/Sum/Average/Count\*\***

  최댓값, 최솟값을 구하기위해 max와 min을, 총합, 평균, 개수를 구하기 위해 sum, average, count를 이용한다.
  min, max, average는 Stream이 비어있는 경우 값을 특정할 수 없기 때문에 Optional로 값이 반환된다.

  ```java
  OptionalInt min = IntStream.of(1, 3, 4, 5, 6).min();
  int max = IntStream.of().max().orElse(0);
  IntStream.of(1, 2, 5, 6, 7).average().ifPresent(System.out::println);
  ```

  sum, count의 경우 Stream이 비어있는 경우에도 0으로 값을 특정할 수 있다. 따라서, sum, count메서드는 Optional이 아닌 원시값을 반환한다.

  ```java
  int count = IntStream.of(1, 3, 5, 6, 10).count();
  long sum = LongStream.of(4, 5, 6, 8, 12, 32).sum();
  ```

- **데이터 수집 - collect**

  Stream의 요소들을 List, Set, Map 등 다른 종류의 결과로 변환하기 위해 collect를 이용한다. Stream의 요소들을 어떻게 처리할 것인지를 정의한 Collector 타입을 인자로 받아 처리한다.

  - **Collectors.toList()**
    Stream의 결과를 List로 반환한다.

    ```java
    List<String> nameList = list.stream()
    		.map(Food::getName) // name 얻기
    		.collect(Collectors.toList()); // list로 수집
    ```

  - **Collectors.joining()**

    Stream의 결과를 1개의 String으로 이어붙이기 위한 메서드이다. delimiter(중간에 들어가 요소를 구분), prefix(맨 앞에 붙는 글자), suffix(맨 뒤에 붙는 글자)의 3개 인자를 받을 수 있다.

    ```java
    String joining = list.stream()
    		.map(Food::getName).collect(Collectors.joining());
    // burgerchipscokesoda

    String joining = list.stream()
    		.map(Food::getName).collect(Collectors.joining(" "));
    // burger chips coke soda

    String joining = list.stream()
    		.map(Food::getName).collect(Collectors.joining(", ", "<", ">"));
    // <burger, chips, coke, soda>
    ```

  - \***\*Collectors.averagingInt(), Collectors.summingInt(), Collectors.summarizingInt()\*\***

    Stream 결과의 평균, 총합을 구하기 위해 Collectors.averagingIng(), Collectors.summingInt()를 사용한다.

    ```java
    Double averageAmount = productList.stream()
    		.collect(Collectors.averagingInt(Product::getAmount));

    Integer summingAmount = productList.stream()
    		.collect(Collectors.summingInt(Product::getAmount));
    ```

    Collectors.summarizingInt()를 사용하면 개수, 합계, 평균, 최대, 최소를 한 번에 얻을 수 있다. get 메서드를 이용해 원하는 값을 꺼낼 수 있다.(getCount(), getSum(), getAverage(), getMin(), getMax())

    ```java
    IntSummaryStatistics statistics = productList.stream()
    		.collect(Collectors.summarizingInt(Product::getAmount));

    //IntSummaryStatistics {count=5, sum=86, min=13, average=17.200000, max=23}
    ```

  - \***\*Collectors.groupingBy()\*\***

    Stream의 요소들을 특정 조건으로 그룹화할 수 있다. 결과는 Map으로 반환받게 된다. groupingBy는 매개변수로 함수형 인터페이스 Function을 필요로 한다.

    ```java
    Map<Integer, List<Food>> calMap = list.stream()
            .collect(Collectors.groupingBy(Food::getCal));

    // { 230=[name: chips, cal: 230],
    //   520=[name: burger, cal: 520],
    //   143=[name: coke, cal: 143, name: soda, cal: 143]}
    ```

  - \***\*Collectors.partitioningBy()\*\***

    Stream의 요소들을 특정 조건의 참 거짓에 따라 2개의 그룹으로 나눌 수 있다. partitioningBy는 함수형 인터페이스 Predicate를 받아 boolean값을 통해 그룹을 나눈다.

    ```java
    Map<Boolean, List<Food>> partitionMap = list.stream()
            .collect(Collectors.partitioningBy(o -> o.getCal() > 200));

    // { false=[name: coke, cal: 143, name: soda, cal: 143],
    //   true=[name: burger, cal: 520, name: chips, cal: 230]}
    ```

- **조건 검사 - Match**

  Stream의 요소들이 특정 조건을 충족하는지 검사할 때 사용한다. match는 함수형 인터페이스 Predicate을 받아 검사하고, 결과를 boolean으로 반환한다.
  match함수에는 anyMatch, allMatch, noneMatch의 3가지가 있다.

  ```java
  List<String> names = Arrays.asList("Dog", "Cat", "Penguin", "Lion");

  boolean anyMatch = names.stream()
      .anyMatch(name -> name.contains("a"));
  boolean allMatch = names.stream()
      .allMatch(name -> name.length() > 3);
  boolean noneMatch = names.stream()
      .noneMatch(name -> name.endsWith("n"));
  ```

- **특정 연산 수행 - forEach**

  Stream의 요소들을 대상으로 특정 연산을 수행하고 싶을 때 사용한다. 중간 연산에서의 peek()과 비슷하다. peek()은 중간 연산으로 실제 요소들에 영향을 주지 않은 채 작업하고, forEach()는 최종연산으로 실제 요소들에 영향을 줄 수 있고, 반환값이 존재하지 않는다.

  ```java
  names.stream()
  		.forEach(System.out::println);
  ```
