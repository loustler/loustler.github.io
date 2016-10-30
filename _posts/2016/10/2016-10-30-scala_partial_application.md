---
layout: post
title:  "부분 적용 작업을 수행하는 고차함수"
date:   2016-10-30 16:50:00 +0900
categories: [languages]
tags : [scala]
---
스칼라에서 공부하면서 어려웠던 부분을 풀이했다.

부분적용(Partial Application)을 수행하는 고차함수이다.
<!--more-->

FPIS(Function Programming In Scala)를 이용해 학습하므로,

함수형 프로그래밍의 패러다임을 배우며 스칼라를 익히고 있기 때문에

기본 내용에 대한 것이 부실할 수 있음을 양해바란다.

부분 적용이란 부분적으로 완성시켜 나머지를 적용시키는 것인데

부분적으로 완성시키는 그 대상은 파라미터이다.

이런 기법을 `currying` 이라고 한다. <br>
알려주신 [Kevin](https://github.com/Kevin-Lee)님께 감사의 인사를...

`currying`에 대한 부분은 공부를 하다 보면 자세히 나오리라 생각한다.

여기에서 **Kevin**님이 알려주신 `currying`은 다음과 같다.

{% highlight scala %}
 add :: Int -> Int -> Int
 add a b = a + b

 add 1 2
 // 결과 3
{% endhighlight %}

마치 파라미터를 여러 개 사용하는 듯한 느낌이지만

실상은 `add 1`에서 부분적으로 완성된 함수가 리턴이 되고

리턴된 함수에 나머지 완성시킬 부분인 `2`를 넣어주는 것이다.

아래는 FPIS의 연습문제를 푼 것이다

{% highlight scala %}
 def curry[A, B, C](f: (A, B)=> C): A => (B => C) =
 (a) => ((b) => f(a,b))

 def uncurry[A, B, C](f: A => B => C): (A, B) => C =
 (a, B) => f(a)(b)
{% endhighlight %}

위의 function을 하나씩 풀어보자.

우선 `curry`

함수를 인자로 받는데 그 함수는 함수를 가지고 있는 고차원 함수이다.

즉, f는 (A, B) 두 개의 파라미터를 받는 function이고 리턴을 C를 하는 function을 인자로 받는다.

`curry`는 결과로 A 타입을 인자로 받아서 A타입을 인자로 받고 결과로 B를 인자로 받아 받았던 A를 추가해놓은 function을 리턴해준다.

즉, 함수를 리턴해준다

그럼 원하는 결과를 내기 위해서는 리턴받은 함수에 인자를 넣어주면 된다.

`(B) => f(넣은값, b)`를 수행하기 때문이다.

`curry`를 보면 다음과 같이 사용하면 된다.

{% highlight scala %}
 def curry[A, B, C](f: (A, B)=> C): A => (B => C) =
 (a) => ((b) => f(a,b))

 def main(args: Array[String]): Unit = {
   // 이것은 (y: Int) => 6 + y 를 return
   println(curry((x:Int, y: Int)=> x+y)(6))

   // 이것은 6+7 을 reutrn
   println(curry((x:Int, y: Int)=> x+y)(6)(7))
 }
{% endhighlight %}

다음은 `uncurry`다.

`uncurry`는 `curry`를 reverse 한 것이라 생각하면 된다.

다시 위로 스크롤 하면서 포스트를 보기 힘드니 아래의 코드를 보자. (위와 같은 코드다)

{% highlight scala %}
def uncurry[A, B, C](f: A => B => C): (A, B) => C =
  (a, B) => f(a)(b)
{% endhighlight %}

`uncurry`는 인자로 함수를 리턴하는 고차원함수를 받는다.

이것은 사용예제를 보면서 설명하는 게 이해가 더 잘될 것 같다.

{% highlight scala %}
def uncurry[A, B, C](f: A => B => C): (A, B) => C =
  (a, b) => f(a)(b)

def main(args: Array[String]): Unit = {
  println(uncurry((x:Int) => (y:Int) => x+y)(6,7))
}
{% endhighlight %}

`uncurry`에 위의 예제와 같은 함수를 넣었다.

x 값을 만족시키면 Int type의 y를 인자로 받아 x+y를 수행하는 function을 리턴한다.

그런데 그것은 `curry`와 같다.

앞에서 `reverse`라고 했으니 이것을 우리가 알던 `function`처럼 결과를 만들어 줘야 한다.

그래서 `uncurry`의 구현쪽을 보면 A type의 a와 B type의 b를 인자로 받아서 f(a)를 우선 실행한다.

실행하면 `curry`의 결과와 같은 함수가 리턴되는데 이 함수에 다시 같이 받은 파라미터인 b를 넣어준다.

그럼 리턴된 함수에 b읙 값이 들어가므로 결국

`x+y`가 실행되는 것과 같다.

마치 우리가 알던 일반적이고 통상적인 `function`의 모습이다.

`currying`은 FP에서 빈번하게 사용된다고 한다.

즉 FP를 하려면 정확히 알아야 되는 것이 아닐까?

앞으로도 공부 후에 내용을 지속 포스팅하겠다.
