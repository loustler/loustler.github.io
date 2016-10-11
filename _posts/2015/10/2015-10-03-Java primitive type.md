---
layout: post
title:  "Java primitive type"
date:   2015-10-03 21:13:00 +0900
category: languages
tags : [java]
---

Java에서 primitive type이란 Java측에서 제공해주는 기본적인 변수의 타입을 말한다.

이 primitive type은 Java에만 국한되지 않고 `programming language` 라면 제공해주는 기본 타입을 말한다.

Java는 아래와 같은 총 8가지의 primitive type을 제공한다.

![](/files/java_primitive_type.png)

1. boolean형

참/거짓을 판별하는데 주로 사용이 논리형변수입니다. 어떤 조건을 주어서 그 조건이 참이면 true를 거짓이면 false를 저장한다.
boolean Type의 default는 `false`다.

2. char형은 character의 약자로 문자를 저장할 수 있다.

크기는 2byte인데 한글 한 글자가 2byte, 영어는 1byte 이다.
그러나 영어든 한글이든 1글자만 입력이 가능하다.
컴퓨터는 모든 data를 숫자(bit 0과 1)로 처리하고 인식하기 때문에 char형은 숫자로 저장된다.
컴파일러가 숫자로 바꾸어 저장하기 때문인데, 각 글자마다 내부적으로 저장이 되는 숫자형식이 있으며
[Unicode](http://www.unicode.org/standard/translations/korean.html) 표준을 따른다.

{% highlight java%}
  char c = 'A';
  System.out.println(c);
{% endhighlight %}

위 코드를 실행시키면 65라는 숫자가 출력된다.
이는 내부적으로 숫자로 관리하고 있다는 것을 볼 수 있다.
어떻게 65가 나오는 지 궁금하다면 아스키코드표(ASCII Code)를 검색해보시기 바란다.

3. 정수형은 byte와 short, int, long가 있다.

단 그 크기가 모두 다른데 각 크기마다 저장할 수 있는 정수의 크기가 다르기 때문이다.
1Byte는 8bit이고  bit는 2^1이니 1Byte는 2^8을 저장할 수 있다는 것이고 크기에 2^8 씩 곱하면 이해될 것이다.
ex) 2Byte = 2^8 * 2^8 = 2^16 , 4Byte = 2^8 * 2^8 * 2^8 ^ 2^8 = 2^32

정수형의 default Type은 int 형이다.
Java에서는 정수형과 실수형에게 default Type을 지정해주었는데 정수형의 default Type은 int형이다.

4. 실수형은 float, double이 있다.

크기마다 표현할 수 있는 범위가 다르기 때문에 크기가 커질수록 표현할 수 있는 근사값이 높다고 말할 수 있다.
실수형의 default Type은 double 다.

각 primitive type의 Type별로 default는 아래와 같다.

1. 논리형 boolean : false
2. 문자형 char : '/u0000'
3. 정수형 byte, short, int, long : 0(long형은 0L)
4. 실수형 float, double : 0.0(float형은 0.0f, double형은 default이므로 안써줘도 되나 0.0d)

각 primitive type은 고유의 크기를 지니고 있으며 변수로 선언하여 사용할 때 메모리에 크기만큼 일정 공간을
할당받아 초기화된 값을 저장한다.
초기화된 값을 그대로 메모리에 넣는 구조로,
primitive type을 앞으로 사용할 메서드(method, 함수)에 사용을 하게 되면 call of value라는 방식으로 호출을 하게 됩니다.
call of value란 함수를 변수에서 호출을 할 때 메모리에서 값을 가지고 오는 것을 말한다.
primitive type이 call of value에 해당한다는 말이 된다.
call of value는 메모리에서 값을 그대로 복사해서 가지고 온다고 생각하면 된다.
따라서 메서드에 의해 값이 변경이 되더라도 원래 값이 변경이 되지 않는다.

코드를 통해 살펴보면

{% highlight java%}
public class CallOfValue {

  public void cof(int number) {
    number = 50;
    System.out.println("cof method number는 "+number);
  }

  public static void main(String[] args) {
    int number = 100;
    CallOfValue callOfValue = new CallOfValue();
    callOfValue.cof(number);
    System.out.println("number값 변했나요"+number);
  }
}
{% endhighlight %}

결과값은 아래와 같이 나온다.

즉 원래의 값은 메서드에 의해서 변하지 않는다는 것을 알 수 있다.
