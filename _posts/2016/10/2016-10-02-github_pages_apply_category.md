---
layout: post
title:  "Jekyll을 이용한 Github pages 만들기[심화/카테고리 적용]"
date:   2016-10-02 16:30:00 +0900
category: etc
tags: [github, jekyll]
---

[Google Analytics 적용](/2016/09/26/github_pages_blog_google_analytics/)과

[Tag 적용](/2016/09/25/create_github_page_use_jekyll_2/)해봤다.

그럼 이제 분류를 위해 Category를 적용해보자.
<!--more-->

카테고리는 [Tag 적용](/2016/09/25/create_github_page_use_jekyll_2/)과

[Google Analytics 적용](/2016/09/26/github_pages_blog_google_analytics/)보다 더 쉽다.

카테고리를 선정해서 `root directory`에 카테고리 이름을 가진 `directory`를 생성한다.

그리고 그 안에 `index.html` 파일을 생성한다.

그리고 다음과 같이 간단하게 써라

{% highlight ruby %}
---
layout: 적용할 레이아웃이름
---
<ul id="post-list">
    {\% for post in site.categories.카테고리이름 \%}
        {\% include 리스트레이아웃.html \%}
    {\% endfor \%}
</ul>
{\% include pagination.html \%}
{% endhighlight %}

작성했는가?

아 물론, 적용할 때 `escape`로 사용된 `\`는 지우고 사용해야 된다.

그리고 작성해놓은 post에 아래와 같은 변수를 선언하고 값을 줘라

{% highlight ruby %}
categories: [카테고리1, 카테고리2 ...]
{% endhighlight %}

`Array` 형태로 주어 적용하고자 하는 카테고리를 입력하면 된다.

그 후 카테고리의 url로 접속하여 테스트 해보면

해당 카테고리가 적용된 포스트들이 나타난다.
