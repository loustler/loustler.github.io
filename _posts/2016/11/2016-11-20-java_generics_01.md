---
layout: post
title:  "Java Generics (제네릭)"
date:   2016-11-20 14:56:30 +0900
categories: [languages]
tags : [java]
---
Java의 Generics에 대해서 알아보자.

<!--more-->

Java의 Generics은 정말 유용한 기능이다.

`casting`은 물론이고 `type safe` 까지 보장해주기 때문이다.

사용방법은 다음과 같다.

Generics에 사용할 수 있는 것은

와일드카드(wildcard)인 `?`이 있고, type을 직접 지정해줄 수 있다 `T` 혹은 `E`와 같이..

그리고 `super`와 `extends`를 통해 제한(bounded)을 줄 수도 있다.

{% highlight java %}
static class Animal{}
static class Dog extends Animal {}

static <T> void print1(T t) {
   System.out.println(t);
}

static <T extends Comparable> void print2(T t) {
   System.out.println(t);
}

static <T extends Comparable & Serializable> void print3(T t) {
   System.out.println(t);
}

public static void main(String[] args) {
   // Object type의 element를 가지는 List
   List<Object> objectList;

   // 모든 타입
   // wildcard인 ? 은 ? extends Object 라고 봐도 될 것 같다
   List<?> someList;

   // 상속체크
   Dog animal = new Dog();

   // Generics subtype
   List<Dog> dogList = Arrays.asList(animal);

   // compile error
   List<Animal> animalList = dogList;

   // 정상 동작
   ArrayList<Animal> animalArrayList = new ArrayList<Animal>(Arrays.asList(animal));
   List<Animal> animalList1 = animalArrayList;

   print1("print1");
   print2("print2");
   print3("print3");
}
{% endhighlight %}

위 소스를 한 번 보면 바로 이해 될 것이다.

`print1`, `print2`, `print3`에서 `String`이 계속 출력되는 이유는

[Stirng API](http://docs.oracle.com/javase/8/docs/api/java/lang/String.html)를 보면

`Serializable`과 `Comparable`을 모두 구현하고 있는 것을 볼 수 있다.
