---
layout: post
title:  "Scala trait - 스칼라의 trait"
date:   2019-01-02 22:11:37 +0900
categories: [languages]
tags : [scala, functional programming]
---

Scala 에서는 `trait`이라는 신기한 녀석이 있다.

`trait`은 어떤 녀석이고 무엇을 하는 녀석일까

<!--more-->

Scala에서는 trait이라는 녀석이 있다.

Java를 알고 있는 사람이라면 `interface`와 유사하다고 볼 수도 있다.

`interface`와 유사하게 `trait`은 선언한 method를 하위 구현체에서 구현을 해주어야 한다.

물론 `interface`와 `default method`처럼 작성할 수도 있다.

다만 Java처럼 특정한 keyword 없이 구현할 수 있다.

가령 자바에서

{% highlight java %}
public interface Taste {
  public boolean isSweet();

  public default boolean isNotSweet() {
    return !isSweet();
  }
}
{% endhighlight %}

이와 같이 작성한다면

스칼라에서는

{% highlight java %}
trait Taste {
  def isSweet(): Boolean

  def isNotSweet(): Boolean = !isSweet()
}
{% endhighlight %}

와 같은 형태로 작성할 수 있다