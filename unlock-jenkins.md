---
description: 啟用 Jenkins 的前置設定。
---

# 啟用 Jenkins

使用 Google Chrome 瀏覽器打開 `localhost:8080` ，首次開啟會出現「Unlock Jenkins」的提示畫面。

在 Cloud9 終端機使用 `cat` 取得 `initialAdminPassword` 的密碼文字：

```text
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![](.gitbook/assets/image%20%2816%29.png)

將這段密碼複製，貼上至「Unlock Jenkins」畫面的「Administrator password」欄位，按下「Continue」按鈕。

![](.gitbook/assets/image%20%2843%29.png)

接著會看到「Customize Jenkins」的畫面，請選擇「Install suggested plugins」：

![](.gitbook/assets/image%20%2854%29.png)

等候 Plugins 安裝完成。

![](.gitbook/assets/image%20%2830%29.png)

設定一組管理者帳號，或是選擇「Continue as admin」跳過此步驟。

![](.gitbook/assets/image%20%2824%29.png)

點選「Start using Jenkins」進入 Jenkins 主畫面。

![](.gitbook/assets/image%20%2893%29.png)

完成所有步驟，接下來需要使用 Jenkins 只要開啟 `localhost:8080` 即可。

![](.gitbook/assets/image%20%2825%29.png)

{% hint style="info" %}
建議從右上角的 admin 下拉選單，點選設定將密碼修改成容易記住的新密碼。
{% endhint %}



