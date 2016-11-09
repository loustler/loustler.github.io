---
layout: post
title:  "CentOS iptables configuration 방화벽 설정"
date:   2016-11-09 21:41:00 +0900
categories: [devops]
tags : [linux]
---
CentOS 6를 기준으로 작성한

iptables 방화벽 설정이다.

모든 설정은 아니고 간단한 설정이다.
<!--more-->

iptables에 적용된 rule(규칙)을 확인해보려면 아래와 같이 해보면 된다.

{% highlight bash %}
 iptables -nvL
{% endhighlight %}

위의 `bash`를 입력하면 적용된 규칙들이 나오게 된다.

그럼 이 규칙들은 어떻게 정의하면 될까?

우리가 가장 자주 사용할만한 것만 골랐다. ~사실 나머진 잘 모른다..~

먼저 특정 IP를 차단해보자

{% highlight bash %}
 iptables -I INPUT -s xxx.xxx.xxx.xxx -j DROP
{% endhighlight %}

위에 있는 `xxx.xxx.xxx.xxx`에 차단할 IP를 등록하면 된다.

위처럼 하면 `bash command`로 하는 것이고

직접 `iptables`를 수정하려면

{% highlight bash %}
 vi /etc/sysconfig/iptables
{% endhighlight %}

로 이동한 뒤에, 위의 `command`에서 `iptables`를 제외한 것을 입력해주면 된다.

{% highlight vim %}
 -I INPUT -s xxx.xxx.xxx.xxx -j DROP
{% endhighlight %}

위의 예제처럼 말이다.

만약 `command`가 아니라 `iptables`에 직접 입력하려면 앞에 `iptables`를 삭제하면 된다.

반대로 특정 IP를 허용하려면 어떻게 하면 될까?

간단하다

앞에 사용했던 `command`에서 `DROP`을 `ACCEPT`로 수정하면 된다.

{% highlight bash %}
 iptables -I INPUT -s xxx.xxx.xxx.xxx -j ACCEPT
{% endhighlight %}

`SVN(Subversion)`같은 것을 사용한다면 `3690`포트,

`ssh`를 허용하려면 `22`번 포트가 열려있어야 된다

그럼 포트를 허용해야 될 것이 아닌가?

허용해보자

{% highlight bash %}
 iptables -A INPUT -p tcp —dport [port number] -j ACCEPT
{% endhighlight %}

`[port number]`에 포트번호를 적으면 특정 포트를 허용한다.

반대로 막으려면?

이것 또한 위의 IP 허용/차단과 동일하다.

`ACCEPT`를 `DROP`으로 바꾸면 된다. 이렇게..

{% highlight bash %}
 iptables -A INPUT -p tcp —dport [port number] -j DROP
{% endhighlight %}

그럼 위의 두개를 동시에 사용하려면?

{% highlight bash %}
 iptables -A INPUT -p tcp -s xxx.xxx.xxx.xxx —dport [port number] -j ACCEPT
{% endhighlight %}

간단하다 두개를 섞으면 된다.

위와 같이 말이다.

정리하면 다음과 같다.

{% highlight bash %}
  # 특정 IP 차단
  iptables -I INPUT -s xxx.xxx.xxx.xxx -j DROP
  # 특정 IP 허용
  iptables -I INPUT -s xxx.xxx.xxx.xxx -j ACCEPT
  # 특정 port 허용
  iptables -A INPUT -p tcp —dport [port number] -j ACCEPT
  # 특정 port 차단
  iptables -A INPUT -p tcp —dport [port number] -j DROP
  # 특정 IP의 특정 port 허용
  iptables -A INPUT -p tcp -s xxx.xxx.xxx.xxx —dport [port number] -j ACCEPT
{% endhighlight %}
