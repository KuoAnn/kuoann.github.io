---
title: 自動更新當前目錄下所有 Git 儲存庫
categories: Git
tags: 
    - Git
    - Bash
date: 2023-04-23T15:46:19+08:00
---

以下 Bash 腳本會自動 Pull 當前目錄下所有儲存庫的程式碼：

``` shell
#!/bin/bash

for dir in */; do
    if [ -d "$dir/.git" ]; then
        echo "Updating $dir"
        cd "$dir"
        git pull
        cd ..
    fi
done
```
