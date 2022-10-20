---
title: [C#] Html to PDF with wkhtmltopdf.netcore
categories: C#
tags: 
    - C#
    - ASP.NET Core
    - PDF
abbrlink: 4215572637
date: 2022-10-15 19:26:10
---

某次接到的需求需實現以下功能，特此紀錄一下

1. 透過網頁傳入 html 字串並下載成 PDF 檔
2. 資料不落地，全程用 Stream 處理
   * 因具多台 AP Server，若產生實體檔案會需要多生一台代理伺服器
3. Header 動態浮水印，需加入圖片及建立日期
4. 密碼保護

原本預計採用常見的 [iTextSharp](https://www.nuget.org/packages?q=iTextSharp) 處理，但後來發現有中文亂碼的問題且轉換的相容性不太好，故決定改用 [wkhtmltopdf.netcore](https://www.nuget.org/packages/Wkhtmltopdf.NetCore/5.0.2-preview)，結果又發現該套件(此時是 v5.0.2 Preview 版)不支援加密功能，最終決定兩者混用

<!-- more -->

## Install

從 Github 或 Nuget 皆可下載，執行檔須依預設目錄結構存放並將屬性設為 `Copy Always`

``` text
.
├── Example
|   ├── Example.csproj
|   └── Rotativa
|   |   ├── Linux
|   |   |   └── wkhtmltopdf
|   |   ├── Mac
|   |   |   └── wkhtmltopdf
|   |   └── Windows
|   |       └── wkhtmltopdf.exe
└── Example.sln
```

### Github

<https://github.com/fpanaccia/Wkhtmltopdf.NetCore>

### Nuget

* [wkhtmltopdf.netcore](https://www.nuget.org/packages/Wkhtmltopdf.NetCore/5.0.2-preview)
* [iTextSharp](https://www.nuget.org/packages?q=iTextSharp)

## Client-Side

* Html

    ``` html
    <a class="btn download-pdf" role="button">PDF下載</a>
    ```

* jQuery

    ``` js
    $("a.btn.download-pdf").click(function () {
        // html 轉 Base64
        const PdfHtmlContent = btoa(encodeURIComponent($(".form").html().trim().replaceAll("\n", "")));

        $.ajax({
            url: '/dl/f/pdf',
            method: 'POST',
            cache: false,
            data: JSON.stringify({ PdfHtmlContent }),
            contentType: "application/json",
            xhrFields: {
                responseType: "blob"
            },
            success: function (response) {
                const $a = document.createElement("a")
                const url = URL.createObjectURL(response)
                $a.download = "檔案名稱.pdf"
                $a.href = url
                $a.click()
                setTimeout(() => URL.revokeObjectURL(url), 5000)
            }
        }).fail(function (xhr, textStatus) {
            alert("下載失敗：" + textStatus);
        });
    });
    ```

## Server-Side

* Startup.cs

    ``` csharp
    public void ConfigureServices(IServiceCollection services)
        // 註冊 wkhtmltopdf.netcore
        services.AddWkhtmltopdf();
    }
    ```

* Controller

    ``` csharp
    [Route("dl")]
    [ApiController]
    public class DownloadFileController : ControllerBase
    {
        private readonly IGeneratePdf _generatePdf;
        private readonly IConfiguration _configuration;
        private readonly IWebHostEnvironment _webHostEnvironment;
        private readonly ILogger<DownloadFileController> _logger;

        public DownloadFileController(IGeneratePdf generatePdf,
            IConfiguration configuration,
            IWebHostEnvironment webHostEnvironment,
            ILogger<DownloadFileController> logger)
        {
            _generatePdf = generatePdf;
            _configuration = configuration;
            _webHostEnvironment = webHostEnvironment;
            this._logger = logger;
        }

        [HttpPost]
        [Route("f/pdf")]
        public async Task<IActionResult> GetPdf([FromBody] PdfModel model)
        {
            var result = new JObject();

            try
            {
                var password = "abcd1234";
                var rootPath = _webHostEnvironment.ContentRootPath;
                var base64EncodedBytes = Convert.FromBase64String(model.PdfHtmlContent);
                var htmlString = HttpUtility.UrlDecode(Encoding.UTF8.GetString(base64EncodedBytes));
                var pdfModel = new PdfModel()
                {
                    RootPath = rootPath,
                    PdfHtmlContent = htmlString,
                };

                // 浮水印參數
                var headerMap = new Dictionary<string, string>
                {
                    { "year", DateTime.Now.AddYears(-1911).ToString("yyy") },
                    { "month", DateTime.Now.AddYears(-1911).ToString("MM") },
                    { "day", DateTime.Now.AddYears(-1911).ToString("dd") },
                    { "hour", DateTime.Now.AddYears(-1911).ToString("HH") },
                    { "minute", DateTime.Now.AddYears(-1911).ToString("mm") },
                };

                // PDF 參數
                _generatePdf.SetConvertOptions(new ConvertOptions
                {
                    PageMargins = new Wkhtmltopdf.NetCore.Options.Margins() { Top = 20, Bottom = 0, Left = 0, Right = 0 },
                    HeaderHtml = Path.Combine(rootPath, "wwwroot", "html", "pdf-header.html"),
                    Replacements = headerMap,
                });

                // wkhtmltopdf 產生 PDF byte 資料
                var pdfByte = await _generatePdf.GetByteArray("Pages/Template/Pdf.cshtml", pdfModel);

                // iTextSharp 讀取 byte 資料後處理加密
                var reader = new PdfReader(pdfByte);
                using (var ms = new MemoryStream())
                {
                    PdfEncryptor.Encrypt(reader, ms, true, password, password, PdfWriter.ALLOW_PRINTING);
                    pdfByte = ms.ToArray();
                }

                // 輸出成資料流
                var output = new MemoryStream();
                output.Write(pdfByte, 0, pdfByte.Length);
                output.Position = 0;

                // 這邊的檔名沒那麼重要，最終會由前端決定實際檔名
                return File(output, "application/pdf", "myfile.pdf");
            }
            catch (Exception ex)
            {
                return StatusCode(StatusCodes.Status500InternalServerError, new
                {
                    rtnCode = "-9999",
                    errMsg = ex.Message
                });
            }
        }
    }
    ```

* PdfModel.cs

    ``` csharp
    public class PdfModel
    {
        public string RootPath { get; set; }
        public string PdfHtmlContent { get; set; }
    }
    ```

### Template

* Pdf 樣板: `Pdf.cshtml`

    ``` html
    @model Model.PdfModel
    @{
        Layout = null;
    }
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <title>PDF</title>
        <meta charset="UTF-8">
        <meta name="viewport"
            content="width=device-width, initial-scale=1, shrink-to-fit=no, minimum-scale=1.0, maximum-scale=1.0">

        <!-- Bootstrap core CSS -->
        <link rel="stylesheet" href="@(Model.RootPath + "/wwwroot/vendors/bootstrap/bootstrap.min.css")" />
        <link rel="stylesheet" href="@(Model.RootPath + "/wwwroot/vendors/iconfonts/style.css")" />
        <link rel="stylesheet" href="@(Model.RootPath + "/wwwroot/vendors/color/color.css")" />
        <!-- Custom styles for website -->
        <link rel="stylesheet" href="@(Model.RootPath + "/wwwroot/css/fonts.css")" />
        <link rel="stylesheet" href="@(Model.RootPath + "/wwwroot/css/main.css")" />
        <link rel="stylesheet" href="@(Model.RootPath + "/wwwroot/css/style.css")" />

    </head>
    <body>
        <div class="container-fluid project_bg px-0" style="padding-top: 20px;padding-bottom: 20px;">
            <div class="row mt-2 mx-auto" style="max-width: 1100px;">
                <div class="col-md-8 col-11 mx-auto">
                    <form id="msform">
                        <fieldset class="row">
                            @Html.Raw(Model.PdfHtmlContent)
                        </fieldset>
                    </form>
                </div>
            </div>
        </div>
    </body>
    </html>
    ```

* Header 浮水印樣板: `pdf-header.html`

    ``` html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <script charset="utf-8">
            function replaceParams() {
                var url = window.location.href.replace(/#$/, "");
                var params = (url.split("?")[1] || "").split("&");
                for (var i = 0; i < params.length; i++) {
                    var param = params[i].split("=");
                    var key = param[0];
                    var value = param[1] || '';

                    if (key == "hour") {
                        try {
                            var realHour = parseInt(value);
                            var ampmText = "上午";
                            if (realHour >= 12) {
                                ampmText = "下午";
                                if (realHour > 12) {
                                    value = realHour - 12;
                                }
                            }

                            document.getElementById("self-report-date").innerText =
                                document.getElementById("self-report-date").innerText.replace(new RegExp('{ampm}', 'g'), ampmText);
                        } catch (e) { }
                        finally {
                            document.getElementById("self-report-date").innerText =
                                document.getElementById("self-report-date").innerText.replace(new RegExp('{hour}', 'g'), value);

                        }
                    }
                    else {
                        document.getElementById("self-report-date").innerText =
                            document.getElementById("self-report-date").innerText.replace(new RegExp('{' + key + '}', 'g'), value);
                    }
                }
            }
        </script>
    </head>
    <body style="margin: 0;" onload="replaceParams()">
        <div style="width: 420px; margin: 0 auto; overflow: hidden;">
            <div style="width: 80px; float: left;">
                <img src="../images/pdf-header-icon.png" style="display: block; width: 100%;" />
            </div>
            <div style="float: left; width: 310px; padding: 16px 0px 0px 6px; font-family: 'Microsoft JhengHei'; font-weight: bolder;">
                <div id="self-report-date" style="padding-bottom: 4px;color:red">下載日期{year}年{month}月{day}日{ampm}{hour}:{minute}</div>
            </div>
        </div>
    </body>
    </html>
    ```
