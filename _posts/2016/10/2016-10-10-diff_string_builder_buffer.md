---
layout: post
title:  "String, StringBuilder 그리고 StringBuffer의 차이"
date:   2016-10-10 22:30:00 +0900
categories: [languages]
tags : [java]
---

`Java`에서 문자열을 표현할 때 가장 많이 사용하는 `class`는 무엇일까?

단연 `String`, `StringBuilder`, `StringBuffer` 이 세 개를 꼽을 수 있다.

그럼 이 비슷해보이는 세 개의 `class`의 차이점은 무엇일까?

<!--more-->

먼저 `String`은 `immutable` 즉, 불변한 클래스다.

상태값이 변경하지 않는 것을 의미한다.

다음은 `Java 8`의 `String`이다.

{% highlight java %}
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    private final char value[];

    private int hash; // Default to 0
{% endhighlight %}

바로 알겠지만 String에 value는 `final`이다.

`char`형태의 `array`이고 `private`이다.

`String`에 존재하는 값인 `value`는 final로

더 이상 변경할 수 없게 했고 `class`에도 `final`을 넣어 상속불가능하게 하였다.

즉, 위 `String`의 `code`를 보면 알겠지만 `String`은 `immutable`하다.

`immutable`은 `thread`에 safe하다.

불변이기 때문에 어떤 `thread`가 접근을 해도 상태를 변경할 수 없기 때문이다.<br>
_Effective Java p.103 에서도 찾아볼 수 있다_

그럼 `StringBuilder`는 어떨까?

`StringBuilder` 또한 직접 소스를 보자.

{% highlight java %}
public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence
{

    public StringBuilder() {
        super(16);
    }

    public StringBuilder(int capacity) {
        super(capacity);
    }

    public StringBuilder(String str) {
        super(str.length() + 16);
        append(str);
    }

    public StringBuilder(CharSequence seq) {
        this(seq.length() + 16);
        append(seq);
    }
{% endhighlight %}

위 소스는 `Java 8`의 `StringBuilder` 이다.

자세히 보면 `StringBuilder`의 `constructor`를 보면 `append()`를 통해 `append`를 실행한다.

`append()`는 여러가지 `argument type`을 지원하므로 `overloading`이 되어 있다.

그 중 우리가 자주 쓸만한 것을 가져왔다.
{% highlight java %}
@Override
public StringBuilder append(Object obj) {
    return append(String.valueOf(obj));
}

@Override
public StringBuilder append(String str) {
    super.append(str);
    return this;
}
{% endhighlight %}

자 보면 `append`는 `this`를 반환한다. 거진 대부분의 `append`가 두 번째 `append()`인 `append(String str)`과

유사한 형태이다.

즉 `return this;`이다.

자기 자신 즉, `StringBuilder`를 반환한다.

그리고 `StringBuilder`를 보면 `super.append();`가 있다.

이 녀석을 추적해보면 구현한 `interface`인 `AbstractStringBuilder`를 보면

`String.getChars`를 사용하고 있고 이것을 찾아보면 `System.arrayCopy`를 통해서

`String`을 카피해서 해서 주는 걸로 보인다.

또한, `String`을 `StringBuilder`가 계속 가지고 있으면서 복사본을 주는 것으로 보여진다.

`StringBuffer`도 `StringBuilder`와 똑같이 동작하며,

두 `class`의 차이점은 각 메서드에 `synchronized`의 유/무다.

즉, `thread safe`는 `StringBuffer`이고, `thread unsafe`는 `StringBuilder`이다.

`StringBuilder`와 `StringBuffer`의 차이점은 **동기화 지원 여부**를 제외하고는 없는 것으로 보여진다.

정리하면 다음 표와 같다.

class | mutable | thread safe
------|---------|----------
`String`| `immutable`|`thread safe`
`StringBuilder`|`mutable`|`thread unsafe`
`StringBuffer`|`mutable`|`thread safe`
