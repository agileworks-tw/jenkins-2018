# 實作：Docker 101

映像檔（Image）是 Docker 的重要基礎概念，也是執行容器（Container）所必備。

* 映像檔類似 Ubuntu Live CD / USB 的 ISO。

映像檔與容器之間的關係？

* 如果把映像檔視為物件導向的類別（class），容器則可視為物件（object）。
* 啟動容器需要本機已存在所需映像檔。

映像檔從哪裡來？

* 來自 Docker 的倉庫（Repository）。
* 如果本機不存在所需映像檔，Docker 會嘗試從預設的倉庫（例如：Docker Hub）下載映像檔。

本單元將練習以下的 Docker 指令：

1. 搜尋映像檔

   `search`

2. 取得映像檔

   `pull`

3. 列出所有映像檔

   `images`

4. 檢視映像檔資訊

   `inspect`

5. 備份與載入

   `save`, `load`

6. 刪除映像檔

   `rmi`

### 指令求助

查詢 Docker 的指令清單。

```text
docker help
```

查詢指定的 Docker 指令說明及用法。

```text
docker help pull
```

### 搜尋映像檔

使用 `docker search` 指令搜尋 Ubuntu Linux 相關的映像檔。

```text
docker search ubuntu
```

搜尋結果的資料筆數非常多，如何篩選出我們需要的映像檔？

通常我們會從 STARS 數量評比來判斷映像檔的熱門程度，人氣指數愈高通常代表：由官方發行或功能完善且有持續更新維護。用 `-s 20` 篩選 STARS 數量大於 20 的映像檔：

```text
docker search -s 20 ubuntu
```

執行結果：

```text
NAME                       DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED      
ubuntu                     Ubuntu is a Debian-based Linux operating s...   3675      [OK]                      
ubuntu-upstart             Upstart is an event-based replacement for ...   61        [OK]                      
torusware/speedus-ubuntu   Always updated official Ubuntu docker imag...   25                   [OK]           
ubuntu-debootstrap         debootstrap --variant=minbase --components...   24        [OK]                      
rastasheep/ubuntu-sshd     Dockerized SSH service, built on top of of...   24                   [OK]
```

若要讓 search 敘述完整呈現可加上面下面的 Option：

```text
--no-trunc         Don't truncate output
```

### 取得映像檔

使用 `docker pull` 指令從 Docker Hub 下載映像檔。

例如取得最新的 Ubuntu Linux 作業系統映像檔，執行：

```text
docker pull ubuntu
```

終端機將會出現下面訊息：

```text
Using default tag: latest
latest: Pulling from library/ubuntu

d38575f188e0: Pull complete
b04ea90f261c: Pull complete
40dc9cd44ffa: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:4bc45eecd925cb8806294d0e8bc5c1cfd1149c6dd8a5ef2165b6c02eabb8821f
Status: Downloaded newer image for ubuntu:latest
```

實際上這條指令省略了兩個參數內容：

1. Registry（註冊伺服器）
2. Tag（版本標記）

加上 Tag 版本標記，明確指定取得 `ubuntu:14.04`（目前的最新版本）的映像檔。

```text
docker pull ubuntu:14.04
```

以下指令的執行結果與 `docker pull ubuntu` 相同。

```text
# 加上 Tag 指定最新版
docker pull ubuntu:latest

# 加上 Registry，指定以 Docker Hub 註冊伺服器作為來源
docker pull registry.hub.docker.com/ubuntu

# 同時指定 Registry 與 Tag
docker pull registry.hub.docker.com/ubuntu:latest

# 指定以 DockerPool 註冊伺服器作為來源
docker pull dl.dockerpool.com:5000/ubuntu
```

備註：除了 Docker Hub 或 DockerPool 也可以搭配自行架設的私有註冊伺服器（Registry Server）。

### 更新映像檔

如果使用 `latest` 版本標記取得映像檔，日後可以重新執行 `docker pull` 檢查更新。

例如當 `ubuntu` 發佈更新後執行：

```text
docker pull ubuntu
```

或

```text
docker pull ubuntu:latest
```

就可以更新為最新版本。

### 列出映像檔

執行 `docker images` 指令列出本機上已有的映像檔：

```text
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
ubuntu                        latest              a1e4ed2ac65b        8 hours ago         188 MB
ubuntu                        14.04               07c86167cdc4        4 weeks ago         188 MB
```

* REPOSITORY

  代表映像檔所在的倉庫（Repository）名稱。

* TAG

  版本標記（若是在 `git pull` 時沒有指定版本，就會顯示 `latest` 代表預設的最新版本）。

* IMAGE ID

  映像檔的唯一識別編號（檔案的 SHA-1 Hash 編碼）。

### 檢視映像檔資訊

先執行 `docker images` 查看映像檔：

```text
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
ubuntu                        latest              a1e4ed2ac65b        8 hours ago         188 MB
ubuntu                        14.04               07c86167cdc4        4 weeks ago         188 MB
```

依據上面的清單內容，如果我們想檢視 `ubuntu:14.04` 的詳細資訊，可以執行 `docker inspect`：

```text
docker inspect ubuntu:14.04
```

或是指定 IMAGE ID：

```text
docker inspect a1e4ed2ac65b
```

執行 `inspect` 的輸出結果為 JSON 格式，在 Console 中可以搭配 `less` 方便捲動檢視。

### 備份與載入映像檔

執行 `docker save` 指令將映像檔備份為 `ubuntu-latest.tar`：

```text
docker save -o ubuntu-latest.tar ubuntu:latest
```

如此一來，我們就能將 `ubuntu-latest.tar` 透過 USB 隨身碟等方式複製到其他電腦。

取得 `ubuntu-latest.tar` 的電腦，即可用 `docker load` 指令載入映像檔。

```text
docker load --i ubuntu-latest.tar
```

如果遇到需要在沒有網路的環境下離線作業，或是不想透過網路傳輸體積大的映像檔，`docker save` 與 `save load` 提供一個簡單實用的方式。

### 刪除映像檔

先執行 `docker images` 查看映像檔：

```text
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
ubuntu                        latest              a1e4ed2ac65b        8 hours ago         188 MB
ubuntu                        14.04               07c86167cdc4        4 weeks ago         188 MB
```

依據上面的清單內容，如果我們想刪除 `ubuntu:14.04` 的映像檔，可以執行 `docker rmi`：

```text
docker rmi ubuntu:14.04
```

刪除的動作有一個前提，該映像檔必須沒有運行的容器（Container），否則就會產生錯誤訊息。然而，如果確定要放棄 Container（可能導致容器資料流失），可加上 `-f` 參數強制刪除映像檔。

```text
docker rmi -f ubuntu:14.04
```

實務上我們應避免直接強制刪除映像檔，正確的做法是，先刪除映像檔相關的所有容器，然後才刪除映像檔。

