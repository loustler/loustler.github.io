---
layout: post
title:  "String + 연산자(operator)"
date:   2016-10-10 21:30:00 +0900
categories: [languages]
tags : [java]
---
`String`에서 `+` operator를 사용하면 어떻게 연산이 될까?

<!--more-->

`+` operator는 사용했을 때 컴파일러가 다음과 같이 변환한다.

{% highlight java %}
  "ab"+"cde";
  /**
  * 아래와 같이 변환된다.
  * StringBuilder 혹은 StringBuffer로 변환된다.
  */
  StringBuilder(or StringBuffer) ext = new StringBuilder();[or StringBuffer();]
  ext.append("ab").append("cde");
{% endhighlight %}

이런 식으로 내부적으로 `StringBuilder`나 `StringBuffer`로 변환하여 사용된다.

`String`은 `immutable` 즉, 상태가 변하지 않기 때문에

+를 그대로 `String`을 이용할 경우 새로 인스턴스를 생성해야 되는데

이 부분이 성능이 좋지 않다고 한다.

또한 `+` operator는 `String.concat()`과 같으며

`String.concat()` 또한 `StringBuilder`나 `StringBuffer`의 `append()`를 이용하여 구현되어 있다.

또, `String`은 저장할 때 `UTF-8`이 아닌 `UTF-16`으로 저장된다.

이 또한 아래의 `API`에 있으니 들어가면 찾아볼 수 있다.

링크의 `API`는 `Java 8`의 `String API`이다.

[String API](http://docs.oracle.com/javase/8/docs/api/java/lang/String.html) <= 링크를 클릭하면 Java String API를 확인할 수 있다.

필자는 `+` operator나 `String.concat()` 사용시 `StringBuilder`의 `append()`를 사용하는 줄 알았는데,

`API` 확인결과 `StringBuilder`와 `StringBuffer` 둘 중 하나를 사용한다고 한다.
