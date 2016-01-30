---
layout: post
title: "How to recover your local Octopress repository"
date: 2016-01-30 15:05
comments: true
categories: 
---

I am [hosting my blog](/creating-a-free-blog-in-10-minutes-with-github-pages-and-octopress/) on [GitHub pages](http://pages.github.com/). Due to a broken hard disk I've recently lost my local respository containing the Octopress sources. A simple `git clone` is not enough to recover the local repository because Octopress works with two branches: `source` for keeping the files to generate the static site and `master` that contains the site itself. The `master` branch is stored in the `_deploy` subfolder which is ignored by default in the `source` branch.

Here is how I've recovered my local repository to deploy new content:

<!-- more -->

``` bash
  git clone -b source git@github.com:mikrobi/mikrobi.github.com.git blog
  cd blog
  bundle install
  rake setup_github_pages
```

I provided `git@github.com:mikrobi/mikrobi.github.com.git` as the repository URL.

And finally I've updated my local `master` from `origin`:

``` bash
  cd _deploy/
  git fetch
  git reset origin/master --hard
  cd ..
```

That's it! Now I can create a new blog post as usual:

``` bash
  rake new_post["New post"]
  rake generate 
  git add .
  git commit -m "Add new post"
  git push origin source 
  rake deploy
```