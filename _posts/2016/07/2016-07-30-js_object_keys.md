---
layout: post
title: "자바스크립트 Object의 모든 key 구하기"
date: 2016-07-30 15:38:00 +0900
categories: [languages, dsaa]
tags: [javascript, algorithm]
---

JS를 사용하다보면 Object안에 Object가 있는 형태를 볼 수가 있다.

예를 들면,
<!--more-->
{% highlight javascript %}
var obj = {
  a : {
    aa: 1,
    ab: 2,
    ac: {
      aca: 1,
      acb: 2,
      acc: 3
    }
  },
  b: {
    ba:1,
    bb:2,
    bc: {
      bca: 1,
      bcb: 2,
      bcc: {
        bcca: 1,
        bccb: 2,
        bccc: 3
      }
    }
  },
  c: {
    ca: 1,
    cb: 2,
    cc: 3,
    cd: {
      cda: 1,
      cdb: 2,
      cdc: 3,
      cdd: 4
    }
  }
}
{% endhighlight %}

위와 같은 경우다.

이럴 때 모든 key들을 구하려면 어떻게 하면 될까?

아래와 같이 하면 `array` 형태로 return 한다.

{% highlight javascript %}
function allKeys(obj, array) {
  if(typeof array === 'undefined' || typeof array === null) {
    array = new Array();
  }
  for(var property in obj) {
    if(obj.hasOwnProperty(property)) {
      if(typeof obj[property] === "object") {
        array.push(property);
        array.concat(allKeys(obj[property], array));
      } else {
        array.push(property);
      }
    }
  }
  return array;
}
{% endhighlight %}

위 function을 사용하면 obj에 있는 모든 key들을 가져올 수 있다 .

recursive(재귀)를 사용하고 있다.
