---
layout: post
title:  "클래스(Class)와 객체(Object)"
date:   2016-10-11 22:50:00 +0900
categories: [languages]
tags : [java, oop]
---
OOP에서 `Class`와 `Object` 는 무엇일까?

<!--more-->

`Class`(클래스) 먼저 살펴보자

`Class`는 `attribute`(속성)와 `actions`(행동)으로 이루어져 있다.

`attribute`는 `property`(성질), `state`(상태)라고도 하며 보통 `field`라고 한다.

`actions`는 `capability`(능력), `method`(메서드)라고 하며 보통 `method`라고 한다.

즉 이 두 개로 이루어진 무언가를 `추상화`한 것이다.

그럼 `Object`(객체)는 무엇일까?

`Object`는  다른 어떤 `Object Instance`(객체 인스턴스)와는 다른 별도의 상태(`attribute`,`field`)를 가지고 있는

어떤 클래스의 특정 `Instance`(인스턴스)를 뜻한다.

`Java`를 이용한 간단한 예제를 통해 살펴보자.

{% highlight java %}
 public class Point {
   private int x;
   private int y;

   public Point(int x, int y) {
     this.x = x;
     this.y = y;
   }

   public Point(Point point) {
     x = point.getX();
     y = point.getY();
   }

   public int getX() {return x;}
   public int getY() {return y;}

   public Point relativeTo(int dx, int dy) {
     return new Point(x+dx, y+dy);
   }

   @Override
   public String toString() {
     StringBuilder builder = new StringBuilder();
     builder.append("(")
            .append(x)
            .append(",")
            .append(y)
            .append(")");

     return builder.toString();
   }
 }
{% endhighlight %}
_출처 : Effective Java 2st Edition_

위의 코드가 `Class`다.

그럼 `Class`의 `Instance`인 `Object`를 살펴보자.

{% highlight java %}
  Point instance1 = new Point(5, 10);
  Point instance2 = new Point(-5, 5);
  System.out.println(instance2.toString());
{% endhighlight %}

위의 코드가 `Class`를 이용해 생성한 `Object Instance` 혹은 `Instance`다.

`instance1`과 `instance2`는 상태(`field`)가 다르기 때문에 다른 `Object`라고 할 수 있다.
