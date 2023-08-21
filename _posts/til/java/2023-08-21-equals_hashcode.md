---
layout: post
title: "[자바] equals() & hashCode() 재정의"
date: 2023-08-21 17:17:00 +0900
categories: til
tags: java
---

**equals() & hashCode() 재정의**

<br>

알고리즘을 풀던 중 Node라는 class를 만들어 set에 넣어 contains 메서드를 이용해 비교하려고했다.

객체를 비교하기 위해서는 클래스에 equals와 hashCode 메서드를 재정의해야했다.

```java
static class Node {
    int a;
    int b;

    public Node(int a, int b) {
        this.a = a;
        this.b = b;
    }
}

public static void main(String[] args) throws IOException {
    Node left = new Node(1, 2);
    Node right = new Node(1, 2);

    System.out.println(Objects.equals(left, right));
}
```

위와 같이 객체에 같은 값을 담고 Objects의 equals 메서드를 이용해 비교하려고 하면 false가 출력된다.

두 객체의 주소값이 다르기 때문이다. 두 객체를 같게 하려면 Node클래스에 equals메서드를 재정의해야한다.

```java
static class Node {
    int a;
    int b;

    public Node(int a, int b) {
        this.a = a;
        this.b = b;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }

        if (!(o instanceof Node)) {
            return false;
        }

        Node node = (Node) o;
        return this.a == node.a && this.b == node.b;
    }
}
```

위와 같이 equals 메서드를 재정의하면 Objects의 equals 메서드로 비교할 때 같은 값이면 true가 출력된다.

즉, Integer나 String같은 값을 표현하는 클래스에서 값이 같은지 비교하고 싶을 때 equals 메서드를 재정의하여 비교할 수 있다.

equals 메서드 구현 방법은 다음과 같다.

```
1. == 연산자를 사용해 자기 자신의 참조인지 확인한다.

2. instanceof 연산자로 타입이 맞는지 확인한다.

3. 입력된 값을 타입에 맞게 형변환한다.

4. 입력 값과 자기 자신의 값들을 대응하여 하나씩 검사한다.
```

equals 메서드만 재정의하면 Hash함수를 사용하는 Collection들에서는 비교가 되지 않는다.

```java
public static void main(String[] args) throws IOException {
    Node left = new Node(1, 2);
    Node right = new Node(1, 2);

    Set<Node> set = new HashSet<>();
    set.add(left);

    System.out.println(set.contains(right));
}
```

위와 같이 set에 넣고 contains 메서드를 사용해 값을 비교하면 true가 나와야 할 것 같지만 false가 나온다.

값을 같지만 서로 다른 해시값을 갖게 되기 때문이다.

마찬가지로 Hash를 사용하는 Map의 경우에도 비교가 되지 않는다.

이를 해결하기 위해서는 클래스에 hashCode 메서드를 추가로 재정의해줘야 한다.

hashCode 메서드를 재정의하면 같은 값을 가진 객체가 같은 해시값을 갖게 된다.

```java
static class Node {
    int a;
    int b;

    public Node(int a, int b) {
        this.a = a;
        this.b = b;
    }

    // equals 재정의
    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }

        if (!(o instanceof Node)) {
            return false;
        }

        Node node = (Node) o;
        return this.a == node.a && this.b == node.b;
    }

    // hashCode 재정의
    @Override
    public int hashCode() {
        return Objects.hash(a, b);
    }
}
```
