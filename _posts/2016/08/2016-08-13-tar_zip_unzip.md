---
layout: post
title: "tar 명령어를 이용한 압축 및 압축해제"
date: 2016-08-13 19:29:00 +0900
categories: [os]
tags: [macos, linux, unix]
---

tar 명령어를 통해 간단히 압축을 해제하고 압축을 할 수 있다.

tar는 macOS에도 가능하다

<!--more-->

{% highlight shell %}
tar cvzf [압축할 파일명] [ [압축할 파일|디렉토리 1] [압축할 파일|디렉토리 2] ..... [압축할 파일|디렉토리 N]]
{% endhighlight %}

위 명령어를 사용하면 간단하게 압축할 수 있다.

디렉토리 또한 가능하니 참고하면 좋겠다.

이제 압축을 해제하려면,

{% highlight shell %}
tar xvzf [압축 해제할 tar 파일] [해제할 위치 default . ]
{% endhighlight %}

tar 의 옵션인 `xvzf`를 통해 압축을 해제할 수 있다.

directory 를 주지 않으면 현재 디렉토리에 압축을 해제한다 .

현재 위치를 확인하는 방법은,

{% highlight shell %}
  pwd
{% endhighlight %}

pwd  명령어를 통해 간단히 확인할 수 있다.
