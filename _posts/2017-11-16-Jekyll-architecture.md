---
layout: post
title:  "Jekyll 结构分析"
categories: Jekyll
tags:  Jekyll Framework
author: SurprisedCat
---

* content 
{:toc}

## 1. Jekyll目录结构分析

Jekyll（发音/'dʒiːk əl/）是一个静态站点生成器，它会根据网页源码生成静态文件（纯HTML+CSS+JS）。它提供了模板、变量、插件等功能，所以实际上可以用来编写整个网站。Jekyll是基于Ruby语言开发的，因此安装Jekyll需要Ruby以及相关的组件，具体安装可以参考[Jekyll的中文网站安装指南](http://jekyllcn.com/docs/installation/)。使用Jekyll可以让我们尽量少的接触网站相关的知识的同时，搭建出漂亮的个人博客网站。本文采用自顶向下的方式来介绍Jekyll如何实现建站。

Jekyll 的核心其实是一个文本转换引擎。它的概念其实就是：你用你最喜欢的标记语言来写文章，可以是 Markdown, 也可以是 Textile, 或者就是简单的 HTML, 然后 Jekyll 就会帮你套入一个或一系列的布局中。在整个过程中你可以设置 URL 路径，你的文本在布局中的显示样式等等。这些都可以通过纯文本编辑来实现，最终生成的静态页面就是你的成品了。$^{[1]}$

一个基本的 Jekyll 网站的目录结构一般是像这样的：

excerpt_separator_here

```shell
.
├── _config.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.textile
|   └── on-simplicity-in-technology.markdown
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _page
|   |—— category.html
|   |—— tags.html
|   └── archive.html
├── js
|   └── main.js
├── css
|   └── main.css
├── _site
├── .jekyll-metadata
└── index.html
```

更精确的说，目录是一个**迭代包括**的结构,大体来说，可以分为这几个部分：(请保持足够的屏宽来看这个插图)

```shell
    --------------     -----------------       ---------------  
    |   page YML |     |    _posts YML |       | index.html  |  --    --------------
    --------------     -----------------       ---------------    |<--| _config.yml|
        ^                     ^                      ^		  |   --------------
        |_____________________|______________________|		  |   --------------
                   |                      |                       |<--| js css sass|
                   |                      |                       |   --------------
           ------------------       ----------------              |
	   |    _layouts    |<------|   _includes  |______________|
           ------------------       ----------------                 
		^ YML  |                ^      |                    
                |______|                |______|
```

其中，_includes文件中是常用代码快，可以被包含在模板（ _layouts ）中，用来组成模板。includes之间也快成互相包括，模板之间也可互相包括。最后页面（ _posts,page,index.html）包含所需模板实现一个完整的页面。以[Gaohaoyang](https://github.com/Gaohaoyang/gaohaoyang.github.io)的index.html为例(个人非常喜欢他改进的这个Theme)。这里就是使用了default模板，所有的内容都被放在的default模板的{{content}}里面。

```html
index.html
---
layout: default
---

<div class="page clearfix" index>
    <div class="left">
        <h1>Welcome to HyG's Blog!</h1>
        <small>这里记录着我的前端学习之路</small>
        <hr>
        <ul>
    ....................中间省略.........................
                   <ul  class="content-ul">

                </ul>
            </div> -->
        </div>
    </div>
</div>
<!-- <script src="{{ "/js/scroll.min.js " | prepend: site.baseurl }}" charset="utf-8"></script> -->
<!-- <script src="{{ "/js/pageContent.js " | prepend: site.baseurl }}" charset="utf-8"></script> -->
```

这个是default.html模板。它包括了一些代码片段（ head.html、header.html、footer.html、backToTop.html ）这些都是在_includes文件夹中的内容，{{content}}表示如果有网页使用了这个模板，内容就会放在这个位置,比如上面index.html的内容就是替换了{{content}}的部分。这也解释了为什么index.html中没有常见的一些头信息。此外，这个模板还包含了一些公用的js文件（main.js、smooth-scroll.min.js）。

```html
default.html
<!DOCTYPE html>
<html>

  {% include head.html %}

  <body>

    {% include header.html %}

        {{ content }}

    {% include footer.html %}
    {% include backToTop.html %}
    <script src="{{ "/js/main.js " | prepend: site.baseurl }}" charset="utf-8"></script>
    <script src="{{ "/js/smooth-scroll.min.js " | prepend: site.baseurl }}" charset="utf-8"></script>
    <script type="text/javascript">
      smoothScroll.init({
        speed: 500, // Integer. How fast to complete the scroll in milliseconds
        easing: 'easeInOutCubic', // Easing pattern to use
        offset: 20, // Integer. How far to offset the scrolling anchor location in pixels
      });
    </script>
    <!-- <script src="{{ " /js/scroll.min.js " | prepend: site.baseurl }}" charset="utf-8"></script> -->
  </body>

</html>
```

### 1.1Theme的包含结构

在这个Theme中，包含结构大致如下：

- index.html-->default.html(包含head.html(**网页的头信息**)、header.html、footer.html、backToTop.html)
- 0archives.html、1categoty.html、2tags.html (包含tag.html、category.html)-->default.html(包含head.html(**网页的头信息**)、header.html、footer.html、backToTop.html)
- pages（ e.g.3collections.md、4about.md )-->page.html(包括pageContent.js)-->default.html(包含head.html(**网页的头信息**)、header.html、footer.html、backToTop.html)
- _posts(文章)-->post.html（包含tag.html、category.html、previousAndNext.html）-->default.html
- _drafts(草稿)-->post.html（包含tag.html、category.html、previousAndNext.html）-->default.html

此外，还有一些js，css，sass文件。Jekyll的大体结构就是这样了。如果想了解每一个文件夹的具体内容可以参考Jekyll的中文文档:[将纯文本转换为静态博客网站](http://jekyllcn.com/docs/home/)。

## 2. 参考文献

[1] [http://jekyllcn.com/docs/structure/](http://jekyllcn.com/docs/structure/)
