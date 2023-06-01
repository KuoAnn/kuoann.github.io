---
title: Change Git Upstream to Origin
categories: Git
tags: 
    - Git
    - Bash
date: 2023-06-01T20:49:34+08:00
---

``` sh
#!/bin/sh

# 遍歷資料夾下的所有子資料夾
for dir in */; do
  # 進入子資料夾
  cd "$dir" || continue

  # 檢查是否為 git 專案
  if [ -d ".git" ]; then
    # 獲取當前分支名稱
    current_branch=$(git symbolic-ref --short HEAD)

    # 設置 upstream 至 origin 下
    git branch --set-upstream-to="origin/$current_branch" "$current_branch"
  fi

  # 返回上一級資料夾
  cd ..
done
```
