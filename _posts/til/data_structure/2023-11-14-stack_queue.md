---
layout: post
title: "[자료구조] 스택 & 큐"
date: 2023-11-14 11:49:00 +0900
categories: til
tags: data_structure
---

**Stack & Queue**

<br>

## 스택

Stack 클래스에서 구현된 메서드는 다음과 같다.

- E push(E item):
  스택의 맨 위에 요소를 추가한다.

- E pop():
  스택의 맨 위 요소를 제거하고 제거된 요소를 반환한다.

- E peek():
  스택의 맨 위 요소를 제거하지 않고 반환한다.

- boolean empty():
  현재 스택에 요소가 존재하지 않을 경우 true, 그 외의 경우 false를 반환한다.

- int search(Object o):
  스택의 상단부터 탐색하여 지정된 객체가 있는 요소의 위치를 반환한다. 없을 경우 -1을 반환한다.

스택은 먼저 들어온 데이터가 마지막에 나가는 구조이다.

페이지 뒤로가기, 실행 취소, 수식 괄호검사 등에서 응용된다.

Stack 클래스는 Vector 클래스를 상속받아 구현되어있다.

Stack > Vector > AbstractList > AbstractCollection > Collection > Iterable

하지만 Stack이 Vector를 상속받는다는 것은 스택을 구현하기 위해 작성된 Stack클래스의 의미를 상실하게 했다.

Vector의 상속으로 Stack에서는 허용되지 않을 중간에 요소 추가, 중간에 있는 요소 조회 및 삭제를 비롯한 온갖 동작을 할 수 있게 되었기 때문이다.

Vector는 ArrayList와 유사하나 Thread 동기화를 위해 synchronized 키워드가 선언되어 있다.

시용자가 필요에 따라 thread-safe하게 묶어서 수행할 수 있어야 성능적인 튜닝이 가능한데, Vector구조에서는 Iterator를 통해 내부의 여러 값들을 참조할 때 각각을 get하는 과정에서 락을 얻는 과정이 일일이 수행된다.

따라서 Vector는 특정 상황에서만 최적으로 동작하고, 어떤 상황에서는 그렇게 되지 않으므로 효율적인 Thread-safe 컬렉션이라고 할 수 없다.

결론: Stack을 사용하지말자. Stack대신 Deque의 하위컬렉션이나 ArrayList를 사용하는 것이 적절하다.

<br>

## 큐

Queue 인터페이스에서 구현된 메서드는 다음과 같다.

- boolean add(E e):
  큐의 마지막에 요소를 추가한다.

- boolean offer(E e):
  큐의 마지막에 요소를 추가한다.

- E remove():
  큐의 첫 번째 요소를 제거하고 제거된 요소를 반환한다.

- E poll():
  큐의 첫 번째 요소를 제거하고 제거된 요소를 반환한다.

- E element():
  큐의 첫 번째 요소를 제거하지 않고 반환한다.

- E peek():
  큐의 첫 번째 요소를 제거하지 않고 반환한다.

add(), remove(), element()는 내부적으로 예외를 처리하고 있다.

offer(), poll(), peek()의 경우 예외를 던지는 것이 아니라 특별한 값을 던지는데, 일반적으로 null, false를 던진다.

큐는 '대기열'과 같다. 선입선출 자료구조로 먼저 들어온 요소가 먼저 나간다.

시간 순으로 처리해야하는 것들은 큐로 처리할 수 있다. 대표적으로 BFS에 사용된다.

Queue는 Collection 인터페이스를 상속받는다.

Collection을 상속받는 인터페이스로 List, Set, Queue가 있는데 이들은 인터페이스로 스스로 객체 생성이 불가능하다.
