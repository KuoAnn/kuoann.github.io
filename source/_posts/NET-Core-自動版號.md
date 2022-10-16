---
title: .NET Core 自動版號
categories: C#
tags:
  - C#
  - ASP.NET Core
  - Visual Studio
abbrlink: 19958632
date: 2022-10-16 15:08:26
---

以下範例是利用專案檔的 `<VersionSuffix>` 指定時間格式，並於建置時自動使用時間更新版號

* Ex. 建置時間：`2022/10/16 15:07` -> 版號：`1.22.1016.1507`
* 可使用 `Condition` 屬性增加其他判斷

<!-- more -->

## .csproj

``` xml
<PropertyGroup>
    <VersionSuffix>1.$([System.DateTime]::Now.ToString(yy)).$([System.DateTime]::Now.ToString(MMdd)).$([System.DateTime]::Now.ToString(HHmm))</VersionSuffix>
    <AssemblyVersion Condition=" '$(VersionSuffix)' == '' ">9.9.9.9</AssemblyVersion>
    <AssemblyVersion Condition=" '$(VersionSuffix)' != '' ">$(VersionSuffix)</AssemblyVersion>
    <Version Condition=" '$(VersionSuffix)' == '' ">9.9.9.9</Version>
    <Version Condition=" '$(VersionSuffix)' != '' ">$(VersionSuffix)</Version>
</PropertyGroup>
```

## 取得版本號

``` csharp
var assemblyVersion = Assembly.GetEntryAssembly().GetName().Version;
var fileVersion = Assembly.GetEntryAssembly().GetCustomAttribute<AssemblyFileVersionAttribute>().Version;
var informationVersion = Assembly.GetEntryAssembly().GetCustomAttribute<AssemblyInformationalVersionAttribute>().InformationalVersion;
```
