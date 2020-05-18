---
layout: post
title:  "인터페이스(interface)와 추상 클래스(abstract class)"
date:   2016-10-12 00:40:00 +0900
categories: [languages]
tags : [java, oop]
---
인터페이스와 추상 클래스는 무엇이고

그 차이는 무엇일까?

<!--more-->

인터페이스(`interface`)와 추상 클래스(`abstract class`)

각각에 대해서 알아보고 그 차이점을 살펴보자.

여기서 설명할 인터페이스와 추상 클래스는 [다형성](/2016/10/11/oop_inheritance_and_polymorphism/)을 먼저 이해하여야 한다.

먼저 추상 클래스(`abstract class`)는 무엇일까?

추상 클래스(`abstract class`)는

> 미완성된 클래스

이다.

미완성된 클래스? 무슨 말일까?

미완성된 클래스라는 말은

> 미완성된 메서드를 포함하고 있다

라는 말이다.

추상 클래스(`abstract class`)는 혼자로는 `Class`의 역할을 다 못하지만,

새로운 `Class`를 작성하는 데 있어 그 바탕(`Basic Class`)가 되는 부모클래스(`Super Class`)로서

중요한 의미를 갖는다.

`Class`를 작성하는 데 있어 어느정도 작성된 상태에서 시작할 수 있기 때문이다.

추상 클래스는 `class` 키워드 앞에 `abstract`가 붙는다.

{% highlight java %}
 abstract class Class
{% endhighlight %}

이런 식으로 말이다.

`abstract`가 있으니 이 `Class`는 구현을 해주어야 하는 `Class`라는 것을 짐작할 수 있다.

그러나 `abstract`가 있는 `Class`라고 모두 구현을 해주어야 하는 `Class`는 아니다.

왜냐하면 단지 **공유의 목적**으로 `abstract class`를 만드는 경우가 있기 때문이다.

추상 클래스는 여러 기존의 클래스에서 **공통된 부분**을 추상화 한 것으로

간단하게 말해 공통된 부분을 따로 뽑아낸 것이라고 할 수 있다.

추상 클래스의 목적은

> 기존의 클래스에서 공통된 부분을 추상화하여 상속하는 클래스에게 구현을 강제화 <br>
> 메서드의 동작을 구현하는 자식클래스로 책임을 위임<br>
> 공유의 목적

이라고 할 수 있다.

다음은 인터페이스다.

인터페이스(`interface`)는 다소 추상적인 개념인데 필자도 거기에 대해 자세히는 알지 못한다.

자바에서 사용하는 기능으로서의 `interface`에 대해서만 설명하겠다.

자바에서 `interface`는

> 일종의 추상클래스

이다. 하지만 **인터페이스는 클래스가 아니다**

추상 클래스가 **미완성된 설계도** 라고 한다면

인터페이스는 **밑그림만 있는 기본 설계도** 라고 할 수 있다.

인터페이스는

> 구현하는 모든 클래스에 대해 특정한 메서드가 반드시 존재하도록 강제한다.

말그대로 인터페이스는 **뼈대** 라고 할 수 있다.

뼈대를 기반으로 해서 구현을 하는 것이다.

마치 1개의 나사 설계도를 가지고 여러 개의 나사를 제품으로 만드는 것처럼 말이다.

그러나 인터페이스는 아래와 같은 제약사항이 있다.

- 모든 멤버변수는 `public static final`이어야 한다. 단, 이를 생략할 수 있다.
- 모든 메서드는 `public abstract` 여야 한다. 이 또한 생략할 수 있다. <br>
  단, `static` 메서드와 `default` 메서드는 제외한다.(Java 8 이후)

인터페이스도 상속이 가능하지만

상속은 인터페이스 끼리만 가능하다.

인터페이스는 **구현** 하여야 하며

상속의 `extends`와 달리 `implements` 키워드를 사용한다.

{% highlight java %}
 class Class implements Interface
{% endhighlight %}

이렇게 말이다.

또 다른점이 있는데

자바에서 상속은 1번 밖에 하지 못하지만 인터페이스를 이용하면

다중상속과 비슷하게 된다.

> 인터페이스는 다중 구현을 할 수 있다.

{% highlight java %}
 class Class implements Interface1, Interface2
{% endhighlight %}

이렇게 하면 인터페이스를 다중 구현할 수 있다.

인터페이스도 다형성으로 다음과 같이 할 수 있다.

{% highlight java %}
 public interface MyInterface {
   int VALUE = 5;

   int calculate(int a);
 }
{% endhighlight %}

위와 같은 인터페이스가 있다고 할 때

{% highlight java %}
 public CalCulA implements MyInterface {
   int calculate(int a) {
     return VALUE+a;
   }
 }

 public CalCulM implements MyInterface {
   int calculate(int a) {
     return VALUE*a;
   }
 }
{% endhighlight %}

이와 같이 구현한 두개의 클래스가 있다고 할 때,

{% highlight java %}
 MyInterface add = new CalCulA();
 MyInterface add = new CalCulM();
 int target = 5;

 System.out.println(add.calculator(target));
 System.out.println(mul.calculator(target));

{% endhighlight %}

다형성으로 인해 이렇게 사용할 수 있다.

단, [다형성](/2016/10/11/oop_inheritance_and_polymorphism/)에서 언급한 것처럼

> 인터페이스에 없는 메서드를 사용하면 에러가 발생한다.

마지막으로 인터페이스와 추상 클래스

이 두개의 차이는 무엇일까?

간단한 정의를 해보면

> 인터페이스는 클래스와 별도로 일련의 연관된 메서드를 정의

> 추상 클래스는 메서드를 선언하지만 모든 메서드를 선언하지 않는 불완전하게 정의된 클래스

이렇게 간단하게 정의를 했다.

또한 차이점으로는

> 인터페이스는 클래스가 아니지만 추상 클래스는 클래스다.

이 있다.

그리고

> 추상 클래스는 일반적으로 베이스 클래스로 상속해서 더 구체적인 클래스를 만들 때 쓰기 좋다

> 인터페이스는 서로 관련이 없는 클래스에서 공통적으로 사용하는 방식이 필요하지만 기능을 각각 구현할 경우 좋다

인터페이스와 추상 클래스를 표로 정리하면 다음과 같다.

인터페이스 | 추상 클래스
------|---------
클래스가 아니다 | 클래스다
클래스와 관련이 없다 | 클래스와 관련이 있다<br>(주로 베이스 클래스로 사용)
한 개의 클래스에 여러 개를 사용할 수 있다 | 한 개의 클래스에 여러 개를 사용할 수 없다.
구현 객체의 같은 동작을 보장하기 위한 목적 | 상속을 받아서 기능을 확장시키는 데 목적

대략 이정도가 되겠다.
