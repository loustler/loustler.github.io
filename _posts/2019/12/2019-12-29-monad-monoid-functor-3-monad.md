---
layout: post
title:  "내가 느낀 모노이드(monoid), 모나드(monad) 그리고 펑터(functor) - 마지막 모나드(Monad)"
date:   2020-01-05 20:20:51 +0900
categories: [languages]
tags : [scala, functional programming]
---
내가 느낀 모노이드(monoid), 모나드(monad) 그리고 펑터(functor)의 마지막! 모나드에 대해서 이야기 해보고자 한다.

[첫번째 모노이드(Monoid)](../monad-monoid-functor-1-monoid), [두번째 펑터(Functor)](../monad-monoid-functor-2-functor)에 이어서 마지막 모나드이다.

<!--more-->

첫번째와 두번째 시리즈를 본 분이라면 이미 익숙해진 그림이다.

![Cats TypeClass by typelevel](/files/type-classes.png)

모나드를 cats와 코드를 통해서 살펴보자

{% highlight scala %}
trait Monad[F[_]] {
  def flatMap[A, B](fa: F[A])(f: A => F[B]): F[B]

  def map[A, B](fa: F[A])(f: A => B): F[B]

  def pure[A](a: A): F[A]
}
{% endhighlight %}

이제는 몇 번 접해봤을 `F[_]`가 또 나왔다.

`F[_]`가 무엇인지 모르는 분이라면 시리즈 [두번째 펑터(Functor)](../monad-monoid-functor-2-functor)를 한 번 보시기를 권장한다.

그럼 알고 계실 거라고 생각하고 `F[_]`에 대해서는 더 이상 설명하지 않고 모나드에 대해서만 설명하겠다.

저번 시리즈에서도 그랬듯이 모나드의 시그니쳐를 한 번 보자. 그리고 그 구성요소들인 메서드들의 시그니쳐도 보자.

`map`은 펑터편에서 설명을 했었던 것이라 익숙할 것이다.

그런데 `flatMap`과 `pure`라는 것이 생겼다.

`pure`는 낯설것이다. 시그니쳐를 보면 별 거 아니다. 매우 간단하지 않은가? 타입 A를 받아서 `F[_]`에 넣어준 것이 끝이다

`flatMap`은 뭘까?

개발자답게 일단 코드부터 살펴보자

{% highlight scala %}
// Option Monad example
def optionMonad: Monad[Option] = new Monad[Option] {
  def flatMap[A, B](fa: Option[A])(f: A => Option[B]): Option[B] = fa match {
    case Some(v) => f(v)

    case None => None
  }

  def map[A, B](fa: Option[A])(f: A => B): Option[B] = fa match {
    case Some(v) => Some(f(v))

    case None => None
  }

  def pure[A](a: A): Option[A] = Some(a)
}
{% endhighlight %}

저번과는 다르게 모나드 구현 부분만 따로 보고 그 이후에 실제 사용하는 걸 살펴보자

`flatMap`과 map을 비교해서 보자

다만 다른 부분이라면 `case Some(v) => Some(f(v))`가 `case Some(v) -> fa(v)` 이렇게 바뀐 것 뿐이다

그리고 시그니쳐를 다시 보자

`f`의 의미는 A를 입력응로 받아서 B타입을 가진 Option으로 만드는 함수 라는 것이다

이렇게 보면 어렵지 않다

자 그럼 실제로 어떻게 사용하는 지 보자

{% highlight scala %}
// use pure
val hello: Option[String] = optionMonad.pure("Hello")

val unknown: Option[String] = None

// flatMap 1
optionMonad.flatMap(hello)(x => Some(x + " World!")) // Some("Hello World!")

// flatMap 2
optionMonad.flatMap(unknown)(x => Some(x + " World!")) // None

{% endhighlight %}

사용하는 건 역시나 간단하다

Java8을 사용해본 분이라면 사용예제를 보고 나니 뭔가 익숙할 것이다.

자바의 `Optional`이나 스칼라의 `Option`에서 봤던 것이다

`Optional.of(..).flatMap` 으로 새로운 `Optional`을 만들어주는 것 말이다

우리는 알게 모르게 이 모나드를 자연스럽게 사용하고 있었던 것이다

모나드는 특히 앞선 펑터나 모노이드처럼 모나드는 이거야! 라고 설명하기가 매우 어려운 것 같다.