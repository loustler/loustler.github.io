---
layout: post
title:  "Functional Programming 함수형 프로그래밍"
date:   2019-12-07 19:01:54 +0900
categories: [languages]
tags : [scala, functional programming]
---

올해 4월 정든 회사를 떠나 새로운 회사로 이직을 하였다.

이직한 새로운 회사에서는 함수형 패러다임을 이용한 함수형 프로그래밍을 이용하여 개발을 진행하고 있다.

그렇다면 함수형 프로그래밍은 무엇일까?

<!--more-->

함수형 패러다임에 대하여 익히 듣고 Java8의 람다를 시작으로 자바 생태계에서도 적극적으로 함수형 패러다임을 도입하고 있다.

이번에 이직한 회사는 최근 몇년들어 조금씩 유명해지고 있는 Scala를 사용하여 함수형 패러다임으로 개발을 한다.

새로운 패러다임에 적응을 조금씩 하면서 알게된 내용을 비롯하여 앞으로 공부하는 내용을 이 포스트를 비롯하여 앞으로 조금씩 적어보고자 한다.

우리가 흔히 접하는 객체지향 프로그래밍(OOP)이 객체지향 패러다임을 통하여 개발을 하는 것이라면

함수형 프로그래밍은 마찬가지로 함수형 패러다임을 이용하여 개발을 하는 것이다.

이 패러다임을 지원하는 하스켈을 비롯하여 여러 언어들이 있지만 우리에게 익숙한 객체지향과 함수형 패러다임을 동시에 지원해주는 멀티패러다임 언어인 스칼라를 사용하여 개발을 하고 있다.

스칼라가 함수형 패러다임을 지원해주는만큼 함수형 프로그래밍을 하기 위해 이런 함수형 패러다임의 개념들을 지원해주는 라이브러리가 있다.

대표적으로 [cats](https://github.com/typelevel/cats)와 [scalaz](https://github.com/scalaz/scalaz)가 있으며 [cats](https://typelevel.org/cats/)가 TypeLevel사가 만든 라이브러리인만큼 문서화가 더 잘되어 있다.

출/퇴근 할 때 지하철에서 간간히 [scala with cats PDF](https://underscore.io/books/scala-with-cats/)를 읽곤 하는데 이 PDF를 만들어준 [underscore](https://underscore.io/)에게 감사하다

이 PDF를 통해 공부한 내용 등을 내 나름대로 정리하여 앞으로 올리고자 한다.

어쨌든 본론으로 돌아가서 그렇다면 함수형 프로그래밍은 우리가 흔히 하는 프로그래밍과 뭐가 다를까?

아래의 간단한 예제를 통해 살펴보자

{% highlight scala %}
  // 10
  5 + 5

  // java.lang.ArithmeticException: / by zero
  5 / 0
{% endhighlight %}

이것은 일반적으로 우리가 사용하는 코드다

반면 함수형 프로그래밍에서는

{% highlight scala %}
  // ammonite-repl 등 repl에서 import 할 때
  import $ivy.`org.typelevel::cats-core:2.1.0-RC2`

  import cats.Monoid

  val plusInt: Monoid[Int] = new Monoid[Int] {
    def combine(x: Int, y: Int) = x + y

    def empty = 0
  }

  plusInt.combine(1, 2) // 3

  val divideInt: Monoid[Int] = new Monoid[Int] {
    def combine(x: Int, y: Int) = x / y

    def empty = 1
  }

  // java.lang.ArithmeticException: / by zero
  divideInt.combine(5, 0)

{% endhighlight %}

이렇게 할 수 있다.

이를 더 나아가서 cats 라이브러리의 effect 사이드 프로젝트를 이용하여 순수함수형 패러다임을 사용하는 간단한 예제를 보면,

{% highlight scala %}
  // ammonite-repl 등 repl에서 import 할 때
  import $ivy.`org.typelevel::cats-effect:2.0.0`

  import cats.Monoid
  import cats.effect.IO

  implicit val MonoidForPlusInt: Monoid[Int] = new Monoid[Int] {
    def combine(x: Int, y: Int) = x + y

    def empty = 0
  }

  def plus(x: Int, y: Int)(implicit M: Monoid[Int]): IO[Int] = IO.pure(x).map(M.combine(_, y))

  plus(1, 2) // IO[Int]

  plus(1, 2).unsafeRunSync // 3

  implicit val MonoidForDeviceLong: Monoid[Long] = new Monoid[Long] {
    def combine(x: Long, y: Long) = x / y

    def empty = 0L
  }

  def divide(x: Long, y: Long)(implicit M: Monoid[Long]): IO[Long] = IO.apply(x).map(M.combine(_, y))

  divide(5, 0) // IO[Long]

  // java.lang.ArithmeticException: / by zero
  divide(5, 0).unsafeRunSync

{% endhighlight %}

처럼 사용하게 된다.

이것들의 차이는 위의 작성한 예제와 같이 코드 작성법과 사용법이 다르다. 특히 마지막 예제인 순수함수형 패러다임 방법으로 작성한 예제의 경우 type-safe 하다

마지막 `divide(5, 0)`을 한 시점에서는 에러가 발생하지 않고 그 리턴값에서 에러값을 가지고 있다가 실행될 때 에러를 반환한다.

일종의 lazy이며 typesafe 인것이다.

또한 `divide(5, 0)`에서 에러가 발생하는 경우를 try-catch 혹은 우리가 흔히 아는 `Option`처럼 사용할 수도 있다.

이럴 경우에는 `divide(5, 0).handleErrorWith( _ => IO.pure(0L))` 처럼 핸들링을 할 수도 있다.

핸들링 할 수 있는 방법은 다음과 같은 방법들이 있다.

{% highlight scala %}
// 방법 1
// handleErrorWith를 이용
divide(5, 0).handleErrorWith( _ => IO.pure(0L)).unsfaeRunSync // 0

// 방법 2
// divide 함수 재정의하여 함수가 handleErrorWith를 Monoid의 empty를 이용하여 해주기
def divide(x: Long, y: Long)(implicit M: Monoid[Long]): IO[Long] =
  IO.apply(x).map(M.combine(_, y)).handleErrorWith(_ => IO.pure(M.empty))

divide(5, 0).unsafeRunSync // 0

{% endhighlight %}

이 포스트에서는 이처럼 간단하게 함수형 프로그래밍이 뭔지 무슨 차이가 있는지 간단하게 맛보기만 하였다.

다음에 이어서 함수형 프로그래밍에서 사용하는 개념들과 용어들에 대해서 공부하고 또 포스팅하겠다.