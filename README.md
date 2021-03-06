---
description: 使用 Git、Jenkins 與 Docker 工具，實現自動化測試與持續整合。
---

# 版本控制、自動化測試與持續整合實務

在漫威電影《鋼鐵人》中，有著人工智慧管家 Jarvis 的協助，讓鋼鐵人可以更專心對付帶給地球威脅的強大敵人。現實生活中，Facebook 執行長 Mark Zuckerberg 也[打造 Jarvis 同名的 AI 管家](https://www.facebook.com/notes/mark-zuckerberg/building-jarvis/10103347273888091/)，方便操控家中的電器與保全。

對於敏捷軟體開發團隊來說，透過 24 小時全年無休的 Jenkins 管家服務，可以幫忙打理開發流程的大小事，包括自動化建置、測試與產生報表等，讓團隊成員專注在更有價值的任務。

Jenkins 是使用 Java 開發的軟體，其前身為 [Hudson](https://en.wikipedia.org/wiki/Hudson_%28software%29) 專案。它支援多種 SCM 工具，例如 SVN、Git 等，可以透過 Apache Ant 或 Maven 進行專案建置，執行 Shell Script 與 Windows Batch 定義的各種任務，以及透過排程或事件方式觸發執行任務，封存建置產生的檔案，以及提供建置歷史記錄與報表。

最新版本 Jenkins 2.0 帶來 Pipeline 與 Blue Ocean  使用者介面，使得 Jenkins 在持續整合與持續發布的應用上，有更好的使用體驗與實用性。

開發流程導入 CI（持續整合）與 CD（持續發布）有什麼好處？我們將從精實生產（Lean Production）開始介紹，並一步步引導使用 Jenkins 進行建置、測試與發布的自動化流程，瞭解持續整合如何在每一個專案開發流程的環節協助團隊運作。

AgileWorks Jenkins 工作坊的內容包括：

* Git/GitLab 版本控制系統
* TDD 測試驅動開發
* Jenkins 持續整合與發佈
* Selenium WebDriver 網站自動化測試
* Jira Issue 問題追蹤管理
* Docker 容器與虛擬化技術

{% hint style="info" %}
依實際培訓時數與需求調整內容。
{% endhint %}

## 教學團隊簡介

創科資訊成立於 2015 年，提供敏捷軟體開發技術顧問與培訓服務，協助企業開發團隊應用新一代技術與開發工具，前端開發技術例如 React、React Native、Vue 及 Webpack⋯，後端開發平台 Node.js、PHP Laravel、.NET Core 與 SpringBoot⋯，雲端運算服務如 AWS、GCP 與 Azure，DevOps 工具例如 GitLab、Jenkins 與 Docker 等。 

為加速軟體人才培育，於 2017 年底新成立 [Monospace 共同工作空間](https://www.facebook.com/monospace.tw/)，是台灣中部第一間以 IT 為主題的 Coworking Space，專注於 UI / UX 設計與前端開發主題，吸引數位游牧工作者與斜槓青年進駐，經常舉辦主題相關的講座與工作坊，形成資訊科技跨域人才交流學習的聚落。

代表性客戶：

* iThome
* PCHome
* 資策會
* 工研院
* 中華電信學院
* 台灣大哥大
* 光寶科技
* 啟德電子

謝宗穎 / Spooky

* 創科資訊 Co-founder
* DevOps 技術顧問

林彥宏 / Kyle

* 創科資訊 Co-founder
* 光寶科技 Jenkins 技術顧問

