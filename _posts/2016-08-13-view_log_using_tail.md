---
layout: post
title: "tail을 이용해 log 보기"
date: 2016-08-13 00:28:00 +0900
categories: [os]
tags: [linux, unix, macos]
---

가끔 로그 파일을 봐야될 때가 있다.

이를테면 서버에 요청이 왔는데 응답이 없다던가,

서버를 셋팅해서 구동을 시켰는데 응답을 정상적으로 하지 않는다던가..
<!--more-->
하지만 log를
{% highlight shell %}
  cat 로그파일
  vim 로그파일
  vi 로그파일
{% endhighlight %}

이렇게 보려면 access_log 같은 경우는 양이 방대하고 또 위의 명령어들을 이용해 보면 옛날 것부터 불러오기 때문에 내용을 보는데 시간이 다소 소요된다.

그래서 간단히 보는 방법은 tail을 이용하는 것인데,


{% highlight shell %}
tail -n [보고자하는 라인수] [로그파일]
{% endhighlight %}

위와 같이 하면 아래서부터 보여주게 된다.

최근순으로 보기에 편리하다.

만약 특정 날짜의 모든 로그를 보고 싶다면

{% highlight shell %}
  tail -n [보고자하는 라인수] *날짜*
{% endhighlight %}
로 하면 된다

간단한 예시로 2016-01-02의 모든 로그의 최근 20줄을 보려고 한다면,
{% highlight shell %}
  tail -n 20 *2016-01-02*
{% endhighlight %}

만약 로그를 감시하고 싶다면 다음과 같이 사용하면 된다.

{% highlight shell %}
  tail -라인f [로그파일]
{% endhighlight %}

`-f` 옵션을 사용하면 감시처럼 파일 크기가 변경되면 변경된 것을 볼 수 있다.

사용방법은 위의 `-n` 옵션과 유사하다.

{% highlight shell %}
tail -라인f [로그파일]
{% endhighlight %}

간단한 예시로 2016-01-02의 모든 로그의 최근 20줄이 업데이트 되는 것을 계속 보려고 한다면,
{% highlight shell %}
tail -20f *2016-01-02*
{% endhighlight %}

처럼 하면 된다.
