---
layout: post
title:  "CentOS user management 유저 관리"
date:   2016-11-09 22:20:00 +0900
categories: [devops]
tags : [linux]
---
CentOS 6를 기준으로 작성한

유저 관리이다.

간단하게 작성했다.

<!--more-->

유저를 추가하고 삭제하는 등

간단하게만 포스트를 작성한다.

먼저 유저 리스트를 어떻게 보면 될까?

추가를 하려면 내가 추가하려는 유저가 있는 지

확인을 먼저 해봐야 할 것이 아닌가?

아래의 `command`를 통해 확인해보자.

{% highlight bash %}
 cat /etc/passwd
{% endhighlight %}

그럼 이제 등록된 유저들이 출력이 된다.

그럼 본격적으로 유저를 추가해보자.

{% highlight bash %}
 useradd 유저이름 [-g 그룹] [-d /directory/home]
{% endhighlight %}

위의 `command`를 통해 user를 추가할 수 있다.

`-g` option은 추가하는 user의 group을 지정하며,

group이 없으면 user를 추가할 수 없다.

`-d` option은 추가하는 user의 home directory를 지정한다.

지정하고자하는 home directory는 존재하고 있어야 한다.

directory가 없으면 user를 추가할 수 없다.

user를 추가하고 나면 user의 password를 지정해줘야 되지 않겠는가?

그럼 아래의 커맨드를 통해 password를 설정할 수 있다.

{% highlight bash %}
 passwd [유저이름]
{% endhighlight %}

password를 변경할 수 있다.
