---
layout: post
title:  "Date type formatting in PostgreSQL(psql)[PSQL에서 date 포맷바꾸기]"
date:   2017-06-29 02:45:28 +0900
categories: [db]
tags : [postgresql]
---

PostgreSQL 에서 date를 formatting 하려면 어떻게 해야할까?

<!--more-->

간단하니 짧게만 작성하겠다.

{% highlight sql %}
  SELECT now() AS date;
{% endhighlight %}

를 해보자

그럼 현재 datetime이 나오는데 이걸 formatting 해보자.

가장 많이 사용하는 `YYYY-MM-DD` 형태로 해보자

{% highlight sql %}
  SELECT TO_CHAR(now(), 'YYYY-MM-DD') AS date;
{% endhighlight %}

그럼 어떤 결과가 나올까?

필자가 테스트한 시간을 기준으로 보면

{% highlight shell %}
      date
  ------------
    2017-06-29
{% endhighlight %}

와 같이 나온다.

간단하지 않은가?

timezone이 적용되어 나올 것이다

한국기준 GMT+9가 적용되는데

GMT+0으로 출력하고 싶다면?

{% highlight sql %}
  SELECT now() AT TIME ZONE 'GTM0' AS datetime;

  SELECT TO_CHAR(now() AT TIME ZONE 'GTM0', 'YYYY-MM-DD') AS date;
{% endhighlight %}

이렇게 하면 TIMEZONE이 적용된다.

이상 간단한 tip이었다.

만약 다양한 포맷이 보고 싶다면 [여기](https://www.postgresql.org/docs/9.6/static/functions-formatting.html#FUNCTIONS-FORMATTING-DATETIME-TABLE)를 방문하면 된다.