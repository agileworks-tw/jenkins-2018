---
description: 以 Ubuntu Server 為例，說明 Jenkins 完整安裝流程。
---

# Ubuntu Server 16.04 LTS

Ubuntu Server 16.04 是架設 Jenkins 伺服器常見的作業系統選項，它是免費的 Open Source 開放源碼作業系統，對於新手也是相當友善的選項，甚至不需要部署實體的伺服器主機，只要租用 AWS EC2 或 Linode VPS 的雲端虛擬機器，就可以開始輕鬆入門。

### 使用 VirtualBox 虛擬機器

如果你希望在自己的筆電建立練習用的環境，可以搭配 VirtualBox 虛擬機器使用。

{% hint style="info" %}
參加工作坊可以取得已經包含 Jenkins 的 AgileWorks VM 映像檔，此章節留給有興趣瞭解安裝流程的學員自行參閱。
{% endhint %}

下載 VirtualBox 安裝程式：[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

下載 Ubuntu Server ISO 映像檔：[https://www.ubuntu-tw.org/modules/tinyd0/](https://www.ubuntu-tw.org/modules/tinyd0/)

![&#x9078;&#x64C7; Linux - Ubuntu \(64-bit\)](.gitbook/assets/image%20%2847%29.png)

![&#x5EFA;&#x8B70;&#x914D;&#x7F6E;&#x81F3;&#x5C11; 2048MB &#x8A18;&#x61B6;&#x9AD4;&#x7D66;&#x865B;&#x64EC;&#x6A5F;&#x5668;](.gitbook/assets/image%20%2851%29.png)

其餘選項依照建議的預設值。

第一次執行虛擬機器，掛載 Ubuntu Server ISO 映像檔。

![](.gitbook/assets/image%20%2827%29.png)

請先完成 Ubuntu Server 安裝。

{% hint style="info" %}
請記得安裝 OpenSSH 伺服器，以方便使用 SSH / PuTTY 工具遠端操作終端機。
{% endhint %}

![](.gitbook/assets/image%20%289%29.png)

VirtualBox 的網路預設為 NAT 模式，必須自行設定 Port Forwarding，才能讓 22\(ssh\) 與 8080\(jenkins\) 兩個 TCP Port 可以被存取。

![](.gitbook/assets/image%20%2855%29.png)

### 安裝 Jenkins

使用 `apt-get` 套件管理工具，輕鬆搞定 Jenkins 的安裝：

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -

echo deb https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list

sudo apt-get update

sudo apt-get install jenkins
```

### 管理 Jenkins 服務

啟動

```bash
sudo systemctl start jenkins
```

重新啟動

```bash
sudo systemctl restart jenkins
```

停止

```bash
sudo systemctl stop jenkins
```

### 修改預設的 8080 Port

編輯 `/etc/default/jenkins`檔案，將 `HTTP_PORT=8080` 改為自訂的 Port 號碼。

### 延伸閱讀

* [DigitalOcean 提供的安裝教學](https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-16-04)
* [Jenkins 官方提供的安裝教學](https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Ubuntu)

