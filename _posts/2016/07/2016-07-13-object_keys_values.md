---
layout: post
title: "자바스크립트 Object의 key와 value 구하기"
date: 2016-07-13 23:00:00 +0900
categories: [languages]
tags: [javascript]
---

JavaScript의 Object를 사용하다보면

key와 그 value를 구할 때가 있다.

그 때 사용할 만한 function을 소개하고자 한다.
<!--more-->
{% highlight javascript %}
if(typeof Object.values === 'undefined') {
  Object.values = function(obj) {
    var array = new Array();
    for (var key in obj) {
      array.push(obj[key]);
    }
    return array;
  }
}

if(typeof Object.keys === 'undefined') {
  Object.keys = function(obj) {
    var array = new Array();
    for (var key in obj) {
      array.push(obj[key]);
    }
    return array;
  }
}

{% endhighlight %}


위 function 들이다.

사실 Obejct.keys는 지원되고 있는 브라우저가 많다.

혹시나 지원되지 않는 브라우저가 있을까 넣은 것일 뿐,

대부분의 브라우저는 지원하고 있다.(IE는 지원하지 않을 수도..)

어쨌든 위 코드를 사용하면

없으면 선언하기 때문에 Object.keys에 대해서 신경쓸 필요는 없다.

사용예제는 아래와 같다.

{% highlight javascript %}
var object = {name : 'apple',  count : 5};
Object.keys(object);
Object.values(object);
{% endhighlight %}
