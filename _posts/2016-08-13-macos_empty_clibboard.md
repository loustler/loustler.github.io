---
layout: post
title: "macOS 클립보드에 저장된 것 삭제하기(비우기)"
date: 2016-08-13 00:38:00 +0900
categories: [os]
tags: [macos]
---

macOS가 10.11.5 이후부터 이상하게 클립보드가 가끔 정상적으로 동작되지 않는 것을 느낀다.

왜 그런지는 애플만 알 것이고..

어쨌든 간단한 명령어 한 줄로 클립보드에 저장된 것을 모두 날려버리고

깔끔한 상태에서 사용해보자.
<!--more-->
{% highlight shell %}
 echo -n '' | pbcopy
{% endhighlight %}

이 한 줄이면 된다.

그럼 클립보드가 깔끔해진 것을 확인할 수 있다.
