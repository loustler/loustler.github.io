---
layout: post
title: "find 명령어를 이용한 특정이름을 가진 파일 검색"
date: 2016-08-19 21:29:00 +0900
categories: [os]
tags: [macos, linux, unix]
---

가끔 어떤 파일을 찾아야 되는데 이 파일이 어디에 있는 지 모를 경우가 있다.

이걸 뭐 다 뒤져볼 수도 없고 ...

그럴 때 간단하게 아래의 명령어로 원하는 파일명을 가진 파일이 어디에 있는 지 찾을 수 있다.

<!--more-->

{% highlight shell %}
find 경로 -name "파일명.파일확장자"
{% endhighlight %}

위의 간단한 한 줄로 특정 위치 하위에 존재하는 특정 이름을 가진 파일을 찾을 수 있다.

예를 들면 README.md 라는 markdown파일을 찾는다고 해보자

그런데 이 markdown파일을 모든 경로를 통틀어서 찾고 싶다

그렇다면,

{% highlight shell %}
find * -name "README.md"
{% endhighlight %}

이 명령어를 찾아 모든 경로에 있는 README.md라는 파일을 찾아 보여줄 것이다

결과는 아래 이미지와 같다.

![](/files/find_specific_name_1.png)

위의 명령어를 그대로 입력하고 엔터를 하면 아래와 같이 나온다.

![](/files/find_specific_name_2.png)
