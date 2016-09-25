---
layout: post
title: "자바스크립트로 날짜 간격 구하기 "
date: 2016-07-12 23:10:10 +0900
categories: [languages]
tags: [javascript]
---

간혹 날짜 간격을 구해야 되는 경우가 있다.

날짜와 날짜 사이의 간격, 그 간격이 몇일이냐에 따라

동적인 내용을 보여줘야 되는 경우가 그럴 것이다.

나름 유용할 것이라 생각되는 function 이다.

{% highlight javascript %}
if(typeof Date.between === 'undefined') {
  Date.between = function(date1, date2) {
    var one_day = 1000*60*60*24;
    var diff = Math.abs(date2.getTime()-date1.getTime());

    return Math.ceil(diff/one_day);
  }
}
{% endhighlight %}

사용방법은 아래와 같이 사용하면 된다.

{% highlight javascript %}
var interval = Date.between(new Date('2016-07-12'), new Date());
{% endhighlight %}
