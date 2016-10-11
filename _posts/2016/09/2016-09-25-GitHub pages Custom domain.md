---
layout: post
title: "GitHub custom domain 사용하기"
date: 2016-09-25 15:00:00 +0900
categories: [etc, devops]
tags: [github]
---

필자처럼 `username.github.io`가 아닌 자신만의 **custom domain**으로 설정하고 싶은 사람이 있을 것이다.

그래서 준비했다.

`username.github.io`가 아닌 자기만의 custom domain으로 구성하기!
<!--more-->
일단 필자는 국내의 DNS Provider를 사용하지 않았다

필자가 추천하는 곳은 [gandi.net](https://www.gandi.net)이다.

무엇보다 저렴하다는 것이 메리트였다.

필자가 사용중인 `.io` 도메인이 1년에 29유로 부가세 포함해서 약 3만 5천원 정도다.(작성일 기준 / tax 제외)

그럼 [gandi.net](https://www.gandi.net)으로 이동하자.

이동한 다음에 간단히 회원가입을 하고 인증까지 끝내자.

그럼 도메인을 구입해보자.

원하는 도메인을 검색하여 사용할 수 있는 도메인이면 아래와 같이 뜬다.

![](/files/gandi_1.png)

`add`를 눌러 장바구니에 추가하여 `order` 버튼을 눌러 `order`를 하자.

등록했던 email로 온 `xxxx-GANDI`로 된 ID를 입력을 하고 로그인을 하자(로그인을 안했다면)

그리고 수정없이 주문을 하면 된다.

결제까지 완료하자.

한 10여분 정도 기다리면 `My Account` 메뉴에서 구입한 도메인을 볼 수 있다.

구입한 도메인을 누르면 설정창으로 이동할 수 있다.

이동하면 아래 그림과 같은 Zone Files 라는 메뉴가 있다.

![](/files/gandi_2.png)

필자와 다르겠지만 Zone Files에 접속한다.

`Create a new version`으로 zone file을 새로 생성한다.

기본 생성되어 있는 것을 모두 삭제한다.

그리고 아래 그림과 같이 zone을 추가해주면 된다

`Add`를 통해 다음 그림처럼 입력하면 된다.

![](/files/gandi_3.png)

그리고 `Use this zone`으로 해당 zone file을 활성화시킨다.

그 후 자신의 프로젝트 Repository에

CNAME이라는 파일을 생성한 뒤

구입한 도메인을 넣고 `Commit&Push`를 하면된다.

{% highlight shell%}
  echo "yourdomain.io" > CNAME
{% endhighlight %}

자 이제 기다리면 `Alias`가 되어 구입한 도메인으로 모두 작동할 것이다

참고로 필자같은 경우는 도메인이 CNAME이 적용되는데 3시간여 걸렸다.
