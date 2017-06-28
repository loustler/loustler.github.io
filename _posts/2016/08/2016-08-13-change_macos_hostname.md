---
layout: post
title: "macOS hostname 사용자 이름 변경"
date: 2016-08-13 00:33:00 +0900
categories: [os]
tags: [macos]
---

사용자 이름을 처음에 설정하고 터미널을 켜게 되면

사용자 이름이 뭔가가 어색하다.

.local이 붙는다던가 ...

hostname을 변경하여 내가 원하는 사용자로 terminal에서 볼 수 있다.

<!--more-->

필자는 이런 것을 좋아하지 않기 때문에 변경하였다.

{% highlight shell %}
 sudo hostname [바꿀 사용자이름]
{% endhighlight %}

바꾸길 원하는 사용자이름을 [] 를 대신하여 아래와 예시와 같이 변경한 뒤,

{% highlight shell %}
sudo hostname loustler
{% endhighlight %}

terminal에 입력을 하고 비밀번호를 입력한다.

관리자(root) 권한으로 사용자 이름을 변경하는 것이다.

그 후 terminal을 완전히 종료(⌘+Q)한 뒤 terminal을 다시 열어보자.

이제 바뀐 사용자이름을 확인할 수 있다.

혹시 사용자이름을 확인해보고 싶다면 아래와 같이 입력하면 된다.

{% highlight shell %}
hostname
{% endhighlight %}

그럼 현재 설정되어 있는 사용자이름을 출력해줄 것이다.
