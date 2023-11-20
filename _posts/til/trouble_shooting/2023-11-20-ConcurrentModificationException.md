---
layout: post
title: "[트러블슈팅] ConcurrentModificationException"
date: 2023-11-20 11:30:00 +0900
categories: til
tags: trouble_shooting
---

**ConcurrentModificationException**

### 내용

```java
private static void dfs(int node, List<Integer> next, List<List<Integer>> list) {

    next.removeIf(item -> item == node);

    for (int nextNode : list.get(node)) {
        next.add(nextNode);
    }

    for (int nextNode : next) {
        dfs(nextNode, next, list);
    }
}
```

```
Exception in thread "main" java.util.ConcurrentModificationException
        at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1042)
        at java.base/java.util.ArrayList$Itr.next(ArrayList.java:996)
        at Main.dfs(Main.java:29)
        at Main.dfs(Main.java:30)
        at Main.main(Main.java:18)
```

dfs탐색에서 list의 요소를 삭제하려고 할 때 발생했다.

ConcurrentModificationException은 동시성 문제에서 발생하는 문제였다. 즉, 서로 다른 쓰레드가 간섭하면서 발생하는 것이다.

List나 Map 등 객체를 순회하면서 요소를 삭제하거나 변경할 때 발생한다.

### 해결

- 해결 1: 컬렉션을 복사하여 사용하기

  한 컬렉션에 여러 쓰레드가 동시에 수정을 할 때 발생하는 문제이므로 임시 컬렉션을 만들어 복사해서 처리해보았다. 성공적으로 실행됐다.

  ```java
  private static void dfs(int node, List<Integer> next, List<List<Integer>> list) {

      ArrayList<Integer> tmp = new ArrayList<>();
      tmp.addAll(next);
      tmp.removeIf(item -> item == node);

      for (int nextNode : list.get(node)) {
          tmp.add(nextNode);
      }

      for (int nextNode : tmp) {
          dfs(nextNode, tmp, list);
      }
  }
  ```

- 해결 2: 스트림으로 새로운 컬렉션 얻기

  스트림 API를 이용해 원본 컬렉션에 변경을 가하지 않고 새로운 컬렉션을 얻을 수 있다.

  ```java
  private static void dfs(int node, List<Integer> next, List<List<Integer>> list) {
      next = next.stream()
              .filter(item -> item != node)
              .collect(Collectors.toList());

      for (int nextNode : list.get(node)) {
          next.add(nextNode);
      }

      for (int nextNode : next) {
          dfs(nextNode, next, list);
      }
  }
  ```

- 해결 3: CopyOnWriteArrayList사용하기

  자바 1.5부터 안전한 쓰레드 처리를 위해 나온 라이브러리이다. 내부를 변경하는 작업에 항상 복사본을 만들어서 수행하도록 구현되어 있다.

  객체를 매번 복사하지 않고, 전달할 때 해당 상태를 스냅샷으로 가지고 있는 방식이다.

  ```java
  List<Integer> next = new CopyOnWriteArrayList<>();
  ```



