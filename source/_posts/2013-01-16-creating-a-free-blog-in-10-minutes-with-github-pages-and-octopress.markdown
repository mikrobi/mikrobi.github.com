---
layout: post
title: "Creating a Free Blog in 10 Minutes with GitHub Pages and Octopress"
date: 2013-01-16 14:58
comments: true
categories:
---
Today I decided to start a personal blog for sharing my thoughts, experiences and ideas about my work as a software developer. After a brief research on which blog system to use and where to (freely) host it, I came up with a solution based on [Octopress](http://octopress.org) that runs on [GitHub Pages](http://pages.github.com). It's completely free and (for people familiar with standard developer tools) easy to set up.

<!-- more -->

Here is how I created this blog within a few minutes:

1.  Create a [GitHub](http://www.github.com "GitHub") repository named `username.github.com`, in my case [mikrobi.github.com](http://mikrobi.github.com "Blog of Jakob Class").
2.  (Optional) If you'd like to enable commenting on your blog posts, sign up at [Disqus](http://disqus.com) and set up your new site.
3.  (Optional) In case you want to analyse your blog's visitors and see if anyone is interested in your website at all, create a [Google Analytics](https://www.google.com/analytics) account to receive a tracking ID for your blog.
4.  Download the current version of [Octopress](http://octopress.org) into your desired folder and enter that folder, in my case `blog`:
    <pre>
      $ git clone git://github.com/imathis/octopress.git blog
      $ cd blog
    </pre>
5.  Install and perform a basic setup of [Octopress](http://octopress.org):
    <pre>
      $ bundle install
      $ rake install
      $ rake setup_github_pages
    </pre>
6.  Refine the default configuration (e.g. name, subtitle, description [Google Analytics](https://www.google.com/analytics) tracking ID and so on) by editing `_config.yml` with your favourite editor.
7.  Generate the necessary files for publishing your blog:
    <pre>
      $ rake generate
    </pre>
8.  Deploy your blog to [GitHub](http://www.github.com "GitHub"):
    <pre>
      $ rake deploy
    </pre>
9.  Commit your sources into your repository:
    <pre>
      git add .
      git commit -m 'initial commit'
      git push origin source
    </pre>
10.   Write your first post:
    <pre>
      $ rake new_post["My First Blog Post"]
    </pre>

For detailed instructions refer to the [Octopress documentation](http://octopress.org/docs).

Have fun reading my and running your own blog!

Cheers, Jakob