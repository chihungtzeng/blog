---
title: Hexo setup
date: 2019-6-02 09:17:41
tags:
- tooling
categories:
- software usage
---

This post is how I deploy hexo into github.io.


### Setup hexo

```
$ mkdir hexo_root
$ cd hexo_root
$ npm install hexo-cli  # create ~/hexo_root/node_modules
$ hexo -v
...
hexo: 4.0.0
hexo-cli: 3.1.0
os: Linux 4.4.6-gentoo linux x64
http_parser: 2.8.1
node: 8.12.0
v8: 6.2.414.66
uv: 1.20.0
zlib: 1.2.11
ares: 1.10.1-DEV
modules: 57
nghttp2: 1.33.0
napi: 3
openssl: 1.0.2q
icu: 64.2
unicode: 12.1
cldr: 35.1
tz: 2019a

$ vi _config.yml
  title: Clark's Mill
  author: Clark
  language: en
  url: https://chihungtzeng.github.io

$ export PATH=~/hexo_root/node_modules/hexo-cli/bin:$PATH
$ hexo init blog  # create ~/hexo_root/blog
$ cd ~/hexo_root/blog
$ hexo generate
$ hexo server
```

Now open http://localhost:4000 to see the effect.

### Install theme
```
$ cd ~/hexo_root/blog
$ git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
$ vi _config.yml
  theme: cactus

$ cd ~/hexo_root/blog/themes/cactus
$ vi _config.yml
  colorscheme: light

$ cd ~/hexo_root/blog

# Create /about page
$ hexo new page about
$ vi source/about/index.md

# Create /tags page
$ hexo new page tags
$ vi source/tags/index.md  # Add the following contents
$ cat source/tags/index.md
---
title: tags
date: 2019-11-02 17:59:17
type: tags
---

# Create categories page
$ hexo new page categories
$ cat source/categories/index.md
---
title: categories
date: 2019-11-02 23:46:38
type: categories
---

# Put tags/categories pages on the navigation bar
$ vi themes/cactus/_config.yml
nav:
  home: /
  about: /about/
  tags: /tags/
  articles: /archives/
```

### deploy to github

create a repo call chihungtzeng.github.io in github web site.

```
$ cd ~/hexo_root/blog
$ vi _config.yml
deploy:
  type: git
  repo: git@github.com:chihungtzeng/chihungtzeng.github.io.git
  branch: master
```

Install hexo-deployer-git to enable ***hexo deploy***.

```
$ cd ~/hexo_root/blog  # Notice the path
$ npm install hexo-deployer-git --save
```

Now open your browser, go to github web site, and register ssh keys: settings->SSH and PGP keys. From now on, we can simply run ```hexo deploy``` to deploy our repo to github.io.


### Add/Delete a page

```
# Add
$ hexo new "Hello World!"

# Delete the page associated with My-screenrc.md
$ rm ~/hexo_root/blog/source/_posts/My-screenrc.md
$ hexo clean
$ hexo generate
```

### RSS support
```
$ cd ~/hexo_root/blog
$ npm install hexo-generator-feed  # for generate rss feeds
$ vi _config.yml
feed:
    type: atom
    path: atom.xml
    limit: 20
    hub:
    content:
    content_limit: 100
    content_limit_delim: ' '
```

### Math support

```
$ cd ~/hexo_root/blog
$ npm uninstall hexo-renderer-marked --save
$ npm install hexo-renderer-kramed --save
$ npm uninstall hexo-math --save
$ npm install hexo-renderer-mathjax --save
$ vi themes/cactus/_config.yml
  mathjax:
    enable: true
$ vi node_modules/hexo-renderer-mathjax/mathjax.html
change line 15 from
  <script src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
to
  <script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

In the last step, the URL starts with https:// rather than http://. It is because we host github pages via https. If we use http:// to get the js file, modern browsers will issue a warning of blocked:mixed-content and refuse to download it.

Test math: $a = \sqrt{2} + 3$.

The final page is

![](/images/hexo-setup.png)
