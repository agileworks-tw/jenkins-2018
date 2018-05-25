---
description: 安裝 VirtualBox 虛擬機器，使用 AgileWorks VM 練習系統。
---

# 前置準備

| 虛擬機器版本 | **AgileWorks Jenkins 0525** \(`agileworks-jenkins-0525.ova`\) |
| --- | --- |
| 線上講義 | https://jenkins.agileworks.tw/ |

### 軟硬體需求

使用自備的筆記型電腦，請確認其硬體規格符合開發環境最低需求：

* 建議使用64位元作業系統
* 實體記憶體 RAM 建議 8GB 以上（最低需求為 4GB）
* 硬碟可用空間至少 40GB 以上（建議搭配 SSD 固態硬碟）
* 處理器支援虛擬化技術（VT）

建議的作業系統版本為：

* Windows 7 64位元
* Windows 10 64位元
* Ubuntu Linux 16.04 64位元以上
* Mac OS X 10.12 以上

請先安裝 Google Chrome 瀏覽器（建議使用最新版本）：

下載 Google Chrome 瀏覽器 - [https://www.google.com.tw/chrome/](https://www.google.com.tw/chrome/)

### 安裝 VirtualBox 虛擬機器軟體

為減少安裝建置環境所耗費的時間，我們已先將教學過程所需要的系統與軟體工具，打包為 AgileWorks VM 虛擬機器。

請先下載並安裝最新版本的 VirtualBox 軟體及延伸套件（Extension Pack）：

下載 VirtualBox 軟體 - [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

以 Windows 為例，安裝 VirtualBox 所需要下載的程式包含：

* [VirtualBox-5.2.10-122406-Win.exe](https://download.virtualbox.org/virtualbox/5.2.10/VirtualBox-5.2.10-122406-Win.exe) （主要安裝程式）
* [Oracle\_VM\_VirtualBox\_Extension\_Pack-5.2.10.vbox-extpack](https://download.virtualbox.org/virtualbox/5.2.10/Oracle_VM_VirtualBox_Extension_Pack-5.2.10.vbox-extpack) \(Extension Pack\)

{% hint style="info" %}
請先安裝完成 VirtualBox 主程式後，再開啟 Extension Pack 安裝檔。
{% endhint %}

![](.gitbook/assets/image%20%2837%29.png)

### 取得 AgileWorks VM 映像檔

課程由講師在上課時提供裝載 AgileWorks VM 映像檔的 USB 隨身碟，或是透過區域網路的檔案伺服器下載，請確保使用日期為最新版本的映像檔。

虛擬機器封裝檔名格式為`agileworks-jenkins-版本代號.ova`（OVA 為 Open Virtual Appliance 的縮寫），點擊開啟或使用 VirtualBox 功能選單的「檔案 / 匯入應用裝置」，將 AgileWorks VM 匯入成為新的虛擬機器。

![](.gitbook/assets/image%20%28104%29.png)

{% hint style="info" %}
AgileWorks VM 預設的帳號密碼為 `user` / `12345`，如果需要變更，請使用 `passwd`指令自行修改為自訂的密碼。
{% endhint %}

### 預設服務位址

| 服務類型 | Port | VirtualBox Port | 備註 |
| --- | --- | --- | --- | --- |
| OpenSSH | 22 | 2222 | ssh user@localhost -p 2222 |
| Jenkins | 8080 | 8080 | http://localhost:8080 |
| GitLab | 8081 | 8081 | http://localhost:8081 |
| Cloud9 | 8181 | 8181 | http://localhost:8181 |

{% hint style="info" %}
為使虛擬機器盡可能減少對記憶體的消耗，預設並不會啟用 GitLab 服務，如需使用，請於終端機執行以下指令，等待時間約 30~60 秒。

`sudo systemctl start gitlab-runsvdir.service`
{% endhint %}

### 預設帳號密碼 {#mi}

AgileWorks Jenkins VM 預設為以下帳號及密碼：

| 類別 | 帳號名稱 | 密碼 |
| --- | --- | --- | --- | --- | --- | --- |
| Ubuntu Linux User | user | user12345 |
| GitLab Administrator | root | root12345 |
| GitLab User | user | user12345 |
| GitLab Demo | demo | demo12345 |
| Jenkins Administrator | admin | admin12345 |
| Jenkins User | user | user12345 |

### 開始使用 AgileWorks VM 練習系統

在 VirtualBox 選取「AgileWorks Jenkins」虛擬機器，並點擊「啟動」開始執行。

使用 Google Chrome 瀏覽器打開 `localhost:8181` ，等待 Cloud9 編輯器畫面出現。

![](.gitbook/assets/image%20%2888%29.png)

下方的終端機畫面，可以直接輸入 Shell 指令，請先測試 Java 是否正常安裝。

```text
java -version
javac -version
```

![](.gitbook/assets/image%20%2899%29.png)

### 註冊個人的 GitHub、Docker Hub 帳號

如果你還沒有 GitHub 帳號，請先註冊一組：

註冊 GitHub 帳號 - [https://github.com/join](https://github.com/join)

如果你還沒有 Docker Hub 帳號，請先註冊一組：

註冊 Docker Hub 帳號 - [https://hub.docker.com/](https://hub.docker.com/)

### 常見問題處理：VT-x is not available

如果你在啟動 VirtualBox 虛擬機器，遇到以下的錯誤訊息：

```text
VT-x is not available (VERR_VMX_NO_VMX).
```

代表主機板 BIOS 的 Intel 處理器的虛擬化功能未被啟用。如果電腦採用 AMD 處理器，則會出現以下訊息：

```text
AMD-V is disabled in the BIOS (VERR_SVM_DISABLED).
```

參考畫面如下：

![](.gitbook/assets/image%20%282%29.png)

請先進入主機板 BIOS 開機畫面的設置，找到「Intel Virtualization Technology」選項，並將它調整為啟用（Enabled）：

![](.gitbook/assets/image%20%2847%29.png)

或是 AMD 處理器則為 SVM 選項：

![](.gitbook/assets/image%20%2813%29.png)

由於不同電腦主機板的 BIOS 設置方式有所差異，通常在開機階段按下 F12 或 Del 按鍵，可以進入設定選單模式，請參閱筆電或主機板提供的原廠手冊。

{% hint style="info" %}
Windows 系統可使用 CPU-V 工具檢測虛擬技術是否啟用。

[https://www.azofreeware.com/2015/09/cpu-v.html](https://www.azofreeware.com/2015/09/cpu-v.html)
{% endhint %}

