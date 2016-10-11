---
layout: post
title:  "Jekyll을 이용한 Github pages 만들기[심화/태그]"
date:   2016-09-25 21:00:00 +0900
category: etc
tags: [github, jekyll]
---

이번엔 태그를 만들어보겠다.

기본만으로는 사용하기에 조금 부족했을 것이다.

아쉬운 부분도 있고...

그래서 이번엔 블로그에 적용된 태그를 어떻게 적용하면 되는 지

소개하고자 한다.

기본편에서도 말했지만 필자는 블로그를 만들 때

[KaKao Tech Blog](http://tech.kakao.com/)의 도움을 많이 받았다.

사실 이 태그 기능도 Kakao Tech Blog에서 소개된 것을

필자가 다시 소개하는 것일 뿐이다.

KaKao Tech Blog의 [kakao 기술 블로그가 GitHub Pages로 간 까닭은](http://tech.kakao.com/2016/07/07/tech-blog-story/)

이라는 포스트를 봐도 되겠다.

시작하겠다.

먼저, `local`에 설치된 `repository`의 `project root`에 `_config.yml`과 `index.html`이 있을 것이다.

바로 여기에 우리가 설정할 `tags.md`가 들어가게 된다.

먼저 필자의 `tags.md`를 보면서 알아보자.

```
---
layout: page
title: Tags
permalink: /tags/
sitemap:
  priority: 0.7
---
 for tag in site.tags
* [tag.name](site.baseurl/tags/tag.name)
 endfor
```

이렇게 구성되어 있다.

[기본편](/2016/09/25/Jekyll을-이용한-Github-pages-생성-기본/)을 봤다면

어려운 점이 없을 것이다.

왜냐하면 `front Matter`라 칭하는

```
---
layout: page
title: Tags
permalink: /tags/
sitemap:
  priority: 0.7
---
```

이 부분이 크게 다르지 않기 때문이다.

sitemap과 priority는 잠시 후에 언급하겠다.


`front matter` 이후에 보이는 body 부분에 `/tags/`가 있다

`/tags/`는 `_tags` directory에 있는 것을 가지고 온다.

즉,  `tag`를 사용하려면 `/tags`에 `.md` 파일이 있어야 한다.

간단한 예시를 하나 보자면,

```
---
name: scala
title: Scala
image: /files/covers/scala.gif
---
```

필자의 `/_tag`안에 있는 `scala.md`이다.

매우 간단하지 않은가?

이렇게 간단하게 `front matter`만 작성하면 놀랍게도 태그를 만들 수 있다.

이제 몇 단계만 더 하면 된다.

다음은, 이 태그에 대한 설정을 `_config.yml`에 해야 된다.

이 또한 간단하다.

```
collections:
  tags:
    output: true
    permalink: /tags/:path/

defaults:
  - scope:
      path: ''
      type: tags
    values:
      layout: tag
```

이렇게만 해도 새로운 /tags/ 페이지가 생겼다.

이제 `/_layout` directory에 tag의 `layout` 역할을 할 녀석이 필요하다.

왜냐하면 설정에서 `default`로 `tags`로 들어오는 링크는 모두 `layout`이 `tag`이기 때문이다.

이 `layout`에 대한 것은 따로 작성하지 않겠다.

왜냐하면 이 `layout`은 이름 그대로 레이아웃이라 기존에 있는 다른 `layout`을 사용해도 되기 때문이다.

`layout`은 개인이 구하는 것으로 하고

이렇게 `tag` 설정을 마친다.
