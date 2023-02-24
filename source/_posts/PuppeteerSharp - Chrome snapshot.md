---
title: Puppeteer Sharp - Chrome snapshot
categories: C#
tags: 
    - C# 
    - Chrome Headless
    - Puppeteer Sharp
date: 2023-02-12T20:40:31+08:00
---

Chrome 自 59 版起內建了 [Headless](<https://developer.chrome.com/blog/headless-chrome/>) 模式，允許透過命令列啟動 Chrome 以無 GUI 方式執行，藉此可透過 C# 套件快速實作開啟網頁並擷圖等功能

<!-- more -->

## Puppeteer Sharp 套件

可透過 Nuget 下載 Puppeteer Sharp 套件，不需在伺服器安裝 Chrome，它會自行下載 Chromium

``` xml
<!-- .csproj -->
<ItemGroup>
    <PackageReference Include="PuppeteerSharp" Version="9.0.2" />
</ItemGroup>
```

## Main Method

``` csharp
using System.Text.RegularExpressions;
using PuppeteerSharp;

public class Job
{
    public string Title;
    public string Url;
    public bool Pass;
    public string Message;
}

public class Chrome : IDisposable
{
    private IBrowser browser;
    private IPage page;
    private int width, height;

    public Chrome(int width = 1024, int height = 768)
    {
        var path = Microsoft.Win32.Registry.GetValue(@"HKEY_CLASSES_ROOT\ChromeHTML\shell\open\command", null, null) as string;
        if (string.IsNullOrEmpty(path))
            throw new ApplicationException("Chrome not installed");
        var m = Regex.Match(path, "\"(?<p>.+?)\"");
        if (!m.Success)
            throw new ApplicationException($"Invalid Chrome path - {path}");
        var chromePath = m.Groups["p"].Value;
        browser = Puppeteer.LaunchAsync(new LaunchOptions
        {
            Headless = false,
            ExecutablePath = chromePath
        }).Result;
        page = browser.PagesAsync().Result.First();
        this.width = width;
        this.height = height;
    }

    public void Navigate(Job job)
    {
        try
        {
            page.SetViewportAsync(new ViewPortOptions
            {
                Width = width,
                Height = height
            }).Wait();
            var response = page.GoToAsync(job.Url, 30000,
                waitUntil: new WaitUntilNavigation[] {
                            WaitUntilNavigation.Load,
                            WaitUntilNavigation.Networkidle2
                }).GetAwaiter().GetResult();
            job.Pass = response.Status == System.Net.HttpStatusCode.OK;
            if (!job.Pass)
            {
                job.Message = $"** {response.StatusText} **\n{response.TextAsync().Result}";
            }
        }
        catch (Exception ex)
        {
            job.Pass = false;
            job.Message = ex.Message;
        }
    }

    public void TakeSnapshot(string path)
    {
        var h = Convert.ToInt32((float)page.EvaluateExpressionAsync(
            @"Math.max(document.body.scrollHeight, document.documentElement.scrollHeight)").Result);
        var w = Convert.ToInt32((float)page.EvaluateExpressionAsync(
            @"document.body.getBoundingClientRect().width").Result);
        page.SetViewportAsync(new ViewPortOptions
        {
            Width = w,
            Height = h
        }).Wait();
        page.ScreenshotAsync(path).GetAwaiter().GetResult();
    }

    public void Dispose()
    {
        if (page != null) page.Dispose();
        if (browser != null) browser.Dispose();
    }
}
```

## Using

``` csharp
using (var browser = new Chrome(1500, 300))
{
    var job = new Job
    {
        Title = "Google",
        Url = "https://www.google.com/"
    };
    browser.Navigate(job);

    if (job.Pass)
    {
        var imgDirectory = Path.Combine(Directory.GetCurrentDirectory(), "Exports");
        var imgPath = Path.Combine(imgDirectory, job.Title + ".png");
        if (!Directory.Exists(imgDirectory))
        {
            Directory.CreateDirectory(imgDirectory);
        }
        browser.TakeSnapshot(imgPath);
    }
    else
    {
        Console.WriteLine("");
    }
}
```

## 參考資料

> <https://blog.darkthread.net/blog/puppeteer-sharp/>
