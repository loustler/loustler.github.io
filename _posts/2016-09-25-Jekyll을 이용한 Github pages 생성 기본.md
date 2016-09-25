---
layout: post
title:  "Jekyll을 이용한 Github pages 만들기[기본]"
date:   2016-09-25 13:44:30 +0900
category: etc
tags: [github, jekyll]
---

기존 블로그인 [tistory](http://loustler.tistory.com)에서

github에서 제공하는 pages를 이용해 blog를 옮겼다.

기존에 사용하던 tistory도 나쁘지 않았지만,

카카오 기술 블로그가 github로 옮기면서

**카카오도 자기들이 만든 티스토리**를

벗어났기 때문이기도 했고

솔직히 [github](https://github.com)가 더 편하기도 했다.

그 외에도 뭐 여자처자한 이유가 있었지만

github의 여러 기능을 사용하고 싶은 욕구도 있었다.

여튼 서론은 이정도만 하고

Jekyll을 이용해서 github pages기능을 이용하는 것은 쉬운 일이다.

처음에 환경설정하는 것이 좀 난해해서 그렇지

환경설정만 하면 정말 쉽다.

필자가 참고한 레퍼런스는 아래와 같다

1. [GitHub help](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)
2. [kakao 기술블로그](http://tech.kakao.com/2016/07/07/tech-blog-story/)
3. jekyllrb documents
 - [pages](https://jekyllrb.com/docs/posts/)
 - [variables](https://jekyllrb.com/docs/variables/)

일단 github에서 공식적으로 지원하는 문서부터 살살 풀어보자

전제는 `homebrew`가 설치된 mac이다.

우선 `homebrew`를 이용해 ruby를 설치하자.

기본적으로 ruby가 `2.0.0`이 설치가 되어 있긴 하지만,

그래도 최신을 사용하기 위해서..

{% highlight shell%}
  cd /path/to/your-directory
{% endhighlight %}

{% highlight shell%}
  brew install ruby
{% endhighlight %}

ruby를 설치한 다음엔 `gem`을 이용해 `bundler`라는 것을 설치하자

{% highlight ruby%}
  gem install bundler
{% endhighlight %}

`bundler`가 설치가 되었다면 `username.github.io`의 `local repository`를 설정하자

{% highlight shell%}
  git init
  git remote add your-github-clone-url
{% endhighlight %}

[Gemfile](http://tosbourn.com/what-is-the-gemfile/)이 없을 것이다. Gemfile을 만들자

{% highlight shell%}
  echo "source 'https://rubygems.org'\n gem 'github-pages', group: :jekyll_plugins" > Gemfile
{% endhighlight %}

Gemfile을 만든뒤 `bundler`를 이용해 필요한 `library`를 다운로드 받자

{% highlight shell%}
  bundle install
{% endhighlight %}

얼마간의 시간이 지난 후 `bundler`가 모든 라이브러리를 다운 받았을 것이다.

`jekyll`에서 제공하는 간단한 `cli`로 directory를 구성하자

{% highlight shell%}
  bundle exec jekyll new . --force
{% endhighlight %}

`directory`가 생겼을 것이다

그 다음에 `Gemfile`을 다시 열자.

VIM을 사용하든 에디터를 사용하든 상관없다.

그 후 자세히 보면 Gemfile에 여러 줄이 있는데 다 삭제해도 무관하다

필자가 사용하면서 전혀 이상이 없었다.

그래서 아예 아래 스크립트를 사용하는 것도 괜찮다

{% highlight shell%}
  echo 'source "https://rubygems.org"\ngem "github-pages", group: :jekyll_plugins' > Gemfile
{% endhighlight %}

자 이제 한 번 실행해보자

{% highlight shell%}
  bundle exec jekyll serve
{% endhighlight %}

정상적으로 실행이 안 될 것이다.

Github Repository 하면서 에러가 날 것이다.

왜냐하면 아래의 설정에서 repository 설정을 하지 않았기 때문인데..

그럼 이제 설정파일인 `_config.yml`을 수정해보자.

아래는 필자가 사용중인 환경파일이다.

```
title: # 사이트의 제목
email:  # 사용자 이메일
description: # 사이트 설명
baseurl: "" # 기본 url
url: "" # username.github.io 에서 username을 본인으로 바꾸면 된다. github에서는 그렇게 제공하기 때문
timezone: Asia/Seoul # timezone 설정

repository: username/username.github.io # username.github.io repository를 적어주면 된다.

permalink: /:year/:month/:day/:title/ # url을 어떻게 구성할 건지 묻는 것

excerpt_separator: <!--more--> # post에서 추출할 때 무엇을 기준으로 추출할 것인지 주로 미리보기에 사용된다.

markdown: kramdown # markdown 설정인데 아래 kramdown 까지 그대로 사용하면 된다

kramdown:
  input: GFM
  highlighter: rouge

gems:
  - jekyll-paginate # 페이지 네이션 설정

paginate: 5 # 몇 개의 포스트를 1개 페이지에 표시할 것인지
paginate_path: '/page/:num/' # 페이지네이션 경로

exclude: [README.md, LICENSE.md, Gemfile, Gemfile.lock] # Jekyll이 무시할 목록

keep_files: [assets, files] # 유지할 파일들

author: username # author
asset_url: /assets # assets의 경로
image: '' # 블로그 기본 이미지
```

위 설정을 가장 기본을 참조해서 설정하면 된다.

설정은 그리 어렵지 않다.

자 다시 실행해보자

{% highlight shell%}
  bundle exec jekyll serve
{% endhighlight %}

이제 에러가 나지 않을 것이고 정상적으로 보일 것이다.

브라우저를 통해 [내 사이트](http://127.0.0.1:4000/)를 보자.

그리고 혹시 나중에 `_config.yml`을 수정하면 위의 커맨드를 다시 실행하여야 된다.

설정은 `watch`를 통해 반영을 하지 않기 때문이다.

그 외는 바로바로 반영해준다.

이상 [jekyll](https://jekyllrb.com)을 통한 github pages 기본 구성하기를 마친다.
