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

最近決定養成寫 Blog 的習慣，以隨手筆記技術文章，基於以下理由決定選用 Github Page + Hexo + NexT

1. 快速寫作 -> Markdown
2. 自主管理相關資源 -> Git
3. 免費 -> Github Page
4. 具搜尋功能，方便查找筆記 -> Hexo
5. 畫面簡單易讀 -> Hexo + NexT (Theme)

<!-- more -->

## Hexo

[Hexo](https://hexo.io/zh-tw/) 是一個靜態頁面生成框架，搭配 Github 的話也能在線上編輯

### Hexo-cli

Require

* Node.js
* Hexo-cli

``` sh
npm install -g hexo-cli
```

### 初始化

``` sh
hexo init <folder>
cd <folder>
npm i
```

## 寫作

### 新增文章

``` sh
hexo new [layout] <title>
```

### 新增草稿

建立時會預設儲存於 source/_drafts 資料夾

``` sh
hexo new draft <title>
```

### 發布草稿

``` sh
hexo publish <title>
```

## 部署

### 產生靜態檔

產生靜態檔至 public 資料夾

* -w,--watch: 監看檔案變更
* -d,--deploy: 產生完成即部署網站

``` sh
hexo generate --watch
hexo g -w
```

### 清除靜態檔

清除 public 資料夾及相關部署檔

``` sh
hexo clean
```

### 本機部署

需安裝外掛 hexo-server

``` sh
npm i hexo-server
hexo server
hexo s
```

### 手動部署

需安裝外掛

* -g, --generate: 部署網站前先產生靜態檔案

``` sh
npm i hexo-deployer-git
hexo deploy --generate
hexo d -g
```

須於設定檔完成相關設定才有效
> 參考： <https://hexo.io/docs/one-command-deployment>

``` yml
deploy:
  type: 'git'
  repo: <https://github.com/KuoAnn/kuoann.github.io.git>
  branch: gh-pages
```

### 自動部署 - Github Action

> 參考： <https://hexo.io/zh-tw/docs/github-pages>

1. 儲存庫根目錄下建立 .github/workflows/pages.yml
2. 貼上以下設定，須注意第 16 行的 node-version 需修改為本機 node.js 版本

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
4. 在儲存庫中前往 **Settings > Pages > Source**，並將 branch 改為 `gh-pages`
5. 當 Github Action 偵測到 main branch 有新版本時會自動部署

   * 部署狀況可至 Action 頁籤觀察

## 設定

> 請參考 [Hexo 推薦外掛及相關設定](/posts/587752702)
