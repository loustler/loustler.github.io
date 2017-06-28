---
layout: post
title: "VIM을 이용한 문자 replace"
date: 2016-09-04 11:15:00 +0900
categories: [os]
tags: [macos, linux, unix]
---

설정파일을 변경하거나 추가를 할 때 주로 `VIM`을 이용한다.

특히, 설정파일의 특정 설정을 변경해야 할 때 여러 라인에 걸쳐져서 있는 문자를 바꾸려면

그 귀찮음이 이만저만이 아니다.

<!--more-->

{% highlight shell %}
/문자
{% endhighlight %}

를 이용해 찾아서

{% highlight shell %}
n | N
{% endhighlight %}

을 이용해 문자를 일일히 뒤져야 되기 때문이다.

그래서 간단히 한 번에 replace를 하려면

{% highlight shell %}
:%s/바꾸려는문자/바꿀문자/g
{% endhighlight %}

를 이용하면 된다.
