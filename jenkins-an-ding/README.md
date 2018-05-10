---
description: 以 Ubuntu Linux Server 18.04 為例。
---

# Jenkins 安裝與設定

## 執行 Jenkins 需要的系統環境

由於 Jenkins 是以 Java 開發的軟體，可以支援跨平台，如 Linux、Windows 與 Mac OS X 等作業系統。但是在實際正式部署 Jenkins Master 服務時，建議採用伺服器專用的作業系統，例如：

* Ubuntu Linux Server 16.04 LTS / 18.04 LTS
* CentOS 7
* Windows Server 2012 / 2016

Jenkins 提供 Master-Slave 的架構，因此無須擔心 Jenkins Master 安裝在 Linux 系統，會造成 Windows 開發環境的專案無法建置，因為可以同時部署多個 Jenkins Slave 節點於不同的作業系統，包括不同的作業系統版本與開發、測試環境版本的各種組合。

```text
Jenkins Master -------- CentOS 7 (Master)
                  |
                  |---- Ubuntu 16.04 (Slave)
                  |---- Ubuntu 18.04 (Slave)
                  |
                  |---- Windows 7 (Slave)
                  |---- Windows 10 (Slave)
                  |---- Windows Server 2016 (Slave)
                  |
                  |---- Mac OS X 10.12 (Slave)
```

### 硬體配置的建議

對於正式的 Jenkins Production 部署環境，採用 Master-Slave 架構是比較可靠的方案。

Jenkins Master

* 配備容量充足的記憶體與磁碟
* 提高可靠度的磁碟陣列與備份機制
* 連線穩定順暢的有線區域網路
* Dual-port 網路使用 Subnet 連線至多台 Jenkins Slave

Jenkins Slave

* 處理器愈快愈好
* 高速的固態硬碟
* 記憶體足夠軟體的建置與測試執行
* 長時間執行易對硬體損耗，選擇容易更換維修的硬體規格

{% hint style="info" %}
可搭配虛擬化伺服器如 VMWare ESXi 或 Docker 容器，彈性部署與方便管理 Jenkins Slave 節點。
{% endhint %}

### “不建議”的配置方式

效能低落的老舊電腦。

> 執行建置與測試需要過長的時間，降低開發人員使用 Jenkins CI 的意願。

僅使用一部配備 SSD 固態硬碟的主機，同時擔任 Master + Slave 兩種角色。

> 固態硬碟容量相對小，容易在一段時間後，建置產生的 artifacts 檔案將磁碟空間用盡。此外，頻繁讀寫磁碟造成固態硬碟故障，也可能導致 Jenkins Master 被迫暫停服務。

### 關於 Jenkins 伺服器的資安

Jenkins 伺服器的資訊安全議題，在 2018 年傳出全球的[駭客攻擊事件](https://www.ithome.com.tw/news/121361)後，引起更多的關注。

> 駭客利用Jenkins上的CVE-2017-1000353安全漏洞進行JenkinsMiner挖礦攻擊，這是一個反序列化漏洞，影響Altoros Jenkins for PCF 10.2以前的版本，這幾個月以來駭客已開始利用遠端存取木馬（RAT）及XMRig挖礦程式開採該漏洞，波及全球Jenkins用戶。

如果 Jenkins 伺服器存在資安漏洞，會帶來哪些風險呢？

* 被植入木馬、殭屍程式，變成被駭客輕易操控的電腦
* 重要的程式碼、設定檔被竊取

如何提高 Jenkins 伺服器的安全性？

* 啟用 Jenkins 的安全設置
* 僅開放必要的 Port 如 80, 8080
* 啟用 SSL 加密的 HTTPS 服務
* 限制存取來源
* 透過權限控管的 VPN 方式存取
* Jenkins Slave 節點使用受隔離的區域網路

