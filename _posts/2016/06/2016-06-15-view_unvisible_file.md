---
layout: post
title:  "숨김파일 보기"
date:   2016-06-15 23:20:19 +0900
category: os
tags: [macos]
---

OS X 사용 중 숨김 파일을 봐야 할 경우가 있다.

이럴 경우 Terminal을 이용해 별도의 설정을 통해 숨김 파일을 볼 수 있다.

{% highlight shell %}
  defaults write com.apple.Finder AppleShowAllFiles YES
{% endhighlight %}

위 명령어를 Terminal 에 입력한 후

Finder를 재시작해주면 된다.

재시작은 Cmd+control+esc 을 누른 뒤 Finder를 클릭한 뒤 재실행을 클릭하면 된다

혹시 나중에 숨김 파일을 다시 안 볼일이 있으면

아래와 같은 명령어를 통해 숨김파일을 안 볼 수 있다.

{% highlight shell %}
  defaults write com.apple.Finder AppleShowAllFiles NO
{% endhighlight %}
