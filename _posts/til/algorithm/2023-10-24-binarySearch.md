---
layout: post
title: "[알고리즘] 이분탐색 - JAVA"
date: 2023-10-24 10:17:00 +0900
categories: til
tags: algorithm
---

**이분탐색 BinarySearch**

1. [이분탐색](#이분탐색)
2. [Lower Bound](#Lower-Bound)
3. [Upper Bound](#Upper-Bound)

---

### 이분탐색

이분탐색은 탐색할 대상이 정렬된 상태에서 사용한다.

데이터의 특정 값을 찾기 위해 대상을 둘로 나눠 절반씩 줄여나가는 원리이다.

대상을 반씩 줄이기 때문에 시간복잡도는 O(logN)이다.

주어진 데이터에서 중복되지 않은 값이 주어질 때, 데이터 내에 특정 값이 존재하는지 여부를 확인하는 방법으로 많이 사용한다.

동작 방식은 다음과 같다.

1. 배열의 중간 값을 가져온다.

2. 중간 값과 검색 값을 비교한다.
    - 중간 값이 검색 값과 같다면 종료한다. (mid = key)
    - 중간 값보다 검색 값이 크다면 중간 값 기준 오른쪽 구간을 탐색한다.(right = mid-1)
    - 중간 값보다 검색 값이 작다면 중간 값 기준 왼쪽 구간을 탐색한다.(left = mid+1)

3. 값을 찾거나(arr[mid] == key) 간격이 비어있을 때(left > right) 까지 반복한다.

```java
private static int binarySearch(int[] arr, int l, int r, int key) {
    int mid;
    while(l <= r) {
        mid = (l + r) / 2;
        if(arr[mid] == key) {
            return mid;
        }

        if(arr[mid] < key) {
            l = mid + 1;
        }else {
            r = mid - 1;
        }
    }

    return mid;
}
```

### Lower Bound

Lower Bound와 Upper Bound는 경곗값을 찾는 알고리즘이다.

이분탐색 알고리즘을 기반으로 하기 때문에 데이터가 정렬되어 있어야 한다.

Lower Bound는 데이터 내 특정 key값보다 **같거나 큰 값**이 처음 나오는 위치를 리턴해주는 알고리즘이다.

중복되는 원소와 상관 없이 찾고자 하는 값 이상이 처음 나타나는 위치를 찾는다.

right값은 array.length로 잡는다.

동작 방식은 다음과 같다.

1. 배열의 중간 값(mid)을 가져온다.

2. 중간 값과 검색 값을 비교한다.

   - 중간 값이 검색 값보다 작다면 left값을 mid+1로 한다.
   - 중간 값이 검색 값보다 크거나 같다면 right값을 mid로 한다.

3. left >= right 일때까지 1, 2를 반복한다.

4. 반복이 끝나면 right값이 lower bound가 된다.

```java
private static int lowerBound(int[] arr, int l, int r, int key) {
    int mid;
    while(l < r) {
        mid = (l + r) / 2;
        if(arr[mid] < key) {
            l = mid + 1;
        }else {
            r = mid;
        }
    }

    return r;
}
```

### Upper Bound

Lower Bound는 데이터 내 특정 key값보다 처음으로 큰 값이 나오는 위치를 리턴해주는 알고리즘이다.

right값은 array.length로 잡는다.

동작 방식은 다음과 같다.

1. 배열의 중간 값(mid)을 가져온다.

2. 중간 값과 검색 값을 비교한다.

   - 중간 값이 검색 값보다 작거나 같다면 left값을 mid+1로 한다.
   - 중간 값이 검색 값보다 크다면 right값을 mid로 한다.

3. left >= right 일때까지 1, 2를 반복한다.

4. 반복이 끝나면 right값이 lower bound가 된다.

```java
private static int upperBound(int[] arr, int l, int r, int key) {
    int mid;
    while(l < r) {
        mid = (l + r) / 2;
        if(arr[mid] <= key) {
            l = mid + 1;
        }else {
            r = mid;
        }
    }

    return r;
}
```
