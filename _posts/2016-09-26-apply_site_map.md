---
layout: post
title:  "구글에 검색이 잘되게 하기! sitemap"
date:   2016-09-26 11:20:00 +0900
category: [etc, devops]
tags: [setting]
---

자 그럼 블로그를 만들었으니

구글에서 검색이 잘 되어야 할 것이 아닌가?

`Google Search Console`에 `sitemap`을 제출하자.
<!--more-->

구글과 같은 검색엔진은 크롤링을 통해 웹 데이터를 수집한다.

내 블로그가 구글에서 검색이 잘 되게 하려면

구글이 내 블로그의 포스트들을 인덱싱을 해야한다.

그러면 어떻게 하면 될까?

구글은 `Search Console`을 통해

이를 해결해준다.

[Search Console](https://www.google.com/webmasters/tools/home?hl=ko)에 접속하면

`속성 추가`를 통해 사이트를 등록할 수 있다.

지난 편인 [Jekyll을 이용한 Github pages 만들기[심화/Google Analytics 적용]](/2016/09/26/github_pages_blog_google_analytics/)를

통해 `Google Analytics`를 적용했다면 한결 더 쉽게 할 수 있다.

자 그럼 시작해보자.

속성 추가를 통해 사이트를 등록한다.

![속성 추가](/files/search_console_1.png)

속성 추가 버튼을 누르면 아래와 같은 화면이 뜬다.

![속성 추가 화면](/files/search_console_2.png)

여기에서 `웹사이트`를 그대로 유지한채 url을 입력한다.

그리고 도메인 admin 인증을 해야되는데 대체 방법에 보면 `Google Analytics` 란이 있다.

![Google Analytics를 이용한 인증](/files/search_console_5.png)

우린 `Google Analytics`를 사용하기 때문에 인증이 매우 쉽다.

확인을 누르면 구글이 체크를 해서 `Google Analytics` 코드가 있으면 승인이 떨어진다.

그럼 승인은 끝났다.

다음은 `Google Console` 메인에서 사이트를 눌러서 들어서 좌측 메뉴를 보면 아래와 같이 있을 것이다.

![좌측 메뉴](/files/search_console_3.png)

아래 그림과 같이 열어라

![이렇게 열어라](/files/search_console_4.png)

열었다면 우측 상단에 `SITEMAP 추가/테스트` 버튼이 있다.

자 그럼 여기까지 진행하고 `sitemap`파일을 등록해야 된다.

방법은 2가지가 있다.

1. 본인이 만들거나

2. [generate](https://www.xml-sitemaps.com/) 사이트를 이용하거나

필자는 전자의 경우를 사용했기 때문에 전자의 경우를 통해 소개한다.

우선 `project root`에 다음과 같은 파일 2개를 생성한다.

1. `sitemap.xml`

2. `sitemap.xsl`

이 두개를 생성하고 아래와 같이 넣는다.

아래 파일은 `sitemap.xml`이다.
{% highlight ruby %}
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="/sitemap.xsl"?>
<urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd"
        xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  {\% for post in site.posts \%}
  <url>
    <loc>{ site.url }{ site.baseurl }{ post.url }</loc>
    {\% if post.date \%}
    <lastmod>{ post.date | date_to_xmlschema }</lastmod>
    {\% endif \%}
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
  </url>
  {\% endfor \%}
  {\% for page in site.pages \%}
  {\% if page.sitemap \%}
  <url>
    <loc>{ site.url }{ site.baseurl }{ page.url }</loc>
    {\% if page.date \%}
    <lastmod>{ page.date | date_to_xmlschema }</lastmod>
    {\% endif \%}
    <changefreq>{ sitemap.freq | default: 'monthly' }</changefreq>
    <priority>{ sitemap.priority | default: '1.0' }</priority>
  </url>
  {\% endif \%}
  {\% endfor \%}
  {\% for tag in site.tags \%}
  <url>
    <loc>{ site.url }{ site.baseurl }/tags/{ tag.name }</loc>
    <changefreq>monthly</changefreq>
    <priority>0.8</priority>
  </url>
  {\% endfor \%}
</urlset>
{% endhighlight %}

여기에서 변수, `{ }`에는 `brace`를 1개씩 더 해주면 되고,

`escape`인 `\`은 제거해주면 된다.

이번엔 `sitemap.xsl`이다.

{% highlight ruby %}
---
layout: null
---
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="2.0"
                xmlns:html="http://www.w3.org/TR/REC-html40"
                xmlns:image="http://www.google.com/schemas/sitemap-image/1.1"
                xmlns:sitemap="http://www.sitemaps.org/schemas/sitemap/0.9"
                xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="html" version="1.0" encoding="UTF-8" indent="yes"/>
  <xsl:template match="/">
    <html xmlns="http://www.w3.org/1999/xhtml">
      <head>
        <title>XML Sitemap</title>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
      </head>
      <body>
        <div id="content">
          <h1>XML Sitemap</h1>
          <p>This is a generated
            <a href="{{ site.url }}{{ site.baseurl }}sitemap.xml">sitemap</a>
            for robot, orcs or elves.
          </p>
          <p>You might to visit a
            <a href="{{ site.url }}{{ site.baseurl }}">site</a>
            for human.
          </p>
          <xsl:if test="count(sitemap:sitemapindex/sitemap:sitemap) &gt; 0">
            <table border="1" cellpadding="3" cellspacing="0">
              <thead>
                <tr>
                  <th>Sitemap</th>
                  <th>Last Modified</th>
                </tr>
              </thead>
              <tbody>
                <xsl:for-each select="sitemap:sitemapindex/sitemap:sitemap">
                  <tr>
                    <td>
                      <xsl:variable name="sitemapURL">
                        <xsl:value-of select="sitemap:loc"/>
                      </xsl:variable>
                      <a href="{$sitemapURL}">
                        <xsl:value-of select="sitemap:loc"/>
                      </a>
                    </td>
                    <td>
                      <xsl:value-of select="sitemap:lastmod"/>
                    </td>
                  </tr>
                </xsl:for-each>
              </tbody>
            </table>
          </xsl:if>
          <xsl:if test="count(sitemap:sitemapindex/sitemap:sitemap) &lt; 1">
            <table border="1" cellpadding="3" cellspacing="0">
              <thead>
                <tr>
                  <th>URL (
                    <xsl:value-of select="count(sitemap:urlset/sitemap:url)"/>
                    total)
                  </th>
                  <th>Priority</th>
                  <th>Images</th>
                  <th>Change Freq.</th>
                  <th>Last Modified</th>
                </tr>
              </thead>
              <tbody>
                <xsl:for-each select="sitemap:urlset/sitemap:url">
                  <tr>
                    <td>
                      <xsl:variable name="itemURL">
                        <xsl:value-of select="sitemap:loc"/>
                      </xsl:variable>
                      <a href="{$itemURL}">
                        <xsl:value-of select="sitemap:loc"/>
                      </a>
                    </td>
                    <td>
                      <xsl:value-of select="sitemap:priority"/>
                    </td>
                    <td>
                      <xsl:value-of select="count(image:image)"/>
                    </td>
                    <td>
                      <xsl:value-of select="sitemap:changefreq"/>
                    </td>
                    <td>
                      <xsl:value-of select="sitemap:lastmod"/>
                    </td>
                  </tr>
                </xsl:for-each>
              </tbody>
            </table>
          </xsl:if>
        </div>
      </body>
    </html>
  </xsl:template>
</xsl:stylesheet>
{% endhighlight %}

`sitemap.xsl` 파일은 그대로 사용하면 된다.

그리고 `_config.yml`에 `url`이 블로그 url 이어야 한다.

자 그럼 다 만들었다면 `commit & push`를 통해 실제 서버에 반영하자.

반영하고 나서 `yourdomain.github.io/sitemap`에 접속하면

글 리스트들이 쭉 뜬다.

그럼 확인했으면 `sitemap`을 등록하자.

`SITEMAP 추가/테스트` 버튼이 있는 것 까지 확인을 했다.

그 버튼을 눌러 뒤에 `sitemap`을 적어주자.

적고 테스트를 하면 `테스트 결과 보기`버튼이 나오는데 누르면 테스트 결과가 쭉 뜬다.

거기서 오류가 없을 것이다.

`테스트 종료` 버튼을 누르고 테스트를 끝내고

진짜 제출을 하자.

다시 `SITEMAP 추가/테스트` 버튼을 누르고

`sitemap`을 적고 이번엔 `제출`을 누른다.

그럼 끝이다.

구글이 크롤링을 해서 인덱싱을 해줄 것이다.

자 `sitemap` 등록이 끝났다.

이제 기다기만 하면 된다.
