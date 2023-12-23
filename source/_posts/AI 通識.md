---
title: AI 通識
categories: Others
tags:
  - AI
abbrlink: 3327344738
date: 2023-12-23 04:59:33
---

## What is AI

* 工人智慧 vs. 人工智慧

![image](https://hackmd.io/_uploads/rkrlxiLLT.png)
  > [WebConf 2023 共筆](https://hackmd.io/@webconf/BkImQ0Ds3/%2FTz4XDh74SqGDDZiGcWBaKg)
  
<!-- more -->

## Generative AI

* 隨機性：連 AI 開發者都很難追溯其結果是怎麼產生的

### LLM

* Google - [Bard](https://bard.google.com/chat)
* Microsoft - [Copilot](https://copilot.microsoft.com/)
  * 具上網搜尋能力
  * 可生成圖像
  * 2023/12/05 升級底層技術至新版DALL-E 3與GPT-4 Turbo
    ![image](https://hackmd.io/_uploads/S1HBys88T.png)
* xAI - [Grok](https://grok.x.ai/)
* Anthropic - [Claude 2](https://claude.ai/chats)
  * 前 ChatGPT 開發團隊跳出來做的產品
  * 支援上傳附件 (for 免費仔)
* [Open AI](https://openai.com/) - [ChatGPT](https://chat.openai.com/)
    |模型|用途| 使用情境|
    |---|---|---|
    |GPT 系列| 一系列的模型，可以理解和生成自然語言或代碼| 自然語言處理、對話機器人、文本生成|
    |DALL·E| 一個可以根據自然語言提示生成和編輯圖像的模型| 圖像生成、圖像編輯、視覺藝術|
    |Whisper| 一個可以將語音轉換為文本的模型| 語音識別、語音轉文字|
    |Embeddings| 一系列的模型，可以將文本轉換為數值形式| 自然語言處理、文本分析、情感分析|
    |Codex| 一系列的模型，可以理解和生成代碼，包括將自然語言轉換為代碼| 代碼生成、代碼編輯、自然語言處理|
    |Moderation| 一個經過微調的模型，可以檢測文本是否可能敏感或不安全| 在線社交平台、內容審查|
  * 2021 年 9 月前的訓練資料
  * GPT 4/GPT Plus 須付費解鎖
  * [Token](https://platform.openai.com/tokenizer)：
    * GPT-3.5 Turbo：4097(4k) tokens
    * GPT-4：32,768 (32K)
    * GPT-4 Turbo：128K (約可容納或考慮逾 300 頁的文字)
      > For example, the string "ChatGPT is great!" is encoded into six tokens: ["Chat", "G", "PT", " is", " great", "!"]
  * Role：`user` `assistant` `system`

    ``` json
    {
      "messages": [
        {
          "role": "user",
          "content": "tell me a joke"
        },
        {
          "role": "assistant",
          "content": "why did the chicken cross the road"
        },
        {
          "role": "user",
          "content": "I don't know, why did the chicken cross the road"
        }
      ]
    }
    ```

    ``` json
    {
      "messages": [
        {
          "role": "system",
          "content": "You are an assistant that speaks like Shakespeare."
        },
        {
          "role": "user",
          "content": "tell me a joke"
        }
      ]
    }
    ```

    ``` json
    {
      "message": {
        "role": "assistant",
        "content": "Why did the chicken cross the road? To get to the other side, but verily, the other side was full of peril and danger, so it quickly returned from whence it came, forsooth!"
      }
    }
    ```

  * 自訂指令
  * [GPTs](https://openai.com/blog/introducing-gpts)

#### for Developer

* [Devv](https://devv.ai/zh)：著重程式面
* [SqlChat](https://www.sqlchat.ai/)：著重 SQL
* [GitFluence](https://www.gitfluence.com/)：著重 Git 指令
* [Will 保哥的 AI 協作工具清單](https://blog.miniasp.com/post/2023/10/18/Will-AI-Tools-List)

### Visual/Image

* Open AI - GPT4
    ![image](https://hackmd.io/_uploads/SymdljU8T.png)
* Vercel - [v0](https://v0.dev/)
* Google - [Gemini](https://deepmind.google/technologies/gemini/#introduction)
* [Midjourney](https://www.midjourney.com/home?callbackUrl=%2Fexplore)

---
