Creating a personal webpage and a blog using github pages and jekyll
===

Having a personal webpage and blog can be very beneficial for a developer. I recently decided to create my own page and a blog and felt that some guide with the problem I faced and how I solved them would be useful. This guide is meant to help people that are familiar with what Git and Github is to get up and running with GitHub Pages and Jekyll in an just couple of hours.

How to create a personal page using GitHub pages
---

[GitHub Pages](https://pages.github.com/) are public webpages hosted for free through [GitHub](https://github.com) Users are allowed to have one personal page and unlimited amount of pages for different projects.

Simple steps of creating a personal page:

1. Create a repository for your personal page. Default naming.
2. Add a simple index.html file. CSS/JavaScript
3. Push the changes and profit.

Templates for the website
---

Creating a basic website might be quick and easy, but who needs a site with just a couple of words? Assuming you don't know HTML that well, or don't want to spend a lot of time creating a pretty website a good option is to use one of countless templates and customize it for yourself.

Here are some good websites to get started with:

* [Html5up](https://html5up.net)
*

Google Analytics
---

In case you love statistics or just curious if anyone visit you website, adding something to track visitor seems like a good idea. My choice was [Google Analytics](http://www.google.com/analytics/) since it's relative simple and works great. To add analytics support go to Admin tab and create new account. After you enter all the needed information and accept the agreements, you will get tracking code, something like this:

```
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-65099615-1', 'auto');
  ga('send', 'pageview');
```

Insert this code anywhere at your index.html page and data will get automatically uploaded to analytics.

How to create a blog as a subdirectory of a personal page
---

Recently I learned about [Jekyll](http://jekyllrb.com/) - static websites generator and for me it seemed like a great and simpler alternative to dynamic platform like Wordpress, Joomla, etc. Besides its simplicity, it makes backups so much easier and avoids most common security concerns caused by running dynamic websites. Jekyll allows to write posts in [Markdown](https://en.wikipedia.org/wiki/Markdown) which is another big plus. Moreover, code examples are very nicely embedded in the website. So combining with a fact that GitHub provides free hosting for Jekyll blogs, I was completely sold for it.

### Poole

Even though setting up Jekyll is relatively easy, there exists a really nice repository - [poole](https://github.com/poole/poole) to simplify the process even more. Poole calls itself "the butler for Jekyll" and it indeed reduces the time spend to minutes. Of course the default design is no unique, but it allows to have a very quick start.

![demo.png]()


### Integrating blog as a part of original website

After you clone the poole repository, it's time to link it to your page. For that just edit `_config.yml`:

```
# Setup
title:               Your Blog Titme
tagline:             ''
description:         ''
home:                http://username.github.io
url:                 http://username.github.io/blog_name
baseurl:             /blog_name
paginate:            5
paginate_path:       "/page:num/"
exclude:             ['posts']
```

Push the changes to your repository and you will be able to see the blog under `%USERNAME%.github.io/blog_name`

### Comments

One of the main things that I was concerned about when creating static blog is having comments functionality. But [Disquis](https://disqus.com/) seems to solve that problem very well. Create a file `_includes/comments.html` which includes the code provided by Disqus after registration. After that add modify the file `_layouts/default.html` to include the line:

```
{% include comments.html %}
```

Setting the comments this way allows easy enabling/disabling of comments on a page-by-page basis. All you need have to do is set `comments: True` in the YAML header of the post.
---
layout: post
title: Creating page and blog with gh-pges(Test)
comments: True
share: True
---
### Analytics

Adding Google Analytics to the blog is similar to adding comments. First, create another account through Google Analytics Admin section as you did for peronal page. Google will give you the javascript tracking code to embed on every website, which you should put in `_includes/google_analytics.html`. Finally, to enable analytics on all of the pages of the blog add the include to `_layouts/default.html`:

```
{% include google_analytics.html %}
```

### Social Buttons

There are different options for ways to adding sharing buttons to you blog (for example [this](https://github.com/carrot/share-button) and [this](https://github.com/lipis/bootstrap-social)), but after some searching, I went for simplicity described in this [article](http://codingtips.kanishkkunal.in/share-buttons-jekyll/) on [CodingTips blog](http://codingtips.kanishkkunal.in/). I would encourage reading an original article, but I also wanted to provide here the summary.

As in other steps, create `_includes/share.html` with following contents:

```html
<div class="share-page">
    Share this on &rarr;
    <a href="https://twitter.com/intent/tweet?text={{ page.title }}&url={{ site.url }}{{ page.url }}&via={{ site.twitter_username }}&related={{ site.twitter_username }}" rel="nofollow" target="_blank" title="Share on Twitter">Twitter</a>
    <a href="https://facebook.com/sharer.php?u={{ site.url }}{{ page.url }}" rel="nofollow" target="_blank" title="Share on Facebook">Facebook</a>
    <a href="https://plus.google.com/share?url={{ site.url }}{{ page.url }}" rel="nofollow" target="_blank" title="Share on Google+">Google+</a>
</div>
```

Next add this to `css/style.css`:

```css
.share-page {
    text-align: center;
    background: $secondary-color;
    color: $light-color;
    padding: 8px 15px;
    border-radius: 5px;
    margin: 1.5 * $spacing-unit 0;

    a {
        font-weight: 700;
        color: #fff;
        margin-left: 10px;

        &:hover {
            border-bottom: 1px dashed #fff;
        }
    }
}
```

Finally, to enable sharing on all of the pages of the blog add the include to `_layouts/default.html`:

```
{% include share.html %}
```

Also keep in mind that same as for comments, sharing can be turned off by setting `comments: False` in the YAML header of the post.

### Interesting deployment systems

### SEO