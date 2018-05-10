# 備份與還原

對於每日頻繁執行任務的 Jenkins 伺服器而言，對於檔案系統進行大量讀寫操作，磁碟毀損帶來的資料遺失風險需要被考慮，若丟失以下的檔案將帶來諸多不便：

* Jenkins 系統設置
* Job 的定義資料
* Build History 記錄與封存的 Artifacts 資料檔案

因此每日或甚至每小時的備份，是 Jenkins 伺服器維運重要的工作。

Jenkins 並未使用資料庫，而是採用檔案系統保存所有的設定與建置記錄檔案，安裝在 Linux 作業系統的 Jenkins 預設將檔案保存在 `/var/lib/jenkins` 路徑。

### 備份方式的選擇

1. 使用 Linux 的 Cronjob 或 Windows 的工作排程器，定時備份 Jenkins 資料夾 
2. 掛載獨立的磁碟，並使用自動化的磁碟快照備份（例如搭配 AWS Lambda 進行 EBS Snapshot）
3. 搭配 Jenkins [thinBackup](https://wiki.jenkins.io/display/JENKINS/thinBackup) 外掛套件進行備份

{% hint style="info" %}
正式維運的 Jenkins 伺服器，可以搭配 AWS 的 S3/Glacier 服務，進行異地備份增強資料安全性。  
{% endhint %}



