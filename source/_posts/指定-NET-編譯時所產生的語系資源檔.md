---
title: 指定 .NET 編譯時所產生的語系資源檔
categories: C#
tags:
  - C#
  - ASP.NET Core
  - .csproj
  - Visual Studio
abbrlink: 2071791023
date: 2022-10-19 22:38:55
---

專案在編譯支援多語系的程式庫時，可能會產生一堆用不到的語系資源 ([衛星組件 (Satellite Assembly)](https://learn.microsoft.com/en-us/dotnet/core/extensions/create-satellite-assemblies?WT.mc_id=DOP-MVP-37580))。若僅會用到部分語系時，可於專案檔 (*.csproj) 內加入 <[SatelliteResourceLanguages](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/msbuild-props?WT.mc_id=DOP-MVP-37580#satelliteresourcelanguages)>

``` xml
  <PropertyGroup>
    <SatelliteResourceLanguages>en;zh-TW</SatelliteResourceLanguages>
  </PropertyGroup>
```

<!-- more -->
## 參考

> [【笨問題】防止 .NET 編譯產生不需要的多語系資源檔](<https://blog.darkthread.net/blog/filter-res-lang-files/>)
