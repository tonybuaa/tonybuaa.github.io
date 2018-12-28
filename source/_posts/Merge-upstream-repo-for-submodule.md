---
title: Merge upstream repo for submodule
date: 2018-12-28 09:39:12
tags: git
categories: Technology
lang: en
---

# Check remote branch
``` bash
$ git remote -v
origin  git@github.com:tonybuaa/hexo-theme-next.git (fetch)
origin  git@github.com:tonybuaa/hexo-theme-next.git (push)
```
We can only see the repo that we forked.

# Add the remote upstream repo
``` bash
$ git remote add upstream https://github.com/theme-next/hexo-theme-next.git
```
And we check again the remote branch.
``` bash
$ git remote -v
origin  git@github.com:tonybuaa/hexo-theme-next.git (fetch)
origin  git@github.com:tonybuaa/hexo-theme-next.git (push)
upstream        https://github.com/theme-next/hexo-theme-next.git (fetch)
upstream        https://github.com/theme-next/hexo-theme-next.git (push)
```

# Fetch the upstream branch
A new branch named upstream/master is created.
``` bash
$ git fetch upstream
remote: Enumerating objects: 31, done.
remote: Counting objects: 100% (31/31), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 35 (delta 27), reused 27 (delta 27), pack-reused 4
Unpacking objects: 100% (35/35), done.
From https://github.com/theme-next/hexo-theme-next
 * [new branch]      master     -> upstream/master
```

# Merge upstream/master to master
``` bash
$ git merge upstream/master
Updating 4048615..e699386
Fast-forward
 .github/stale.yml                                  |  2 -
 LICENSE.md                                         | 12 +--
 README.md                                          |  4 +-
 _config.yml                                        | 87 +++++++++----------
 docs/{LICENSE => LICENSE.txt}                      | 10 +--
 docs/ru/README.md                                  |  4 +-
 docs/zh-CN/README.md                               |  2 +
 languages/uk.yml                                   | 98 ++++++++++++++++++++++
 layout/_macro/post.swig                            | 12 +--
 layout/_macro/sidebar.swig                         |  9 +-
 layout/_partials/head/head.swig                    |  4 +-
 layout/_partials/header/brand.swig                 | 12 ++-
 layout/_scripts/vendors.swig                       | 44 +++++-----
 .../analytics/application-insights.swig            | 10 +--
 layout/_third-party/analytics/cnzz-analytics.swig  |  4 +-
 layout/_third-party/analytics/facebook-sdk.swig    | 33 ++++----
 .../_third-party/analytics/google-analytics.swig   | 15 ++--
 layout/_third-party/analytics/growingio.swig       |  4 +-
 layout/_third-party/analytics/tencent-mta.swig     | 23 +++--
 layout/_third-party/analytics/vkontakte-api.swig   | 44 +++++-----
 layout/_third-party/comments/valine.swig           | 29 ++++---
 layout/_third-party/copy-code.swig                 | 50 ++++++-----
 layout/_third-party/math/mathjax.swig              | 30 +++----
 layout/_third-party/needsharebutton.swig           |  6 +-
 layout/_third-party/rating.swig                    | 30 +++----
 layout/_third-party/search/localsearch.swig        |  2 +-
 layout/_third-party/seo/baidu-push.swig            | 24 +++---
 layout/page.swig                                   | 36 ++++----
 .../css/_common/components/header/site-meta.styl   |  2 +-
 source/css/_common/components/pages/archive.styl   |  2 +-
 .../css/_common/components/post/post-reward.styl   |  1 -
 .../components/third-party/algolia-search.styl     |  3 +-
 source/css/_common/scaffolding/base.styl           |  6 ++
 source/css/_schemes/Gemini/index.styl              | 18 ++++
 source/css/_schemes/Muse/_logo.styl                | 14 ++--
 source/css/_schemes/Pisces/_brand.styl             | 18 +++-
 source/js/src/motion.js                            | 15 +++-
 source/js/src/utils.js                             |  6 +-
 38 files changed, 444 insertions(+), 281 deletions(-)
 rename docs/{LICENSE => LICENSE.txt} (88%)
 create mode 100644 languages/uk.yml
```

# Review the changed (optional)
``` bash
git log -p
```

# Push updates to repo
``` bash
$ git push
Enumerating objects: 57, done.
Counting objects: 100% (57/57), done.
Delta compression using up to 4 threads
Compressing objects: 100% (34/34), done.
Writing objects: 100% (35/35), 5.57 KiB | 407.00 KiB/s, done.
Total 35 (delta 29), reused 0 (delta 0)
remote: Resolving deltas: 100% (29/29), completed with 21 local objects.
To github.com:tonybuaa/hexo-theme-next.git
   4048615..e699386  dev -> dev
```
