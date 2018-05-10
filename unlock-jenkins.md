---
description: 啟用 Jenkins 的前置設定。
---

# 啟用 Jenkins

使用 Google Chrome 瀏覽器打開 `localhost:9088` ，首次開啟會出現「Unlock Jenkins」的提示畫面。

在 Cloud9 終端機使用 `cat` 取得 `initialAdminPassword` 的密碼文字：

```text
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![](.gitbook/assets/image%20%286%29.png)

將這段密碼複製，貼上至「Unlock Jenkins」畫面的「Administrator password」欄位，按下「Continue」按鈕。

![](.gitbook/assets/image%20%2821%29.png)

接著會看到「Customize Jenkins」的畫面，請選擇「Install suggested plugins」：

![](.gitbook/assets/image%20%2828%29.png)

等候 Plugins 安裝完成。

![](.gitbook/assets/image%20%2811%29.png)

設定一組管理者帳號，或是選擇「Continue as admin」跳過此步驟。

![](.gitbook/assets/image%20%287%29.png)

點選「Start using Jenkins」進入 Jenkins 主畫面。

![](.gitbook/assets/image%20%2841%29.png)

完成所有步驟，接下來需要使用 Jenkins 只要開啟 `localhost:9088` 即可。

![](.gitbook/assets/image%20%288%29.png)



