---
layout: post
title:  "First Class in Scala[스칼라의 first class]"
date:   2017-06-29 02:28:28 +0900
categories: [languages]
tags : [scala, functional programming]
---

이전에 썼던 [Clousre in Scala](../closure_in_scala) 에서

말했듯이

`JavaScript`를 비롯한

여러 Functional Programming paradigm을

지원하는 언어에서 쉽게 볼 수 있는

`First class` 는 어떤 걸 말하는 걸까?

<!--more-->

본인이 공부한 것을 바탕으로 설명하겠다.

`first class`는 간단히 정의하면

> 제약없이 다를 수 있는 대상

이다.

그럼 제약이 없다면 모두 `first class` 일까?

그건 아니다.

1. 인자로 넘길 수 있어야 함
1. 변수에 저장 가능
1. 함수에서 반환이 가능

이 성립해야 한다.

그럼 왜 `first class`가 좋을까?

아래와 같은 이유가 있다.

1. 연산을 추상화하거나 새로운 제어 구조를 만드는 데 좋은 수단
1. 함수를 일반화해두면 표현력이 엄청나게 늘어나기 때문에 아주 읽기 쉬우면서 간결한 프로그램의 작성이 가능

간단한 예제를 통해 일급함수가 무엇인지 왜 좋은지 알아보자

조건에서 첫번째 였던 **인자로 넘길 수 있어야 한다**의 예제를 보자.

{% highlight scala %}
  def calculate(f: (Int) => Int, x: Int): Unit = print(f(x)

  val add = (x: Int) => x + 3

  calculate(add, 3) // It returns 6

  calculate(add, 2) // It returns 5

{% endhighlight %}

이렇게 사용할 수 있다.

이렇게 연산을 추상화하여 `function literal`로 사용하여 재사용성을 높이고 1개의 function에 다양하게 구현할 수 있다.

추가적인 예를 들면 아래와 같은 것이다.

{% highlight scala %}
  def calculate(f: (Int) => Int, x: Int): Unit = print(f(x)

  val add = (x: Int) => x + 3

  calculate(add, 3) // It returns 6

  calculate(add, 2) // It returns 5

  val mul = (x: Int) => x * 2

  calculate(mul, 3) // It returns 6

  calculate(mul, 2) // It returns 4

  val mod = (x: Int) => x % 2

  calculate(mod, 5) // It returns 1

  calculate(mod, 4) // It returns 0

{% endhighlight %}

이런식으로 사용할 수 있다.

그래서 `calculate`라는 function 1개로 굉장히 유연하고 재사용성이 높게 사용할 수 있다.


그 다음은 **변수에 저장 가능**은 아래의 예제를 보자.
{% highlight scala %}
  def printHello: Unit = print("Hello")

  val hello = printHello

{% endhighlight %}


다음은 **함수에서 반환이 가능**하다는 것이다. 예제를 보자.
- 이부분은 앞에서 설명했던 [Partially applied function](/2016/10/30/scala_partial_application/)을 보면 감이 온다 -

{% highlight scala %}
  def add(x: Int, y: Int): Int = x + y

  def increase(x: Int): Int = add(1, x)

  println(add(1, 5)) // It will print 6

  println(increase(5)) // It will print 6

{% endhighlight %}

위의 `increase`와 같은 것을 말한다.

`partially applied function`의 간단한 예제이기도 한데,

사실 이것이 적절한 예제이면서도 `curried function`이라 부르는 것을 간단히 보여주기도 하기 때문에

좋은 예제이다.


위의 예제는 `scala repl`을 통해서 실행해보면서 확인해볼 수 있다.

post가 많은 도움이 되기를 바라며 잘못된 부분이나 수정해야할 부분이 있다면 [mail](mailto:dev.loustler@gmail.com)을 보내주면 되겠다.