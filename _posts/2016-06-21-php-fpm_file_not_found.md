---
layout: post
title:  "PHP-FPM File Not Found"
date:   2016-06-21 21:23:11 +0900
category: devops
tags: [setting, php, nginx]
---

nginX와 PHP-FPM 연동시 File Not Found Error가 발생할 수 있다.

Virtual Host까지 정상적으로 설정했는데도 에러가 나서 당황해서

이것저것 찾아보다 아래와 같이 하니 간단히 해결되었다.

{% highlight shell %}
 vi /etc/nginx/nginx.conf
{% endhighlight %}

로 nginx의 설정파일을 연 뒤,

대게 첫번째 라인에 있는 user가 있을 것이다

이 user와

{% highlight shell %}
  vi /etc/php5/fpm/pool.d/www.conf
{% endhighlight %}

여기 php-fpm의 설정파일에서의 user가

virtual host에 정의된 디렉토리의 소유자여야 했다.

default가 www-data일텐데

가장 간단한 건

파일의 소유권한을 변경해주면 된다

{% highlight shell %}
  chown www-data:www-data 디렉토리
{% endhighlight %}

를 통해 디렉토리의 소유유저와 소유그룹을 변경해버리면

File Not Found Error가 사라진다.
