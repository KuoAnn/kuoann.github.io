---
title: Hexo Next Theme
categories: Others
abbrlink: 2185127750
date: 2022-10-23 20:14:48
tags:
  - Hexo
  - Hexo NexT
---

本站 Hexo 使用 [NexT](<https://theme-next.js.org/>) 主題，須注意 Github Repo 有新舊版之分，舊版已不再維護，為避免混亂以下僅提供新版連結

> <https://github.com/next-theme/hexo-theme-next>

## 安裝

官方提供兩種安裝方式：

1. npm 下載 (建議)

    ``` node
    npm install hexo-theme-next
    ```

2. Git Clone

    ``` git
    git clone https://github.com/next-theme/hexo-theme-next themes/next
    ```

Hexo 的 `_config.yml` 須將 theme 切換至 `next`

``` yml
## Themes: https://hexo.io/themes/
# theme: landscape
theme: next
```

## NexT 設定

詳細設定內容網路上滿多，以下僅提供需特別注意的部分

* 若是用 git clone 的方式安裝，請盡量避免直接修改 themes 下的 `_config.yml`。
* Hexo 本身有提供覆寫方法，可於根目錄下建立 `_config.next.yml`，並將相關設定無腦複製進來再做修改。

<!-- more -->

## NexT 標籤語法

### Note

可直接用 Html 或是標籤撰寫文章

``` html
<div class="note default"><p>Html範例</p></div>
{% note default %}標籤範例{% endnote %}
{% note primary %}標籤範例{% endnote %}
{% note success %}標籤範例{% endnote %}
{% note info %}標籤範例{% endnote %}
{% note warning %}標籤範例{% endnote %}
{% note danger %}標籤範例{% endnote %}
```

Sample:

<div class="note default"><p>Html範例</p></div>
{% note default %}標籤範例{% endnote %}
{% note primary %}標籤範例{% endnote %}
{% note success %}標籤範例{% endnote %}
{% note info %}標籤範例{% endnote %}
{% note warning %}標籤範例{% endnote %}
{% note danger %}標籤範例{% endnote %}

相關樣式可於 `_config.next.yml` 設定：

``` yaml
# Note tag (bootstrap callout)
note:
  # Note tag style values:
  #  - simple    bootstrap callout old alert style. Default.
  #  - modern    bootstrap callout new (v2-v3) alert style.
  #  - flat      flat callout style with background, like on Mozilla or StackOverflow.
  #  - disabled  disable all CSS styles import of note tag.
  style: modern
  icons: true
  # Offset lighter of background in % for modern and flat styles (modern: -12 | 12; flat: -18 | 6).
  # Offset also applied to label tag variables. This option can work with disabled note tag.
  light_bg_offset: 0
```

### Label

``` text
{% label default@default標籤 %}
{% label primary@primary標籤 %}
{% label success@success標籤 %}
{% label info@info標籤 %}
{% label warning@warning標籤 %}
{% label danger@danger標籤 %}
```

Sample:

{% label default@default標籤 %}
{% label primary@primary標籤 %}
{% label success@success標籤 %}
{% label info@info標籤 %}
{% label warning@warning標籤 %}
{% label danger@danger標籤 %}

## 自訂樣式

與設定檔一樣，不建議直接修改原始檔內容，可於 `_config.next.yml` 啟用自訂內容

``` yml
custom_file_path:
  #head: source/_data/head.njk
  #header: source/_data/header.njk
  sidebar: source/_data/sidebar.njk
  #postMeta: source/_data/post-meta.njk
  #postBodyEnd: source/_data/post-body-end.njk
  #footer: source/_data/footer.njk
  #bodyEnd: source/_data/body-end.njk
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl
```

`styles.styl`：本站有特別調整的樣式內容如下

``` css
$heavy-bluegreen = #005757;
$bg-green = #D1E9E9;

body {
    background-color: $bg-green;
}

.posts-expand .post-title, .posts-expand .post-title-link {
  color: $heavy-bluegreen;
  font-weight: bold;
}

.exturl {
    color: #3D7878;
}

// 短 Code 樣式
code {
    color: #c7254e; //文字顏色
    background: #f9f2f4; //底色
    margin: 2px;
}
.highlight, pre {
    margin: 5px 0;
    padding: 4px;
    border-radius: 3px;
}
.highlight, code, pre {
    border: 1px solid #d6d6d6;
}

// 修改選中文字底色
/* webkit, opera, IE9 */
::selection { 
    background: #408080;
    color: #f7f7f7;
}
/* firefox */
::-moz-selection {
    background: #408080;
    color: #f7f7f7;
}
```
