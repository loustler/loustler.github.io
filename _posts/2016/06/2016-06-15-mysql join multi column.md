---
layout: post
title:  "MySQL join시 여러 컬럼을 한눈에 보기"
date:   2016-06-15 23:11:20 +0900
category: db
tags: [mysql]
---
데이터를 SELECT할 때

한 개의 컬럼에 여러 개의 컬럼들이 매칭이 되는 경우

한 개의 row에 같이 매칭되는 것 끼리 같이 보는 방법은

mysql 에서 제공되는 GROUP_CONCAT() 을 사용하는 것이다.

기본적인 사용 방법은 아래와 같다.

{% highlight sql %}
 SELECT standardCol1
 , GROUP_CONCAT(joinCol1)
FROM tbls
GROUP BY standardCol1
{% endhighlight %}

GROUP_CONCAT 의 기본 구분자(Default Seperator)는 ,(comma)이며 기본 구분자 외에 다른 구분자를 사용하려면

{% highlight sql %}
SELECT standardCol1
  , GROUP_CONCAT(joinCol1 SEPARATOR  '구분자' )
 FROM tbls
 GROUP BY standardCol1
 {% endhighlight %}

위와 같이 주면 된다.
