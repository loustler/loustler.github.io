---
layout: post
title:  "Closure in scala[스칼라에서의 클로저]"
date:   2017-06-29 00:50:28 +0900
categories: [languages]
tags : [scala, functional programming]
---

`JavaScript`를 비롯한 여러 Functional Programming paradigm을

지원하는 언어에서 쉽게 볼 수 있는

Closure가 scala 에서는 어떤 모습일까?

<!--more-->

Functional Programming paradigm을 지원하는

여러 언어에서 쉽게 접할 수 있는 Closure

본인이 공부한 것을 공유한다

----

{% highlight scala %}
  (x: Int) => x + more
{% endhighlight %}

이와 같은 함수값(function literal)이 있을 때,

x는 function literal의 scope에서만 의미가 있으므로

bound varaible(바운드 변수)라고 한다.

more은 function literal에서 정의(define)한 게 아니고

다른 scope에서 변경된 것이고 변경될 여지가 있기 때문에

free varaible(자유 변수)라고 한다.

이를테면,

{% highlight scala %}
  var more = 1
  val addMore = (x: Int) => x + more

  addMore(1) // What is the result?
{% endhighlight %}

일 때 이 때

`addMore` function literal을 `closure` 라고 한다.

즉, `closure`는 변수가 참조하는 값이 아니라

**그 변수 자체**를 `capture`한다.

그래서 아래와 같이 `free variable`에 변화가 생겼을 때 `capture` 할 수 있다.

{% highlight scala %}
  var more = 1

  val addMore = (x: Int) => x + more

  println(addMore(1)) // 1

  more = 9999

  println(addMore(1)) // 2
{% endhighlight %}

이 때 `1`, `2`의 결과는 무엇일까?

more이 중간에 `9999`로 상태의 변화가 일어났다.

앞의 설명에 따르면

`capture`를 다시 할텐데 결과는 어떨까?

`closure`인 `addMore`이 `more`의 상태 변화를 실행할 때 `capture` 한다.

그래서 `1`의 결과는 `2`가 되고

`2`의 결과는 `10000`이 된다.

scala에서 제공하는 `repl`을 이용하여 확인해볼 수 있다.
