# GitHub Pages
 * 기존 블로그인 [Tistory](http://loustler.tistory.com/)에서 [GitHub Pages](https://loustler.github.io)로 이전

# 포스트 작성 방법
[README](_post/README.md)

# 태그 추가 방법
[README](_tags/README.md)

# 카테고리 추가 방법
  1. 카테고리 폴더 생성
  2. `index.html` 생성
  3. 다른 카테고리 `index.html` 참조 (매우간단)

# Local Server Start
`bundle exec jekyll serve`

# Setting for local
 see [Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)

 1. `gem install bundler`(require `ruby` >= `2.1.0`)
 2. `bundle install`


## Warning
  `An error occurred while installing json ~~~` 와 같은 에러 발생시,
  ruby 버전이 너무 높아서 그러는 것으로 보여짐.
  ruby 버전을 낮추면 됨
