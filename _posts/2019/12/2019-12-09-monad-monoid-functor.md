---
layout: post
title:  "내가 느낀 모노이드(monoid), 모나드(monad) 그리고 펑터(functor) - 첫번째 모노이드(Monoid)"
date:   2019-12-20 23:01:54 +0900
categories: [languages]
tags : [scala, functional programming]
---
새로운 회사로 이직하면서 기존보다 더 체감되기 시작한 모노이드, 모나드 그리고 펑터

이것들에 대해서 내가 느끼고 이해한 것을 설명하려고 한다.

<!--more-->

사실 모노이드(Monoid), 모나드(Monad), 펑터(Functor)는 다른 개발자분들의 블로그를 비롯하여 다양한 곳에서 설명을 해주고 있다

모노이드는 추상대수학에서 나온 순수 대수적 구조이며 결합법칙을 비롯하여 복잡한 개념이 존재한다

그러나 실질적으로 우리가 사용을 하면서 이 부분을 항상 떠올리면서 코딩을 하기란 쉽지 않다

그래서 이런 부분들을 잘 서포트해주고 있는 [cats](https://typelevel.org/cats/)를 참조하여 소개를 하고 내가 이해한 방식으로 풀어나가보고자 한다.

시작하기에 앞서 먼저 아래 그림을 통해서 더 직관적으로 살펴보자

![Cats TypeClass by typelevel](/files/type-classes.png)

우리가 실제 개발을 하다보면 쉽게 접할 수 있는 그런 구조 같지 않은가?

그림을 보다보면 다른 것들과 다르게 진하게 색칠되어 있는 개념들이 몇개 있다. 자세히 보자

우리가 찾던 Functor를 시작으로 Monad, Monoid가 보이지 않은가?

앞으로 이 그림과 함께 cats를 활용하여 조금 더 실무적으로 다가가보려 한다.

아 참고로 cats를 이용하면 cats가 자체적으로 이런 개념들에 대응되는 인스턴스들을 만들어놓았다.

우선 모노이드는 무엇일까?

모노이드는 다음과 같은 인터페이스를 가지는 녀석이다

{% highlight scala %}
// 이런 녀석
trait Monoid[A] {
  def empty: A

  def combine(x: A, y: A): A
}

// 실제론 cats에선?
trait SemiGroup[A] {
  def combine(x: A, y: A): A
}

trait Monoid[A] extends SemiGroup[A] {
  def empty: A
}
{% endhighlight %}


스칼라를 접해보지 않아 문법을 모르는 분들을 위해 자바로 소개를 하자면

{% highlight java %}
// 이런 녀석
public interface Monoid<T> {
  A empty();

  A combine(A x, A y);
}

// 실제론 cats에선?
public interface SemiGroup<A> {
  A combine(A x, A y);
}

public interface Monoid<A> extends SemiGroup<A> {
  A empty();
}
{% endhighlight %}

이런 식이라는 것이다

개인적으로 코드로 보니 모노이드의 느낌이 더 확 다가왔다.

`combine`의 시그니쳐만 봐도 느낌이 딱 온다. 동일한 타입의 `x`, `y`를 결합해서 똑같은 타입의 결과물을 만드는 것이다

이 때, 결과를 만드는데 필요한 입력을 모두 받아서 처리하기 때문에 우리가 흔히 사용하는 사이드 이펙트 또한 걱정할 필요 없다.

단순하면서 직관적이다. `x`, `y`를 받아서 새로운 무언가를 만드는 것

그럼 실제로 스칼라 코드를 통해 `Int`를 더해주는 모노이드를 만들어보자

{% highlight scala %}
def plusIntMonoid: Monoid[Int] = new Monoid[Int] {
  override def combine(x: Int, y: Int): Int = x + y

  override def empty: Int = 0
}
{% endhighlight %}

코드를 보니 더 직관적으로 다가온다

위 예제처럼 Int 타입을 더하는 모노이드뿐만 아니라 곱하거나 나누거나 하는 등 Int 2개를 가지고 다른 Int를 만들 수 있는 모든 것을 할 수 있다.

이런식으로 cats를 통해 배우고 공부하고 내 나름대로 모노이드에 대해서 이해를 한 것은 다음과 같다.

> 타입 A를 가진 2개의 입력을 이용해 타입 A를 가진 어떤 output을 만들 수 있는 어떤 것

그리고 무엇보다 **사이드 이펙트가 없다**

물론 억지로 만들려면 만들 수 있다...

그래서 나는 모노이드를 하면 cats의 이 시그니쳐가 머리에 이렇게 떠오른다.

> 모노이드는 동일한 타입의 입력 2개를 가지고 동일한 타입의 출력을 만들 때 사용하고 사이드 이펙트가 없다

사이드 이펙트가 없고 순수하게 입력만 가지고 출력을 만든다는 것은 굉장한 장점이라고 생각하며,

이 모노이드에 대해 간단한 설명을 마친다.