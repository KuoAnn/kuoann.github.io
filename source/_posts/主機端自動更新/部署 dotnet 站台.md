---
title: 部署 dotnet 站台
categories: Others
tags: 
    - C#
    - Cmd
    - IIS
    - Git
date: 2023-02-24T10:40:58+08:00
---

使用 cli 實現主機端自動更新 git 並發布 dotnet 站台的腳本

``` sh
cd "D:\\Workspace\\FutureVision"
git pull
iisreset /stop
dotnet publish "D:\\Workspace\\FutureVision\\FutureVision" -c Release -o "D:\\wwwroot\\FutureVision"
iisreset /start
```
