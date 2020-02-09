---
layout: post
title:  "내가 느낀 모노이드(monoid), 모나드(monad) 그리고 펑터(functor) - 두번째 펑터(Functor)"
date:   2019-12-29 18:01:01 +0900
categories: [languages]
tags : [scala, functional programming]
---
내가 느낀 모노이드(monoid), 모나드(monad) 그리고 펑터(functor)의 두번째 펑터에 대해서 이야기 해보고자 한다.

[첫번째 모노이드(Monoid)](../monad-monoid-functor-1-monoid)에 이어서 두번째다

<!--more-->

첫번째에 이어서 이번에도 이 그림을 먼저 살펴보자

![Cats TypeClass by typelevel](/files/type-classes.png)

펑터(Functor)는 무엇이고 어떤 건지 cats를 통해 그리고 코드를 통해 알아보자

{% highlight scala %}
trait Functor[F[_]] {
  def map[A, B](fa: F[A])(f: A => B): F[B]
}
{% endhighlight %}

뭔가 시그니쳐가 이전에 보았던 모노이드와는 많이 다르지 않은가?

`F[_]` 라는 것도 있고 낯설다.

여기서 `F[_]`는 간단하게 생각하면 어떤 타입이라고 생각하자.

간단히 예를 들면 우리가 자주 사용하는 스칼라의 `List`, `Option`,

자바로 생각하면 `List`, `Optional` 등이 있다.

잠깐 뭔가 의구심이 들지 않은가? 앞의 스칼라의 `List`, `Option` 이나

자바의 `List`, `Optional` 의 클래스의 시그니쳐를 떠올려보자.

우리가 흔히 아는 제네릭이 있지 않은가?

`F[_]` 뿐만 아니라 이제 `map`의 시그니쳐도 보자.

뭔가 연결된다 `Option`, `Optional` 다른 언어에 있지만 어떤 공통점으로

그래도 감이 오지 않는다면 코드로 보자

{% highlight scala %}
// Option Functor example
def optionFunctor: Functor[Option] = new Functor[Option] {
  def map[A, B](fa: Option[A])(f: A => B): Option[B] = fa match {
    case Some(v) => Some(f(v))

    case None => None
  }
}

// 사용한다면?
val hello: Option[String] = Some("Hello")

val unknown: Option[String] = None

// map 1
optionFunctor.map(hello)(_ + " World!") // Some("Hello World!")

// map 2
optionFunctor.map(unknown)(_ + " World!") // None
{% endhighlight %}

자 이제 이 코드를 보면 확 느꼈을 것이다.

그리고 우리가 자바의 `Optional`, 스칼라의 `Option`에서 `map`을 많이 보고 사용해봤었다.

안의 내용물을 변환하기 위해서 말이다.

그렇다 이것이 펑터다

이제 펑터에 대해서 무엇이다 라고 딱 말 할 순 없지만 펑터를 알게 되었다.

내 나름대로 펑터를 정의해보자면

> A라는 타입의 데이터를 가지고 그것이 본인의 타입이 되는 클래스 혹은 무언가가 가지고 있는 타입의 데이터를 변환하고 싶을 때 사용하는 것

모노이드도 그렇고 펑터도 그렇고 무언가 말로 하기는 참 어려운 녀석들이다.