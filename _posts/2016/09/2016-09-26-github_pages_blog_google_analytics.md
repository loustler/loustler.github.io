---
layout: post
title:  "Jekyll을 이용한 Github pages 만들기[심화/Google Analytics 적용]"
date:   2016-09-26 10:52:00 +0900
category: etc
tags: [github, jekyll]
---

이제 블로그도 어느정도 만들었고 누가 내 사이트에 들어오는 지 등을 분석할 수 있는

Google Analytics(a.k.a GA)를 적용해보자.

<!--more-->

기존에 사용하던 티스토리는 플러그인 형식으로 지원하고 있다.

그러나 `Jekyll`의 기본적인 기능만 이용하면서 사용하려면 다음과 같이 하면 된다.

혹시 Google Analytics를 사용하지 않는다면

[링크](https://www.google.com/analytics/)를 눌러

등록을 하자.

Google Analytics에 내 블로그를 등록하는 방법은

[링크](http://blogchannel.tistory.com/149)를 통해서 알아보자.

기본적으로 Google Analytics에 계정이 있고 사이트를 등록했다는 가정하에 진행한다.

Google Analytics의 관리탭에 보면

속성에서 본인의 사이트를 클릭하여

속성 설정을 들어가자.

조금만 내려보면 `추적 ID`라는 카테고리와 함께 일종의 `ID`가 있다.

그리고 `Jekyll`의 설정파일인 `_config.yml`을 텍스트 에디터 혹은 `VIM`으로 열자.

열었다면 아래와 같이 입력하고 저장한다.

{% highlight ruby %}
google_analytics:
  ua: '추적ID'
{% endhighlight %}

`Google Analytics`는 `HTML`의 `tag` 중에 `head`에 넣는 것을 권장한다고 한다.

그럼 `head`가 있는 `layout` 혹은 `include` 파일을 연다.

그리고 `Google Analytics`에 보면 `추적 정보 -> 추적 코드`에 보면 스크립트가 있다.

이것을 그대로 복사해서 `/_include` 안에 넣는다.

필자같은 경우는 `google_analytics.html`이라고 생성하여

해당 스크립트를 집어넣었다.

그리고 스크립트부분에서

{% highlight js %}
ga('create', '추적ID', 'auto');
{% endhighlight %}

를 아래처럼 변경했다.

{% highlight js %}
ga('create', 'site.google_analytics.ua', 'auto');
{% endhighlight %}

이렇게 하면 설정에 있는 `google_analytics` 의 `ua`에 저장한 추적ID가 들어가기 때문이다.

그리고 `site.google_analytics.ua` 이 변수를 `{}` 두개로 감쌌다. ~~**랜더링이 자꾸 되어서 불가피하게**~~

자 그럼 설정은 되었고

만든 `google_analytics.html` 파일을 include를 해야 할 것이 아닌가?

`default`역할을 하는 `layout`이 있을 것이다.

필자같은 경우는 `/_layout/default.html`이 이 역할을 수행한다.

거기에

{% highlight html %}
 <head> </head>
{% endhighlight %}

`head` 태그 사이에 넣으면 된다.

{% highlight ruby %}
if site.google_analytics
  include google_analytics
endif
{% endhighlight %}

이런식으로 넣으면 된다.

물론 이것은 이것을 랜더링할 수 있게 `{\%`와 `\%}`로 감싸야 한다.

*여기에서 \은 escape처리를 위해 입력한 것이니 실제로 넣을때는 빼야된다*

자 그러면 `Google Analytics` 설정이 끝났다.
