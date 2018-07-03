---
description: 最受歡迎的持續整合與持續部署自動化工具。
---

# 認識 Jenkins

Jenkins 是許多團隊採用的專案自動化服務，透過撰寫 Pipeline 或其他類型的 Script，可以建立持續整合 CI（Continuous Integration）與持續發布 CD（Continuous Delivery）流程。豐富的擴充套件（Plugins）與社群支持為 Jenkins 持續帶來更多功能。最新版本支援 Pipeline as Code 以及分散式建置環境。

Jenkins 的特點：

* 跨平台與語言： 豐富的 Plugin 支援，使得 Jenkins 支援大多數程式語言和開發框架。對於大多數需要自動化的流程來說，都可以搭配  Shell 指令進行。
* 大量的擴充套件： Jenkins 的社群龐大，提供安裝的 Plugin 數量超過 1000 種。如果還是無法滿足你的需求，也可以自行開發專用的 Plugin。 
* 跨平台： 因為 Jenkins 是使用 Java 開發，其封裝檔為 Java EE 的 WAR（Web Application Archive）格式，也提供 Docker 映像檔、相容於 Windows、Linux 與 Mac OS X 作業系統，在常見的 Linux 伺服器系統上，也可以透過 APT 或 YUM 方式進行安裝。 
* 支援各種版本控制系統： 絕大多數的版本控制系統與建置工具，都可以搭配 Jenkins 使用，之所以能夠廣泛支援，也是因為 Jenkins 有龐大的社群與擴充套件。
* 分散式運算： Jenkins 內建 Master / Slave 的機制，因此能夠同時部署多個節點在多台機器，也可以在各節點運行不同的作業系統和建置環境。
* 簡單易用： 安裝和設定流程簡單，也不需要資料庫。
* 程式碼導向： 透過 Pipeline 程式碼定義 Jenkins 任務，以及 Jenkins 本身的設定檔，也可以使用 XML 或 Groovy 撰寫，因此 Jenkins 的設定可以搭配版本控制系統，並做到全面的自動化。 

安裝 Jenkins 的最低軟硬體需求：

* Java 8
* 256MB free memory
* 1 GB+ free disk space

