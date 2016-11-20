---
layout: post
title:  "covariant와 invariant (공변과 불변)"
date:   2016-11-20 13:40:50 +0900
categories: [languages]
tags : [scala, functional programming]
---
FP의 covariant와 invariant는 무엇일까?

<!--more-->

covariant와 invariant는 `Generics`를 떠올리면 된다.

* 공변(covariant)
    * Type Args 앞에 +를 추가해서 표기
        * `[+A]`

    * 가변 지정자(variance annotation : 가변주해)
    * 위의 예제와 같은 args를 양(positive)의 매개변수 라고 함
    * explain
        * 만일 Dog가 Animal의 subtype이면 List[Dog]가 List[Animal]의 subtype으로 간주된다
        * generally
            * X가 Y의 subtype일 때, 모든 type X와 Y에 대해 List[X]는 List[Y]의 하위형식이다
            *

* 불변(invariant)
    * 공변(covariant)과 달리 Type Args앞에 +가 없으면 불변(invariant)
        *`[A]`

* covariant에 대해
 자바와 비교할 수는 없으나 이해를 하기 위해 비교 아닌 비교를 해보자..
 > Java 에서 `List<Dog>`가 `List<Animal>`의 서브타입이 될 수는 없다

 코드로 보자.

{% highlight java %}
  class Animal {}
  class Dog extends Animal {}
{% endhighlight %}

와 같다고 하면,

{% highlight java %}
List<Animal> animals = new ArrayList<Animal>();
List<Dog> animals = new ArrayList<Dog>();
{% endhighlight %}

자바에서 위의 것을 해보면 컴파일 에러가 발생한다.(제네릭)

 > 그러나 Scala의 covariant는 서브타입이 될 수 있다.

* 자바에서 covariant가 될 경우 문제점

{% highlight java %}
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Object> objects = numbers;
objects.add("aaa");
{% endhighlight %}

이 성립할 가능성이 있기 때문에 불가능하다.

> Scala의 List는 immutable이라 나중에 추가될 수가 없음

위와 같기 때문에 Scala에서는 성립이 가능하다
