---
title: OpenAI API in Javascript
categories: Javascript
tags:
  - Openai
  - ChatGPT
  - Javascript
abbrlink: 3263803727
date: 2023-05-26 09:13:29
---

* 透過 Js 發送 OpenAI 官方提供的 API (<https://api.openai.com/v1/chat/completions>)，需至 [官方網站](https://platform.openai.com/account/api-keys) 申請 API Key 才能使用
* API 規格參考：
    > <https://platform.openai.com/docs/api-reference/making-requests>
<!-- more -->

## Rq

### Header

``` sh
curl https://api.openai.com/v1/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "text-davinci-003",
    "prompt": "Say this is a test",
    "max_tokens": 7,
    "temperature": 0
  }'
```

### Body

``` js
{
     "model": "gpt-3.5-turbo",
     "messages": [{"role": "user", "content": "Say this is a test!"}],
     "temperature": 0.7
}
```

* model：指定語言模型，不同語言模型的上行內容會不同，以下以 `gpt-3.5-turbo` 參考
  * 雖上行指定 `gpt3.5-turbo`，但從下行的資訊可以看出實際使用的會是 `gpt-3.5-turbo-0301`，但該模型
* message：
  * role=`system`：指定角色(非必填)，可用中文，範例用英文的目的是為了降 Token 數，以節省成本
  * role=`user`：問題內容，如有延續關係可串多組
* temperature：What sampling temperature to use, between 0 and 2. Higher values like 0.8 will make the output more random, while lower values like 0.2 will make it more focused and deterministic.

## Rs

``` js
{
   "id":"chatcmpl-abc123",
   "object":"chat.completion",
   "created":1677858242,
   "model":"gpt-3.5-turbo-0301",
   "usage":{
      "prompt_tokens":13,
      "completion_tokens":7,
      "total_tokens":20
   },
   "choices":[
      {
         "message":{
            "role":"assistant",
            "content":"\n\nThis is a test!"
         },
         "finish_reason":"stop",
         "index":0
      }
   ]
}
```

## Js Sample

範例以 Vanilla js 開發，可使用其他能 POST 的套件

``` js
function send2Gpt(sQuestion) {
    if (OPENAI_API_KEY == null) {
        OPENAI_API_KEY = prompt("Enter Your OPENAI_API_KEY", "");
        if (OPENAI_API_KEY === null) {
            alert("You did not enter OPENAI_API_KEY");
            return;
        }
    }
    if (sQuestion === "") {
        alert("Type in your question!");
        return;
    }

    let q = "Translate into zh-TW\n" + sQuestion;
    console.log("Send Gpt: " + q);

    $("#btnTransAll").text("Translating...").prop("disabled", true);

    const oHttp = new XMLHttpRequest();
    oHttp.open("POST", "https://api.openai.com/v1/chat/completions");
    oHttp.setRequestHeader("Accept", "application/json");
    oHttp.setRequestHeader("Content-Type", "application/json");
    oHttp.setRequestHeader("Authorization", "Bearer " + OPENAI_API_KEY);
    oHttp.onreadystatechange = function () {
        $("#btnTransAll").text("Done").prop("disabled", false);
        if (oHttp.readyState === 4) {
            var oJson = {};

            try {
                oJson = JSON.parse(oHttp.responseText);
            } catch (ex) {
                alert("GPT Parse Error: " + ex.message);
                log.console("Resp:" + oHttp.responseText);
            }

            if (oJson.error && oJson.error.message) {
                alert("GPT Error: " + oJson.error.message);
                console.eror("GPT Error: " + oJson.error.message);
            } else if (oJson.choices && oJson.choices[0].message) {
                console.log(oJson);
                var s = oJson.choices[0].message;
                if (s == "") {
                    alert("GPT response empty");
                } else {
                    localStorage.setItem("OPENAI_API_KEY", OPENAI_API_KEY);
                    
                    // Get Answer
                    alert(s.content);
                }
            } else {
                alert("GPT Occurs Exception: " + oHttp.responseText);
                console.error("GPT Occurs Exception: " + oHttp.responseText);
            }
        }
    };

    var data = {
        model: "gpt-3.5-turbo",
        messages: [
            { role: "system", content: "Translator" },
            { role: "user", content: q },
        ],
    };

    oHttp.send(JSON.stringify(data));
}
```
