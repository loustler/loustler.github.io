---
layout: post
title:  "순수함수"
date:   2016-10-02 14:10:00 +0900
category: languages
tags : [scala, functional programming]
---

함수형 프로그래밍의 순수함수는 무엇일까?

한 번 알아보자

<!--more-->

순수함수(Pure Function)를 정의하자면 다음과 같다.

1. 입력으로 주어진 것의 연산 외에는 실행하지 않는다. 즉, 부수효과(side effect)가 없어야 한다.
2. 입력이 동일하면 출력도 동일하다.
3. 다른 형태의 변화를 일으키지 않는다.
4. 참조에 있어서 투명해야 한다.


그래서 순수함수는

> 스레드에 안전하고, 병렬계산이 가능하다.

`Java`의 `String` class에 있는

`length()`도 순수함수이다.

String의 길이가 동일하면 항상 같은 값을 돌려주기 때문이다.

더 간단한 예제를 살펴보자.

{% highlight java %}
  public int add(int a, int b) {
    return a+b;
  }
{% endhighlight %}

이 예제를 보면 더 감이 올 것이다.

이것은 인자로 주어진 `a`와 `b`를 합만 계산하여 return 해준다.

그 외의 어떤 부수효과가 없다.

함수명 그대로 더한 결과값만 return한다.
