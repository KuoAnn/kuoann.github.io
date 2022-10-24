---
title: Hexo 推薦外掛及相關設定
tags:
  - Hexo
  - Hexo NexT
abbrlink: 587752702
date: 2022-10-05 21:18:53
categories: Others
---

## 推薦外掛

1. **hexo-abbrlink**: 產生永久連結 for SEO，建立文章時會產生隨機編號的 url，以避免更換文章結構(/2022/10/5/xxx)時連結失效，同時也能避開中文的 urlencoding 讓連結更簡短些
2. **hexo-filter-nofollow**: 避免爬蟲爬到外部連結會回不來 for SEO
3. **hexo-generator-sitemap**: 產生 sitemap.xml for SEO
4. **hexo-generator-searchdb**: 搜尋功能
5. **hexo-generator-feed**: 產生 RSS

相關設定可參考最下方的 [外掛設定](#外掛設定)

> [Plugins](https://hexo.io/plugins/)

## Hexo 設定

直接參考以下設定檔 `_config.yml` 註解
> Hexo version: 6.3.0
> [官方設定說明](<https://hexo.io/zh-tw/docs/>)

<!-- more -->
### 站台設定

1. permalink: SEO用，避免URL太長或中文被encode，需安裝`hexo-abbrlink`
    * [permalink 官方說明](https://hexo.io/zh-cn/docs/permalinks.html)

``` yml
# Site
title: KuoAnn's Gate
subtitle: '郭安的真理之門'
description: 這裡是郭安的筆記碎片<br>因為是碎片所以看不懂很正常
keywords: 'KuoAnn,github page'
author: KuoAnn
language: 'zh-TW'
timezone: 'Asia/Taipei'

# URL
url: https://kuoann.github.io/
permalink: posts/:abbrlink/
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks

# Directory > 預設產檔的資料夾
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:
```

### 寫作設定

1. external_link: 啟用以新分頁開啟外部連結
2. post_asset_folder: 啟用後，在建立檔案時，Hexo 會自動建立一個與文章同名的資料夾，可用來放置文章相關資源
   * marked/prependRoot + postAsset: 啟用後可直接用 Markdown 抓到對應資料夾的資源
     * Ex. `![](image.jpg)` -> `<img src="/2020/01/02/foo/image.jpg">`
3. highlight: 預設使用 highlight.js 設定；highlight/prismjs 只能擇一啟用

``` yml
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link:
  enable: true # Open external links in new tab
  field: site # Apply to the whole site
  exclude: ''
filename_case: 0
render_drafts: false
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: false
  tab_replace: ''
  wrap: true
  hljs: false
prismjs:
  enable: false
  preprocess: true
  line_number: false
  tab_replace: ''
```

### 顯示設定

``` yml
# 首頁顯示
index_generator:
  path: ''
  per_page: 10
  # 依日期反序
  order_by: -date

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Metadata elements
## https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta
meta_generator: true

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss
## updated_option supports 'mtime', 'date', 'empty'
updated_option: 'mtime'

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10
pagination_dir: page

# Include / Exclude file(s)
## include:/exclude: options only apply to the 'source/' folder
include: []
exclude: []
ignore:
```

## 外掛設定

``` yml
# hexo-abbrlink
abbrlink:
  alg: crc32  # 演算法：crc16(default) and crc32 
  rep: dec    # 進位制：dec(default) and hex

# hexo-filter-nofollow
nofollow:
  enable: true
  field: site
  exclude:  # 不加上 nofollow 的友情連結可以放在這邊。

# hexo-generator-sitemap
sitemap:
  path: sitemap.xml

# hexo-generator-feed
feed:
  enable: true
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
  icon: icon.png
  autodiscovery: true
  template:

# hexo-generator-searchdb
search:
  path: search.json
  field: post
  format: html
  limit: 10000
```

### 主題設定

本站使用 [NexT](<https://theme-next.js.org/>)，細節設定可參考下一篇

``` yml
theme: next
```

### 手動佈署設定

``` yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: 'git'
  repo: https://github.com/KuoAnn/kuoann.github.io.git
  branch: gh-pages
```
