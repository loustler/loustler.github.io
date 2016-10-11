---
layout: post
title:  "정규표현식을 이용해 a 태그의 href 속성값 가지고 오기"
date:   2016-06-15 23:41:39 +0900
category: etc
tags: [regexr]
---

정규표현식으로 모든 a tag를 대상으로 href 속성값을 리턴함
{% highlight html %}
  <a href='#'>쉼표</a>
{% endhighlight %}

{% highlight javascript %}
  String.match(/<a.*?href="([^"]*)"[^>]*>/);
{% endhighlight %}

결과 : #


정규표현식으로 a tag안에 선언된 string을 가지고 옴

{% highlight html %}
<a href='#'>쉼표</a>
{% endhighlight %}

{% highlight javascript %}
 String.match(/<a\s+.*?>(.*)<\/a>/);
{% endhighlight %}

결과 : 쉼표
