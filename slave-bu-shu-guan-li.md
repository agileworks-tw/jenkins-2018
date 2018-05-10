# Slave 節點部署與管理

建議的 Slave Agent 部署選項：

1. Launch slave agents on Unix machines via SSH 
2. Launch slave agents via Java Web Start.

建議使用第二種 JNLP \(Java Web Start\) 方式部署，因為相容於所有支援 Java 執行環境的作業系統，包括 Windows、Linux 與 Mac OS X 等。

### 建立新的 Slave Agent 節點

從「管理 Jenkins / 管理節點」進到節點列表的畫面。

![](.gitbook/assets/image%20%2822%29.png)

點選「新增節點」建立 `docker-slave-001 `新節點。

![](.gitbook/assets/image%20%285%29.png)

填寫必要的設定資訊。

* 遠端檔案系統根目錄：`/var/jenkins`
* 標籤：`docker linux`
* 啟動模式：`Launch agent via Java Web Start`

![](.gitbook/assets/image%20%286%29.png)

按下「儲存」完成建立新節點的程序。

![](.gitbook/assets/image%20%2816%29.png)

### 測試新建的 Slave Agent 節點

點選節點的名稱，進到節點的主畫面。

![](.gitbook/assets/image%20%2832%29.png)

從這裡取得 Slave 所需要的啟動指令：

```text
java -jar agent.jar -jnlpUrl http://localhost:8080/computer/docker-slave-001/slave-agent.jnlp -secret ae3b276e565e6499dbac2772114f7b581d8005b302829c1bea01ddbf102c07d5 -workDir "/var/jenkins"
```

請下載 `agent.jar` 到 Slave 電腦，並使用終端機執行：

```text
sudo mkdir /var/jenkins
sudo chown user:user /var/jenkins
wget http://localhost:8080/jnlpJars/agent.jar
java -jar agent.jar -jnlpUrl http://localhost:8080/computer/docker-slave-001/slave-agent.jnlp -secret ae3b276e565e6499dbac2772114f7b581d8005b302829c1bea01ddbf102c07d5 -workDir "/var/jenkins"
```

順利執行後，可以看到 Slave 狀態為已上線（閒置）。

![](.gitbook/assets/image%20%2849%29.png)

請嘗試在新節點執行 `helloworld`的測試，並觀察 `/var/jenkins`路徑的檔案內容。

![](.gitbook/assets/image%20%284%29.png)

完成測試後，請按「Ctrl + C」結束在終端機執行的 JNLP Agent 程式，此時 `docker-slave-001`回到離線狀態。

### 使用 Docker 執行 Slave Agent 程式

使用 `docker run` 執行 `jenkins/jnlp-slave` 進行測試：

```text
docker run jenkins/jnlp-slave -url http://172.17.0.1:8080 ae3b276e565e6499dbac2772114f7b581d8005b302829c1bea01ddbf102c07d5 docker-slave-001
```

使用 `docker create`建立常駐的背景服務。

```text
docker create --name docker-slave-001 jenkins/jnlp-slave -url http://172.17.0.1:8080 ae3b276e565e6499dbac2772114f7b581d8005b302829c1bea01ddbf102c07d5 docker-slave-001
```

### 使用 Python 實現 Jenkins JNLP Slave 部署自動化

{% code-tabs %}
{% code-tabs-item title="slave.py" %}
```python
import os, sys, urllib, base64, requests

nodeName = sys.argv[1]
username = 'dashboard'
password = '756bbb8390e2406533486be04bc61504'
jenkinsUrl = 'http://192.168.168.58:8080'
remoteFS = '/home/liteon/Jenkins'


if not os.path.isfile('slave.jar'):
  urllib.urlretrieve(jenkinsUrl + '/jnlpJars/slave.jar', 'slave.jar')

r = requests.get(jenkinsUrl + '/crumbIssuer/api/json', auth=(username, password))
headers = {'Jenkins-Crumb': r.json()['crumb']}
data = {'script': base64.b64decode('aW1wb3J0IGh1ZHNvbi5tb2RlbC5Ob2RlLk1vZGUNCmltcG9ydCBodWRzb24uc2xhdmVzLioNCg0Kbm9kZU5hbWU9JyVzJw0KcmVtb3RlRlM9JyVzJw0KDQppZiAoSmVua2lucy5pbnN0YW5jZS5zbGF2ZXMuY291bnR7aXQubm9kZU5hbWU9PW5vZGVOYW1lfT09MCkgew0KICBKZW5raW5zLmluc3RhbmNlLmFkZE5vZGUobmV3IER1bWJTbGF2ZShub2RlTmFtZSwgcmVtb3RlRlMsIG5ldyBKTkxQTGF1bmNoZXIoKSkpDQogIEplbmtpbnMuaW5zdGFuY2Uuc2F2ZSgpDQp9DQoNClN0cmluZyByZXN1bHQNCg0KSmVua2lucy5pbnN0YW5jZS5zbGF2ZXMuZmluZEFsbHtpdC5ub2RlTmFtZT09bm9kZU5hbWV9LmVhY2ggeyBzbGF2ZSAtPg0KICByZXN1bHQgPSAiamF2YSAtamFyIHNsYXZlLmphciAtam5scFVybCAlcy9jb21wdXRlci8kc2xhdmUubm9kZU5hbWUvc2xhdmUtYWdlbnQuam5scCAtc2VjcmV0ICRzbGF2ZS5jb21wdXRlci5qbmxwTWFjIg0KfQ0KDQpwcmludGxuIHJlc3VsdA0K') % (nodeName, remoteFS, jenkinsUrl)}
r = requests.post(jenkinsUrl + '/scriptText', auth=(username, password), data=data, headers=headers)
os.system(r.text)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

執行方式：

```python
python slave.py NODE-NAME-000
```

### 實作練習

1. 如果你使用 Windows 或 Mac OS X 筆電，請嘗試也讓它變成 Slave 節點。
2. 請嘗試新增 2 個使用 Docker 執行的 Slave 節點。

