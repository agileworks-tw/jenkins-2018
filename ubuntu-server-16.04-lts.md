---
description: 以 Ubuntu Server 為例，說明 Jenkins 完整安裝流程。
---

# 伺服器安裝指南

Ubuntu Server 16.04 是架設 Jenkins 伺服器常見的作業系統選項，它是免費的 Open Source 開放源碼作業系統，對於新手也是相當友善的選項，甚至不需要部署實體的伺服器主機，只要租用 AWS EC2 或 Linode VPS 的雲端虛擬機器，就可以開始輕鬆入門。

### 使用 VirtualBox 虛擬機器

如果你希望在自己的筆電建立練習用的環境，可以搭配 VirtualBox 虛擬機器使用。

{% hint style="info" %}
參加工作坊可以取得已經包含 Jenkins 的 AgileWorks VM 映像檔，此章節留給有興趣瞭解安裝流程的學員自行參閱。
{% endhint %}

下載 VirtualBox 安裝程式：[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

下載 Ubuntu Server ISO 映像檔：[https://www.ubuntu-tw.org/modules/tinyd0/](https://www.ubuntu-tw.org/modules/tinyd0/)

![&#x9078;&#x64C7; Linux - Ubuntu \(64-bit\)](.gitbook/assets/image%20%28108%29.png)

![&#x5EFA;&#x8B70;&#x914D;&#x7F6E;&#x81F3;&#x5C11; 2048MB &#x8A18;&#x61B6;&#x9AD4;&#x7D66;&#x865B;&#x64EC;&#x6A5F;&#x5668;](.gitbook/assets/image%20%28113%29.png)

{% hint style="info" %}
如果需要執行 GitLab CE 請至少配置 4096MB 以上的記憶體量。
{% endhint %}

其餘選項依照建議的預設值。

### 安裝 Ubuntu Server 16.04

第一次執行虛擬機器，掛載 Ubuntu Server ISO 映像檔。

![](.gitbook/assets/image%20%2850%29.png)

請先完成 Ubuntu Server 安裝。

{% hint style="info" %}
請記得安裝 OpenSSH 伺服器，以方便使用 SSH / PuTTY 工具遠端操作終端機。
{% endhint %}

![](.gitbook/assets/image%20%2824%29.png)

VirtualBox 的網路預設為 NAT 模式，必須自行設定 Port Forwarding，才能讓 22\(ssh\) 與 8080\(jenkins\) 兩個 TCP Port 可以被存取。

![](.gitbook/assets/image%20%28118%29.png)

#### 進行系統更新

為確保 Ubuntu Server 的套件皆為最新狀態，在系統安裝完成後，建議進行一次完整的更新。

```text
sudo apt-get update
sudo apt-get upgrade
```

### 安裝 OpenJDK

Jenkins 需要使用 OpenJDK 8 的版本才能運行，以下指令先移除系統自帶的 `openjdk-9-jre-headless` 套件，再安裝 `openjdk-8-jdk-headless`套件。

```text
sudo apt-get purge openjdk-*
sudo apt-get -y install openjdk-8-jdk-headless
```

### 安裝 Maven

Apache Maven 是常見的 Java Build Tool。

```text
sudo apt-get install maven
```

### 安裝 Groovy & Gradle

安裝必要的套件。

```text
sudo apt-get install zip unzip
```

安裝 sdkman 工具。

```text
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk version
```

安裝 Groovy 與 Gradle。

```text
sdk install groovy
sdk install gradle
```

### 安裝 Jenkins

使用 `apt-get` 套件管理工具，輕鬆搞定 Jenkins 的安裝：

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -

echo deb https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list

sudo apt-get update

sudo apt-get install jenkins
```

#### 管理 Jenkins 服務

啟動服務

```bash
sudo systemctl start jenkins
```

重新啟動服務

```bash
sudo systemctl restart jenkins
```

停止服務

```bash
sudo systemctl stop jenkins
```

#### 修改預設的 8080 Port

如果需要變更 Jenkins 使用的 HTTP Port 號碼，請編輯 `/etc/default/jenkins`檔案，將 `HTTP_PORT=8080` 改為自訂的 Port 號碼。

#### 安裝其他常用的 Plugins

* TAP - ​https://plugins.jenkins.io/tap

### 安裝 GitLab

安裝相依套件：

```text
sudo apt-get update
sudo apt-get install ca-certificates curl openssh-server postfix
```

安裝 GitLab 套件：

```text
cd /tmp
curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
sudo bash /tmp/script.deb.sh
sudo apt-get install gitlab-ce
```

修改 `/etc/gitlab/gitlab.rb` 設定：

```text
external_url 'http://localhost:8081'
unicorn['port'] = 8180
gitlab_workhorse['auth_backend'] = "http://localhost:8180"
```

重置並啟動 GitLab 服務：

```text
sudo gitlab-ctl reconfigure
```

GitLab 系統管理提示：

```text
sudo systemctl disable gitlab-runsvdir.service  # 關閉開機自動執行
sudo systemctl stop gitlab-runsvdir.service     # 停止
sudo systemctl start gitlab-runsvdir.service    # 啟動
```

使用瀏覽器開啟 `http://localhost:8081`開始使用 GitLab，第一次使用必須設定管理者帳號（root）的密碼。

### 安裝 Node.js

先安裝 NVM 工具，方便管理與升級 Node.js 的安裝版本。

```text
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

重新登入後，安裝 Node.js 8.x 最新版：

```text
nvm install v8
```

檢查是否正確安裝：

```text
node --version
npm --version
```

安裝 PM2 工具：

```text
npm install -g pm2
```

### 安裝 Python

同時安裝 Python 2.7 與 Python 3.x 兩種版本。

```text
sudo apt-get install python python3
```

### 安裝 Cloud9

Cloud9 是一款 Web-based 的整合開發環境，方便於使用瀏覽器在遠端伺服器進行程式碼的編輯與終端機指令的執行。

先安裝必要的系統套件。

```text
sudo apt-get install build-essential
```

安裝 Cloud9。

```text
git clone https://github.com/c9/core.git c9sdk
cd c9sdk
scripts/install-sdk.sh
```

使用 PM2 啟動 Cloud9。

```text
 pm2 start server.js --name "cloud9" -- --debug -l 0.0.0.0 -p 8181 -w /home/user -a :
```

```text
sudo env PATH=$PATH:/home/user/.nvm/versions/node/v8.11.2/bin /home/user/.nvm/versions/node/v8.11.2/lib/node_modules/pm2/bin/pm2 startup systemd -u user --hp /home/user
pm2 dump
```

### 安裝 Docker

安裝 Docker 的指令：

```text
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce
```

### 安裝 .NET Core

```text
wget -q packages-microsoft-prod.deb https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb

sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-sdk-2.1.200
```

### 系統權限相關設置

編輯 `/etc/sudoers`加入 `NOPASSWD:` 設定，讓 `sudo`不需要輸入密碼。

```text
%sudo   ALL=(ALL:ALL) NOPASSWD: ALL
```

{% hint style="warning" %}
請注意 `sudo` 免輸入密碼會為系統帶來資安風險。
{% endhint %}

將 user 與 jenkins 加入至 sudo 與 docker 群組。 

```text
sudo usermod -aG docker ${USER}
sudo usermod -aG docker jenkins
sudo usermod -aG sudo ${USER}
sudo usermod -aG sudo jenkins
```

### 預設服務位址

| 服務類型 | Port | VirtualBox Port | 備註 |
| --- | --- | --- | --- | --- |
| OpenSSH | 22 | 2222 | ssh user@localhost -p 2222 |
| Jenkins | 8080 | 8080 | http://localhost:8080 |
| GitLab | 8081 | 8081 | http://localhost:8081 |
| Cloud9 | 8181 | 8181 | http://localhost:8181 |

### 預設帳號密碼

AgileWorks Jenkins VM 預設為以下帳號及密碼：

| 類別 | 帳號名稱 | 密碼 |
| --- | --- | --- | --- | --- | --- | --- |
| Ubuntu Linux User | user | user12345 |
| GitLab Administrator | root | root12345 |
| GitLab User | user | user12345 |
| GitLab Demo | demo | demo12345 |
| Jenkins Administrator | admin | admin12345 |
| Jenkins User | user | user12345 |

### 延伸閱讀

* [Installing Jenkins on Ubuntu](https://wiki.jenkins.io/display/JENKINS/Installing+Jenkins+on+Ubuntu) \(Jenkins Official Wiki\)
* [How To Install Jenkins on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-16-04)
* [How To Install and Configure GitLab on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-gitlab-on-ubuntu-16-04)
* [How To Install and Use Docker on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04)



