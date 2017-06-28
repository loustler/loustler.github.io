---
layout: post
title:  "VIM사용시 라인 이동"
date:   2016-10-20 16:35:00 +0900
categories: [os]
tags : [vim]
---
VIM을 이용해 편집할 때 라인을 옮겨야 할 때가 있다

어떻게 옮길까?

<!--more-->

한개의 라인을 옮길때는 아래와 같이 해도 무방하다

{% highlight vim %}
 y
 y
 p
{% endhighlight %}

y는 클립보드에 복사(copy)가 되고

p를 하면 붙여넣기(paste)가 된다.

한 줄은 이렇게 한다고 치자.

그럼 여러줄은 어떻게 옮길 건가?

여러 줄을 저렇게 하기엔 시간도 아깝고 무엇보다 귀찮다.

한 번에 해보자.

아, 혹시 VIM의 line을 보게 설정할 줄 모르면 아래와 같이 넣자.

{% highlight vim %}
  :set number
{% endhighlight %}

사용하는 방법이다.

참고로 이동할 라인의 바로 다음라인에 이동된다.

{% highlight vim %}
 # current line
 :m [이동할 라인]

 # single line
 :[이동될 라인]m [이동할 라인]

 # multi line
 :[라인 시작],[라인 끝]m [이동할 라인]
{% endhighlight %}

이렇게 하면 된다.

이해가 잘 안될 수도 있다

예시를 보자.

{% highlight vim %}
 # current line
 # 현재 커서가 있는 라인을 15라인 뒤로 옮긴다 가정하자.
 :m 15

 # single line
 # 특정 라인(10라인)에 있는 1개의 라인을 15라인 뒤로 옮긴다.
 :10m 15

 # multi line
 # 5라인에서 10라인을 15라인 뒤로 옮긴다.
 :5,10m 15
{% endhighlight %}

간단한 사용법이지만 모르면 앞에 나왔던 `y y p`처럼 해야된다.

시간도 많이 소요되고 귀찮게 말이다.

앞으로도 vim에 대해 알아내는 것이 있다면 [이곳](/tags/vim)에 올리도록 하겠다.
