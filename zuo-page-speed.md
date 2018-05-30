---
description: 參數化建置與產生測試報告。
---

# 實作：Page Speed

在網站更新發布後，我們需要進一步瞭解網站前端的效能，是否能達到基本要求。Google Page Speed 與 Yahoo YSlow 是常見的網站效能測試工具，在這個單元我們以 Jenkins Job 搭配 Page Speed 工具，進行自動化網站效能檢測，並且產生易讀的測試報告。

使用 Google 提供的 [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/?hl=zh-TW) 測試網站效能，可以獲得一個評分：

![](.gitbook/assets/image%20%28104%29.png)

### 安裝 psi 工具

開始定義 Jenkins Job 之前，我們需要思考如何利用 Command-line 方式，執行 PageSpeed 測試。很幸運地，已經有 Open Source 的工具：

PageSpeed Insights with reporting - [https://github.com/addyosmani/psi](https://github.com/addyosmani/psi)

首先，在 Cloud9 的終端機，實際安裝操作一次 `psi`指令。

使用 `npm`安裝 `psi`工具：

```text
npm install -g psi
```

接下來，使用 `psi` 對一個網站進行測試：

```text
psi todomvc.com --strategy=mobile
```

![](.gitbook/assets/image%20%2870%29.png)

從這個測試結果觀察，分數為達到及格門檻 70 分。

```text
Threshold of 70 not met with score of 67
```

### 建立 Jenkins Job

建議新的 Free-Style Job 並名為「pagespeed」：

![](.gitbook/assets/image%20%281%29.png)

在建置流程加入執行 Shell 的 Script 設定：

![](.gitbook/assets/image%20%2846%29.png)

儲存後，立即進行一次「馬上建置」。

### 錯誤 psi: not found 的處理

第一次建置的結果，你會看到代表建置錯誤的「紅燈」，先別緊張，點進去「Console Output」觀察遇到什麼問題。 

![](.gitbook/assets/image%20%2867%29.png)

從訊息中可以得知 `psi: not found` 是造成錯誤的主要原因。

為什麼前面在 Cloud9 可以執行，但是 Jenkins Job 相同指令卻無法執行呢？

### 瞭解 Jenkins 的執行環境

Jenkins 執行這段 Shell Script 時，會使用以下的工作目錄（Working Directory）：

```text
/var/lib/jenkins/workspace/pagespeed
```

並且是以 `jenkins`的系統使用者身份。

檢視 Linux 系統中的 `jenkins` 使用者：

```bash
cat /etc/passwd | grep jenkins
```

切換成 `jenkins`使用者與 `pagespeed`的工作目錄：

```bash
sudo su - jenkins
cd ~/workspace/pagespeed
```

此時 Shell 的狀態，才是 Jenkins Job 實際執行的環境，並沒有 `npm`與 `psi` 等指令。

### 安裝 Jenkins 執行環境需要的軟體

定義一個名為 `install-nodejs` 的 Free-Style Job。

![](.gitbook/assets/image%20%2834%29.png)

使用 Shell Script 幫 Jenkins 使用者安裝 Node.js 執行環境。

```bash
touch ~/.profile

wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

nvm install v6
```

觀察 Console Output 的輸出，確認是否成功安裝。

重新啟動 Jenkins。

```bash
sudo systemctl restart jenkins
```

### 重返 pagespeed 任務

因為 `jenkins` 已經有 Node.js 執行環境，包括 `npm`工具，我們可以重新改寫 `pagespeed` 的內容，加上自動安裝 `psi`工具的 Shell 指令。

```bash
if ! hash psi 2>/dev/null
then
    echo "'psi' was not found in PATH"
    npm install -g psi
fi

psi todomvc.com --strategy=mobile
```

以上 Shell 程式碼加入 if then 的判斷，假如 `psi`尚未安裝，就會執行 `npm install -g psi`先完成安裝。

### 參數化建置

勾選參數化建置，設置一個字串參數。

* 名稱：TARGET\_URL
* 預設值：_\(自訂一組預設網址\)_

![](.gitbook/assets/image%20%2893%29.png)

修改 Shell 程式碼，使用 `$TARGET_URL`參數取代網址。

```bash
if ! hash psi 2>/dev/null
then
    echo "'psi' was not found in PATH"
    npm install -g psi
fi

psi $TARGET_URL --strategy=mobile
```

使用「Build with Parameters」執行任務，此時可以輸入自訂的網址：

![](.gitbook/assets/image%20%2883%29.png)

選擇一個可以通過測試的網址，執行結果將會出現代表建置成功的藍色燈號。

![](.gitbook/assets/image%20%2813%29.png)

### 建立測試報告

使用 `psi --help` 觀察指令使用說明，會發現它的輸出格式包括 `tap`格式。

```text
$ psi --help

  PageSpeed Insights with reporting

  Usage
    $ psi <url>

  Options
    --key        Google API Key. By default the free tier is used
    --strategy   Strategy to use when analyzing the page: mobile|desktop
    --format     Output format: cli|json|tap
    --locale     Locale results should be generated in
    --threshold  Threshold score to pass the PageSpeed test
    --optimized  Get the URL of optimized resources
    --download   Download optimized resources
```

什麼是 TAP 呢？

它是「Test Anything Protocol」的縮寫，是一種通用的純文字格式，用來記錄測試程式執行的一個或多個測試結果。範例如下：

```text
1..4
ok 1 - Input file opened
not ok 2 - First line of the input valid
ok 3 - Read the rest of the file
not ok 4 - Summarized correctly # TODO Not written yet
```

因此我們將 `psi`執行的參數加上 `--format tap`指定以 TAP 格式輸出。

```bash
psi $TARGET_URL --strategy=mobile --format tap | tee pagespeed.tap
```

程式輸出將會顯示在 Console Output，同時也保存到 `pagespeed.tap` 檔案。

### 使用 TAP 外掛套件顯示測試報告

為了讓 TAP 格式的測試報告，可以在 Jenkins 建置結果顯示，請先在「外掛程式管理」安裝 TAP 套件。

![](.gitbook/assets/image%20%2858%29.png)

外掛安裝完成後，重新啟動 Jenkins 伺服器。

回到 `pagespeed`任務設置，在「建置後動作」新增：

1. 封存成品 要封存的檔案：`pagespeed.tap`
2. Publish TAP Results Test results:`pagespeed.tap`

執行幾次`pagespeed`輸入不同網址，分別得到通過與不通過的結果，可以在 TAP Test Results 看到測試報告。

![](.gitbook/assets/image%20%2820%29.png)



