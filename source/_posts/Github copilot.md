---
title: Github copilot
categories: Others
tags:
  - Github Copilot
  - AI
abbrlink: 582090662
date: 2023-12-03 12:30:23
---

## 簡介

- 基於 AI / LLM 技術的開發助手，用於改善開發者體驗(DX)
- 核心技術來自於 OpenAI 的 GPT-3.5 Turbo 模型
  - 該模型基於海量的自然語言文本資料集進行訓練，並具備一定的推理能力
- 明年初有機會到 GPT 4 (32K)
- 幾乎支援「任何」程式語言
- 官方宣稱不會用客戶資料作為訓練模型
<!-- more -->

### 用途

- 重複/單調的開發
- 回想語法
- 保持心流
- 確保程式一致性
- 精準的判斷上下文，並自動完成程式碼
- 有很複雜的快取機制，避免瘋狂 DDOS
- 沒有足夠的上下文，也可以透過「註解」提供更完整的上下文資訊
- 可透過妥善命名的類別、方法、參數即可自動產生適合的程式碼
- 擅長撰寫重複的程式碼，可幫助開發人員節省時間和精力，提高開發效率
- 可自動建立與撰寫單元測試程式碼
- 可促進程式碼的標準化和可讀性
- 可以快速生成程式碼，節省開發人員的時間和精力
- 可以在不熟悉的領域提高寫 Code 的自信度
- 強調寫 Code 的「心流」模式不被破壞

### 上下文 (Context) 包含哪些

GitHub Copilot 會依據「上下文」自動組成 GPT-3.5 Turbo 所需的提示

- 目前正在編輯的程式
- 目前鍵盤游標的前/後幾行
- 還包含了所有已經開啟的程式碼檔案
- 他會分析你的程式碼結構並取出重要的資訊
- 註解、檔案名稱、類別名稱、函式名稱、參數名稱...
- 刪掉的字

#### CDD (Comment Driven Development)

註解導向的開發模式

- 違反人性，通常都是直接寫 Code
- 太多註解很干擾
- 錯誤的註解比 bug 還可怕

## 支援 IDE/設定

![image](https://hackmd.io/_uploads/Skl4AsLUa.png)

## 支援的程式語言

- 包含但不限於 Java、PHP、Python、JavaScript、Ruby、Go、C#、C++...
  - 目前 Github 上最多的語言是 js > python
- 只要是 GitHub 公開儲存庫找的到的 Code 它都看的懂!
- 越少公開的程式語言範例，產生的程式碼就越爛!

## 版權

可能存在版權和隱私問題

- 若有比對到和 Github 上一致的 Code 的話，會有 Code Reference 提示
- 發生機率 1%

## 費用

- 個人版：

  ![image](https://hackmd.io/_uploads/Hk9MMT8UT.png)

- 商業/企業版：月繳 $19/$39 美元/使用者  
  (主要使用在組織帳戶中)

  ![image](https://hackmd.io/_uploads/HydJfaLLp.png)

## 基本使用技巧

## Github Copilot Chat

- 一個以「對話」為基礎的工具
- 對於對話內容有一定程度的短期記憶能力
- 提問會跟當前游標所在程式碼自動關聯  
- 提供各種強大的程式碼生成功能

## 指令  

### @workspace 關於您當前工作區中的檔案或專案提出問題

`/fix`: Bug 修正
`/explain`: 解釋 Code
`/doc`: 產生文件
`/tests`: 產生單元測試
`/clear`: clear session
`/terminal`: 用 cli 做事
`/new`: 建立系統架構
`/newNotebook`：建立新的 Jupyter 筆記本  

### 快捷鍵

VsCode

- inline-chat: `ctrl + i`
- new chat: `ctrl + shift + i`

### @vscode 提問關於 VS Code 的問題  

- /api 請教有關 VS Code 擴充套件開發的問題

## Github Next

> <https://githubnext.com/>
