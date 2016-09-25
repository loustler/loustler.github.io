---
layout: post
title:  "PHP-FPM과 nginX 연동시 Access Denied Error"
date:   2016-06-20 22:41:39 +0900
category: devops
tags: [nginx, setting]
---

만약 nginX와 PHP-FPM 연동시 Access Denied 라는 Error를 발견하게 된다면,

아래와 같이 해보기 바란다.

만약 ubuntu의 경우 apt-get을 통해 설치했다면

{% highlight shell %}
  vi /etc/php5/fpm/pool.d/www.conf  
{% endhighlight %}

로 php-fpm conf 파일을 vi 로 연 뒤,

 `/security.limit_extensions`

를 그대로 입력하면

security.limit_extensions를 찾아준다.

 `; security.limit_extensions = .php .php2 php3`

이렇게 되어 있을텐데

php의 보안을 위해 다른 확장자가 실행되지 못하게 막아 놓은 것이다.

그래서 앞의 주석역할을 하는 ; 을 제거하고

사용하고자 하는 확장자를 추가한다

 `security.limit_extensions = .php .html .htm`

이렇게 하면 Access Denied 에러는 사라졌을 것이다.
