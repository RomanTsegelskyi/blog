---
layout: post
title: Personal page and blog using Github pages and Jekyll
comments: True
---

Having a personal web page and blog can be very beneficial for a developer. I recently decided to create ones myself and ironically enough my first post will is a guide/report with the problem I faced and how I solved them would be useful. This port is meant to help people that are familiar with what Git and [GitHub](https://www.github.com/) is to get up and running with [GitHub Pages](https://pages.github.com/) and [Jekyll](http://jekyllrb.com/) in just a couple of hours.

Creating a personal page using GitHub pages
---

[GitHub Pages](https://pages.github.com/) are public web pages hosted for free through [GitHub](https://github.com). Github Users are allowed to have one personal page and unlimited amount of pages for different projects.

Since there are many articles about creating GitHub Pages, I will only go through high-level steps for creating a personal page:

1. Create a folder locally and initialize `git` repository in that folder by `git init`.
2. Create a simple `index.html` and add it to the repo by `git add index.html`.
3. Commit you changes by doing `git commit -m 'init'`.
4. Create a remote repository on Github as described [here](https://help.github.com/articles/create-a-repo/). Name it `NAME.github.io`, replacing `NAME` with desired page name.
5. If needed setup `ssh` keys as described [here](https://help.github.com/articles/generating-ssh-keys/).
6. Add remote repository to your local repository by `git add remote REMOTE_URL`. More detailed steps are can be found [here](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/).
7. Push changes to remote repository with `git push origin master` and you should be able to access your page at `NAME.github.io`.

### Templates for the website

Creating a basic website might be quick and easy, but who needs a site with just a couple of words? Assuming you don't know HTML that well (like me), or don't want to spend a lot of time creating a pretty website a good option is to use one of countless templates and customize it for yourself.

Here are some good websites to get started with:

* [Html5up](https://html5up.net)
* [WIX](http://www.wix.com/website/templates/html/personal/1)
* [FreeWebTemplates](http://www.freewebtemplates.com/personal/)

### Google Analytics

In case you love statistics or just curious if anyone visit you website, adding something to track visitor seems like a good idea. My choice was [Google Analytics](http://www.google.com/analytics/) since it's relative simple and works great. To add analytics support go to Admin tab and create new account. After you enter all the needed information and accept the agreements, you will get tracking code, something like this:

``` javascript
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-65099615-1', 'auto');
  ga('send', 'pageview');
```

Insert this code anywhere at your `index.html` page and data will get automatically uploaded to analytics.

Setting up a blog
---

Recently I learned about [Jekyll](http://jekyllrb.com/) - static websites generator and for me it seemed like a great and simpler alternative to dynamic platform like Wordpress, Joomla, etc. Besides its simplicity, it makes backups so much easier and avoids most common security concerns caused by running dynamic websites. Jekyll allows to write posts in [Markdown](https://en.wikipedia.org/wiki/Markdown) which is another big plus. Moreover, code examples are very nicely embedded in the website. So combining with a fact that GitHub provides free hosting for Jekyll blogs, I was completely sold for it.

### Poole

Even though setting up Jekyll is relatively easy, there exists a really nice bundle repository - [Poole](https://github.com/poole/poole) to simplify the process even more. `Poole` calls itself "the butler for Jekyll" and it indeed reduces the time needed to setup Jekyll powered blog to the matter of minutes. Of course the default design is no unique, but it allows to have a very quick start.

![demo.png](https://camo.githubusercontent.com/913caa38048fb4c6ed8767ab206f02b3fbc10f40/68747470733a2f2f662e636c6f75642e6769746875622e636f6d2f6173736574732f39383638312f313833313232382f34326166366336612d373338342d313165332d393866622d6530623932336565303436382e706e67).

Simple steps to get started:

1. Clone `Poole` repository locally with `git clone https://github.com/poole/poole.git`
2. [Create](https://github.com/new) another GitHub repository same way as with personal page.
3. Remove original remote repository from `Poole` by `git remote remove origin`.
4. Add your remote Github repository created at step 2 using `git add remote REMOTE_URL`.

Note that even if you push changes to the remote repository, you will probably not be able to access blog yet, since `_config.yml` is not correct which we will address in a bit, but you can test it locally as described in next subsection.

### Running Jekyll locally

To test locally the correctness of the setup, you will need to install Jekyll using [RubyGems](https://rubygems.org/pages/download):

```
gem install jekyll
```

And to run Jekyll locally:

```
jekyll serve
```

This is basically all you need. Of course, `Jekyll` provides a variety of options, a good intro to which might be found [here](http://jekyllrb.com/docs/usage/).

### Integrating blog as a part of personal page

After you clone the poole repository, it's time to link it to your page. For that just edit `_config.yml`:

```yaml
# Setup
title:               Your Blog Titme
tagline:             ''
description:         ''
home:                http://NAME.github.io
url:                 http://NAME.github.io/blog_name
baseurl:             /BLOG_NAME/
paginate:            5
paginate_path:       "/page:num/"
exclude:             ['posts']
```

Push the changes to your repository and you will be able to see the blog under `NAME.github.io/BLOG_NAME`

### Comments

One of the main things that I was concerned about when creating static blog is having comments functionality. But [Disquis](https://disqus.com/) seems to solve that problem very well. Create a file `_includes/comments.html` which includes the code provided by Disqus after registration. After that add modify the file `_layouts/default.html` to include the line:

``` html
{% include comments.html %}
```

Setting the comments this way allows easy enabling/disabling of comments on a page-by-page basis. All you need have to do is set `comments: True` in the YAML header of the post.

### Analytics

Adding Google Analytics to the blog is similar to adding comments. First, create an account through Google Analytics Admin section as you did for peronal page. Google will give you the javascript tracking code to embed on every website, which you should put in `_includes/google_analytics.html`. Finally, to enable analytics on all of the pages of the blog add the include to `_layouts/default.html`:

``` html
{% include google_analytics.html %}
```

### Social Buttons

There are different options for ways to adding sharing buttons to your blog (for example [this](https://github.com/carrot/share-button) and [this](https://github.com/lipis/bootstrap-social)), but after some searching, I went for simplicity described in this [article](http://codingtips.kanishkkunal.in/share-buttons-jekyll/) on [CodingTips blog](http://codingtips.kanishkkunal.in/). I would encourage reading an original article, but I also wanted to provide simplified steps.

As in other steps, create `_includes/share.html` with following contents:

``` html
<div class="share-page">
    Share this on &rarr;
    <a href="https://twitter.com/intent/tweet?text={{ page.title }}&url={{ site.url }}{{ page.url }}&via={{ site.twitter_username }}&related={{ site.twitter_username }}" rel="nofollow" target="_blank" title="Share on Twitter">Twitter</a>
    <a href="https://facebook.com/sharer.php?u={{ site.url }}{{ page.url }}" rel="nofollow" target="_blank" title="Share on Facebook">Facebook</a>
    <a href="https://plus.google.com/share?url={{ site.url }}{{ page.url }}" rel="nofollow" target="_blank" title="Share on Google+">Google+</a>
</div>
```

Next add this to `css/_share.scss`:

``` css
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

And and import in `styles.scss`:

``` css
@import "share";
```

Finally, to enable sharing on all of the pages of the blog add the include to `_layouts/default.html`:

``` html
{% include social.html %}
```

Also keep in mind that same as for comments, sharing can be turned off by setting `social: False` in the YAML header of the post.

### Pages in the header

To add permanent pages (like About, Feeds, CV, etc.) in the blog header you need to specify that in `_config.yml`. For example the following snippet adds About, Atom and RSS pages, which are in the root of the repository:

```yaml
pages_list:       
  About: '/blog_name/about'
  Atom: '/blog_name/atom.xml'
  RSS: '/blog_name/rss.xml'
```

Note that those files can themselves contain `Jekyll liquid variable`. Nice into to `liquid variables` can be found [here](https://github.com/jpoehls/hulk-example/blob/master/_posts/2013/2013-02-02-jekyll-recipes-for-blog-meta-tags.md).

#### Atom and RSS feeds

Poole provides a template for `Atom` feed by [default](https://github.com/poole/poole/blob/master/atom.xml). It's basic, but serves as a decent starting point. For RSS, there exists a great [repository](https://github.com/snaptortoise/jekyll-rss-feeds) with different templates for RSS feeds. Be sure to check the correctness of the links generated, because all the templates don't contain a reference to `blog_name`. My [RSS](https://github.com/RomanTsegelskyi/pi-blog/blob/gh-pages/rss.xml) and [Atom](https://github.com/RomanTsegelskyi/pi-blog/blob/gh-pages/atom.xml) files respectively.

### Code Highlighting

Strangely, by default `Poole` is not setup to support widely accepted triple backticks for code highlighting. Which leaves 2 options:

1) Change to different markdown style in `_config.yml` to support triple backticks:

```yaml
markdown:           redcarpet
redcarpet:
      extensions: ["no_intra_emphasis", "fenced_code_blocks", "autolink", "strikethrough", "superscript", "with_toc_data"]

```

2) Use ```highlight``` liquid variable:

```html
{\% highlight python \%}
def yourfunction():
     print "Hello World!"
{\% endhighlight \%} 
```

Which results in 

{% highlight python %}
def yourfunction():
     print "Hello World!"
{% endhighlight %}

I personally like the first option more, since I am more used to it, but after all both seem to yield identical results, so it's rather a matter of preferences.

### Interesting deployment systems

While setting up personal page and a blog already give a boost in productivity, there are deployment systems that make the process even more efficient:

* [R + knitr](http://kevinushey.github.io/blog/2015/01/03/first-post/). R is a great language primarily used by statisticians and one of it's amazing features is a great support for generating reports using [knitr](http://yihui.name/knitr/). It the linked blog post the author describes blogging using R stack which might be very useful for data analysis related posts.

* [Python + Pweave](http://iaingallagher.tumblr.com/post/41359279059/python-pweave-and-pandoc-howto). Similar to `R + knitr`, [Pweave](http://mpastell.com/pweave/) for `Python` provides an interesting system for generating `markdown` and `html` while embadding code. 

### Basic SEO

To improve search engine recognition of the blog, it's good idea to creat `sitemap.xml`. You can either create one by hand or [use the one automatically generated by GitHub](You can have one automatically generated by GitHub Pages. See this one I use for my site). Mine can be viewed [here](https://github.com/RomanTsegelskyi/romantsegelskyi.github.io/blob/master/sitemap.xml).
