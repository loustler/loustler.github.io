# How to create post?
[README](_post/README.md)

# How to add new tag?
[README](_tags/README.md)

# How to create category
  1. Create directory for new category in root
  2. Create file `index.html`
  3. Reference at another `index.html`, because it is very simple

# Local Server Start
`bundle exec jekyll serve`

Open url is `localhost:4000` or `127.0.0.1:4000`

# Setting for local
 see [Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)

 1. `gem install bundler`(require `ruby` >= `2.1.0`)
 2. `bundle install`

# Initialize

 1. `\curl -sSL https://get.rvm.io | bash -s stable`
 2. `rvm install 2.4.5 --disable-binary`
 3. [show next step](#setting-for-local)

## Warning
if you found error like below ?

 `An error occurred while installing json ~~~`

Reinstall ruby like this (use rvm)

`rvm reinstall [ruby version] --disable-binary`