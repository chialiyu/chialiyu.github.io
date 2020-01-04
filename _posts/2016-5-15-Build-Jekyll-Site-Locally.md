---
title: Build Jekyll Site Locally - How to use advanced jekyll themes
modified: 2016-05-14
tags: [dev, blogging, setup, hpstr]

---

I use HPSTR theme now: https://github.com/mmistakes/hpstr-jekyll-theme

Since the theme is more complicated to setup and have more plugin dependencies, it becomes challenging to get everything line up before uploading to github.
Therefore I change to compile locally then upload the site content.
Here is my setup.

Charlie Park has an awesome post about the process.  More details here:
[Charlie Park's original post](http://charliepark.org/jekyll-with-plugins/)

1.  Move original theme and post contents to a new git repo.
https://github.com/chialiyu/chialiyu.github.io.raw

2.  Setup Jekyll locally [Jekyll installation guide](https://jekyllrb.com/docs/installation/)

3.  Setup local bundler environment for Jekyll [instructions](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-2-install-jekyll-using-bundler)

4.  Build and test locally

{% highlight html %}
{% raw %}
bundle exec jekyll build
bundle exec jekyll serve
{% endraw %}
{% endhighlight %}

5.  Automate the build and upload process.  (It is how I setup locally with my own path configuration.)
    After everything is setup, type 'bb' to build the updated site.

{% highlight html %}
{% raw %}
alias build_blog="cd ~/gitKitchenSink/chialiyu.github.io.raw; bundle exec jekyll build;cp -r ~/gitKitchenSink/chialiyu.github.io.raw/_site/* ~/gitKitchenSink/chialiyu.github.io/;cd ~/gitKitchenSink/chialiyu.github.io;git checkout 'new';git add --all;git commit -am 'Latest build.';git push origin new"
alias bb="build_blog"
{% endraw %}
{% endhighlight %}

6.  Complete the PR and merge in your site repo.

7.  Manually commit your raw repo after things look good.
