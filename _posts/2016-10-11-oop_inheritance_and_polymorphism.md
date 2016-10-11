---
layout: post
title:  "상속(Inheritance)과 다형성(Polymorphism)"
date:   2016-10-11 23:10:00 +0900
categories: [languages]
tags : [java, oop]
---
OOP에서 상속(`Inheritance`)와 다형성(`Polymorphism`) 는 무엇일까?

<!--more-->

상속과 다형성은 [OOP](/tags/oop/)에서 중요한 다형성이다.

상속은

> 어떤 클래스에서 더 특화된 버전의 클래스를 위한 행동을 제공할 수 있게 한다

무슨 말일까?

만약, `B Class`가 `A Class`로부터 상속을 받으면(자바에서는 확장한다고 한다)

`A Class`는 `B Class`의 부모 클래스(`Super Class`) 또는 베이스 클래스(`Base Class`)가 되며

`B Class`는 `A Class`의 자식 클래스(`Sub Class`)가 된다.

`A Class`에서 정의한 행동(`method`)은 모두 `B Class`의 행동(`method`)가 되며,

**일부를 변형할 수도 추가할 수도 있다.**

그리고 `A Class`의 `Instance`가 필요한 곳이라면

>어디든 &nbsp;`B Class`를 사용해도 된다

상속은 쉽게 말하면 상위 클래스(`Super Class`)의 모든 것(상태(`status`), 행동(`method`))을

하위 클래스(`Sub Class`)가 가지고 있으며 그대로 사용할 수도 있고 원하면 `Customizing`을 할 수 있다는 것이다.

다형성은 무엇일까?

다형성은

> 한 행동(method)을 여러 방법으로 구현하고 상황에 따라 적당한 구현을 선택해서 쓸 수 있도록 해주는 기능을 제공한다

이 말은 이 말과도 같다

예를 들어보자

특정 클래스에서 서로 다른 매개변수(`argument` 또는 `parameter`)를 써서

한 개의 메서드(`method`)를 두 가지 버전으로 정의할 수 있다.

아니면 부모클래스(`Super Class`)와 자식클래스(`Sub Class`)에서 같은 메서드(`method`)를

다르게 정의하여 자식클래스(`Sub Class`)의 `Instance`에 대해서는

자식클래스(`Sub Class`)에서 정의한 메서드가 부모클래스(`Super Class`)에서

정의한 메서드(`method`)를 오버라이드(`Override`)하도록 할 수 있다.

이 때, 실행될 메서드(`method`)는

> 컴파일 시점 또는 Application이 실행될 때 선택된다.

상속과 다형성은 OOP에서 가장 중요하다고 봐도 될 것이라 생각한다.

`interface`나 `abstract Class` 등을 사용하려면 상속과 다형성에 대한 이해가 반드시 선행되어야 되어야 한다고 생각하기 때문이다.

이제 상속과 다형성을 아래의 예제를 통해 한 번에 이해해보자

{% highlight java %}
  public abstract class Shape {
    protected Point center;
    protected Shape(Point center) {
      this.center = center;
    }

    public Point getCenter() {
      return center;
    }

    public abstract Rectangle getBounds();
    public abstract void draw();
  }
{% endhighlight %}

위의 `abstract Class`를 확장한 `Class`를 만들어 상속과 다형성을 한 번에 살펴보자.

{% highlight java %}
  public class Rectangle extends Shape {
    private int height;
    private int width;

    public Rectangle(Point center, int width, int height) {
      super(center);
      this.width = width;
      this.height = height;
    }

    /**
    * Super Class인 Shape에서
    * abstract를 통해 상속받은 클래스가 구현하게
    * 강제하였으므로 구현하여야 됨
    */
    public Rectangle getBounds() {
      return this;
    }

    public int getHeight() {return height;}
    public int getWidth() {return width;}

    /**
    * draw 또한 getBounds()와 동일함
    */
    public void draw(Graphics g) {
      // 구현..
    }
  }

  public class Ellipse extends Shape {
    private int a;
    private int b;

    public Ellipse(Point center, int a, int b) {
      super(center);
      this.a = a;
      this.b = b;
    }

    /**
    * Rectangle의 getBounds()에 있는 주석 참조
    */
    public Rectangle getBounds() {
      return new Rectangle(center, a*2, b*2);
    }

    public int getSemiMajorAxis() {return a;}
    public int getSemiMinorAxis() {return b;}

    /**
    * Rectangle의 draw()에 있는 주석 참조
    */
    public void draw(Graphics g) {
      // 구현..
    }
  }
{% endhighlight %}

`extends`를 이용하여  `Shape`를 확장(상속)하였다.


이제 `Shape`를 상속받은 두 `Class`는 아래와 같이 사용할 수도 있다.
{% highlight java %}
  Point center = new Pointer(5, 10);
  Shape rectangle = new Rectangle(center, 5, 10);
  Shape ellipse  = Ellipse(center, 5, 10);

  // Exception
  rectangle.getHeight();

  // Success
  rectangle.getBounds();

  // Exception
  ellipse.getSemiMinorAxis();

  // Success
  ellipse.getBounds();
{% endhighlight %}

다형성으로 인해 `Rectangle Class`와 `Ellipse Class`는 `Shape Class`형으로

선언하여 사용할 수 있다.

이렇게 되면 `Rectangle Class`와 `Ellipse Class`의 각 `Instance`는

`Shape Instance`로 변환된다.

그리고 `Shape Class`에 선언된 `method`만 사용할 수 있다.

왜냐하면 `Shape Class`는 각 자식클래스(`Sub Class`)에서 어떤 `method`들을

추가로 정의했는 지 알 수 없기 때문이다.
