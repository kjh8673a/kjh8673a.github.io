---
layout: post
title: "[자바] JUnit을 이용한 단위 테스트"
date: 2023-08-28 15:25:00 +0900
categories: til
tags: java
---

**JUnit을 이용한 단위 테스트**

<br>

JUnit이란 자바의 대표적인 단위 테스트를 하는 프레임워크이다.

JUnit의 버전 중 하나인 JUnit5와 AssertJ 라이브러리가 사용되는데

AssertJ는 자바 테스트를 돕기 위해 다양한 문법을 지원한다.

### Assert 구문

테스트에서 기대값과 출력값을 비교하는 구문은 Assert이다. 변수 타입의 종류가 다양하기 때문에 여러 종류의 Assert 구문을 지원하고 있다.

| 메서드                              | 내용                                       |
| ----------------------------------- | ------------------------------------------ |
| assertEquals(expected, actual)      | 두 객체의 equals 결과가 참인지 검사한다.   |
| assertTrue(actual)                  | 계산 결과가 참인지 검사한다.               |
| assertFalse(actual)                 | 계산 결과가 거짓인지 검사한다.             |
| assertNotNull(actual)               | 계산 결과가 null이 아닌지 검사한다.        |
| assertNull(actual)                  | 계산 결과가 null인지 검사한다.             |
| assertSame(expected, actual)        | 두 객체가 동일 객체인지 검사한다.          |
| assertNotSame(expected, actual)     | 두 객체가 동일 객체가 아닌지 검사한다.     |
| fail("Test Fail")                   | 테스트를 실패시킨다.                       |
| assertArrayEquals(expected, actual) | 두 개의 배열의 equals값이 참인지 검사한다. |

<br>

### 테스트 예시

단위 테스트 코드 예시는 [https://mangkyu.tistory.com/144](https://mangkyu.tistory.com/144)의 코드를 참고했다.

<br>

**given/when/then 패턴**

단위 테스트를 given-when-then 패턴으로 작성하는데, 각각의 단계는 다음을 의미한다.

- given(준비): 어떠한 데이터가 준비되었을 때

- when(실행): 어떠한 함수를 실행하면

- then(검증): 어떠한 결과가 나와야 한다.

<br>

**로또 생성기 Java 코드**

1000원을 입력하면 난수 6개를 반환하는 클래스이다.

```java
public class LottoGenerator {

	public List<Integer> generate(int money) {
		if(!isValidMoney(money)) {
			throw new RuntimeException("올바른 금액이 아닙니다.");
		}

		return generate();
	}

	private List<Integer> generate() {
		return new Random()
			.ints(1, 45 + 1)
			.distinct()
			.limit(6)
			.boxed()
			.collect(Collectors.toList());
	}

	private boolean isValidMoney(int money) {
		return money == 1000;
	}
}
```

<details>
<summary>[로또 생성기 코드 공부]</summary>
<div markdown="1">

1000이 아닌 다른 수가 입력됐을 때는 강제로 예외를 발생시킨다.

```java
if(!isValidMoney(money)) {
    throw new RuntimeException("올바른 금액이 아닙니다.");
}
```

generate 메서드에서는 1~45까지의 숫자 6개를 중복없이 뽑아 리스트로 만들어 반환한다.

Random클래스의 난수 스트림을 생성하는 방법으로 <code>Random.ints(start, end + 1)</code>를 사용했고,

중복을 없이 뽑기 위해 <code>distinct()</code>를, 개수 제한을 위해 <code>.limit(num)</code>를 사용했다.

int 자체로는 Collection에 담지 못하기 때문에 <code>boxed()</code>를 사용하여 박싱과정을 거쳐 list에 담았다.

</div>
</details>

<br>

**1. 로또 번호 개수 테스트**

```java
@Test
void lottoSizeTest() {
    // given
    final LottoGenerator lottoGenerator = new LottoGenerator();
    final int price = 1000;

    // when
    final List<Integer> lottoNumber = lottoGenerator.generate(price);

    // then
    assertThat(lottoNumber.size()).isEqualTo(6);
}
```

assertThat 메서드를 사용할 수 없는 에러가 있었는데

<code>import static org.assertj.core.api.Assertions.assertThat;</code> 를 직접 import 추가하여 해결했다.

price를 1000이외의 다른 숫자로 바꿔 보면 테스트가 실패하면서 로또 생성기 클래스에 예외 처리해놓았던 "올바른 금액이 아닙니다." 가 출력된다.

<br>

**2.로또 번호 범위 테스트**

```java
@Test
void lottoRangeTest() {
    // given
    final LottoGenerator lottoGenerator = new LottoGenerator();
    final int price = 1000;

    // when
    final List<Integer> lottoNumber = lottoGenerator.generate(price);

    // then
    assertThat(lottoNumber.stream().allMatch(v -> v >= 1 && v <= 45)).isTrue();
}
```

List로 반환된 로또 번호들을 다시 스트림으로 만들어 1이상 45이하의 숫자들인지 검사했다.

isTrue()외에도 isFalse(), isNull() 등이 있다.

<br>

**3. 잘못된 로또 금액 테스트**

```java
@Test
void lottoInvalidMoneyTest() {
    // given
    final LottoGenerator lottoGenerator = new LottoGenerator();
    final int price = 2000;

    // when
    final RuntimeException exception = assertThrows(RuntimeException.class, () -> lottoGenerator.generate(price));

    // then
    assertThat(exception.getMessage()).isEqualTo("올바른 금액이 아닙니다.");
}
```

1000원 이외의 금액을 입력하면 예외가 발생한다. 따라서 발생하는 RuntimeException을 assertThrows()메서드로 감싸서 처리해야 한다.
