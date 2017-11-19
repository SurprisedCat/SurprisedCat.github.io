---
layout: post
title: "Jekyll的默认配置"
author: Jekyll Official Documents
categories: Jekyll
tags: Jekyll
---

## Jekyll的默认配置规则

为了加深对Jekyll指令的了解，从官网上查找了 _config.yml 文件的默认配置$^{[1]}$，如下所示：

```liquid
# Where things are
source:       .
destination:  ./_site
plugins_dir:  ./_plugins
layouts_dir:  ./_layouts
data_dir:     ./_data
includes_dir: ./_includes
collections:  null

# Handling Reading
safe:         false
include:      [".htaccess"]
exclude:      []
keep_files:   [".git", ".svn"]
encoding:     "utf-8"
markdown_ext: "markdown,mkdown,mkdn,mkd,md"

# Filtering Content
show_drafts: null
limit_posts: 0
future:      false
unpublished: false

# Plugins
whitelist: []
gems:      []

# Conversion
markdown:    kramdown
highlighter: rouge
lsi:         false
excerpt_separator: "\n\n"
incremental: false

# Serving
detach:  false
port:    4000
host:    127.0.0.1
baseurl: "" # does not include hostname

# Outputting
permalink:     date
paginate_path: /page:num
timezone:      null

quiet:    false
defaults: []

# Markdown Processors
rdiscount:
  extensions: []

redcarpet:
  extensions: []

kramdown:
  auto_ids:       true
  footnote_nr:    1
  entity_output:  as_char
  toc_levels:     1..6
  smart_quotes:   lsquo,rsquo,ldquo,rdquo
  enable_coderay: false

  coderay:
    coderay_wrap:              div
    coderay_line_numbers:      inline
    coderay_line_number_start: 1
    coderay_tab_width:         4
    coderay_bold_every:        10
    coderay_css:               style

  redcloth:
    hard_breaks: true
```

## 参考文献
[1] [http://jekyll.com.cn/docs/configuration/](http://jekyll.com.cn/docs/configuration/)