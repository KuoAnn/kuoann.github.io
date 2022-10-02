---
title: Github Page Blog with Hexo
abbrlink: 534415561
date: 2022-10-02 21:09:06
categories: Others
tags: 
    - Github
    - Github Page
    - Github Action
    - Hexo
    - Hexo NexT
---

# Github Page Blog with Hexo and NexT

本篇會著重在 Hexo 的初始設定

## 起源

最近決定養成寫 Blog 的習慣，以隨手筆記技術文章，基於以下理由決定選用 Github Page + Hexo + NexT

1. 不想花太多撰寫時間 (太麻煩也會懶得寫) -> Markdown
2. 不想維護站台 -> Github Page
3. 畫面簡單易讀 -> Hexo + NexT (Theme)
4. 具搜尋功能 -> 方便未來查找筆記

<!-- more -->

## Hexo

Hexo 是一個靜態頁面生成框架，以下是常用的指令，相關設定可參考 <https://hexo.io/zh-tw/docs/>

### 安裝 Hexo-cli

``` sh
npm install -g hexo-cli
```

### 初始化

``` sh
hexo init <folder>
cd <folder>
npm i
```

### 撰寫新文章

``` sh
hexo new [layout] <title>
```

### 產生靜態檔

產生靜態檔至 public 資料夾

* -w,--watch: 監看檔案變更
* -d,--deploy: 產生完成即部署網站

``` sh
hexo generate --watch
hexo g -w
```

### 本機測試

``` sh
hexo server
hexo s
```

### 清除靜態檔

清除 public 資料夾

``` sh
hexo clean
```

### 手動部署網站

* -g, --generate: 部署網站前先產生靜態檔案

``` sh
hexo deploy --generate
hexo d -g
```

須於設定檔完成相關設定才有效 <https://hexo.io/docs/one-command-deployment>

``` yml
deploy:
  type: 'git'
  repo: <https://github.com/KuoAnn/kuoann.github.io.git>
  branch: gh-pages
```

## 使用 Github Action 自動部署

<https://hexo.io/zh-tw/docs/github-pages>

### 目的

Github Repo 偵測到 main branch 有新版本時會自動部署

* 部署狀況可至 Action 頁籤觀察

### 步驟

1. 儲存庫根目錄下建立 .github/workflows/pages.yml
2. 貼上以下設定，node-version 需修改為本機 node.js 版本

    ``` yml
    name: Pages

    on:
    push:
        branches:
        - main  # default branch

    jobs:
    pages:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Use Node.js 16.x
            uses: actions/setup-node@v2
            with:
            node-version: '16'
        - name: Cache NPM dependencies
            uses: actions/cache@v2
            with:
            path: node_modules
            key: ${{ runner.OS }}-npm-cache
            restore-keys: |
                ${{ runner.OS }}-npm-cache
        - name: Install Dependencies
            run: npm install
        - name: Build
            run: npm run build
        - name: Deploy
            uses: peaceiris/actions-gh-pages@v3
            with:
            github_token: ${{ secrets.GITHUB_TOKEN }}
            publish_dir: ./public
    ```

3. push pages.yml
4. 在儲存庫中前往 **Settings > Pages > Source**，並將 branch 改為 gh-pages
