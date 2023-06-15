---
layout: post
title: "[JAVA]Math.floorDiv & Math.floorMod"
date: 2023-06-15 16:00:00 +0900
categories: til
---

**Math.floorDiv & Math.floorMod**

### 내용

"음수 % 양수" 의 값이 음수로 나와 양수로 바꿔줄 필요가 있었다.

Math클래스의 floorMod라는 연산을 찾았다.

```java
public static int floorMod(int x, int y) {
        return x - floorDiv(x, y) * y;
    }
```

floorMod를 타고 들어가보니 floorDiv에 대해 먼저 알아야 했다.

```java
public static int floorDiv(int x, int y) {
        int r = x / y;
        // if the signs are different and modulo not zero, round down
        if ((x ^ y) < 0 && (r * y != x)) {
            r--;
        }
        return r;
    }
```

floorDiv는 "x / y"의 몫을 구하는데  x와 y의 부호가 다를 경우 처리하는 로직이 여기에 있다.

<code>(x ^ y) < 0</code>가 두 수의 부호가 다른 것을 판단한다.

숫자를 이진수로 나타냈을 때 최상위 비트가 1이면 음수, 0이면 양수이다.

따라서, XOR연산으로 부호가 다름을 표현할 수 있다.

floorDiv에서 x와 y의 부호가 다를 경우 몫에 1을 뺀 값을 반환한다.

따라서 floorDiv에서도 양수인 나머지를 구할 수 있다.


