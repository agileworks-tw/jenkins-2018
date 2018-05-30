# 實作：Docker 101

## Docker Image

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

## Docker Container

容器（Container）是映像檔（Image）的執行實例（Instance），容器帶有可讀寫的資料存取空間，讓容器的作業系統與應用程式在運行時，可以保存額外的狀態與資料。

本單元將練習以下的 Docker 指令：

`create`, `start`, `stop`, `ps`, `run`, `exec`, `attach`, `rm`

### 建立容器

先確認本機已有 `ubuntu:latest` 映像檔。

```text
docker pull ubuntu
```

執行 `docker create` 指令新建一個容器。

```text
docker create -it ubuntu:latest
```

為什麼需要 `-it` 參數呢？實際上這是 `-i` 與 `-t` 兩個參數的縮寫。

* `-i`

  Keep STDIN open even if not attached

* `-t`

  Allocate a pseudo-TTY

使用 `docker ps` 檢視所有容器的狀態。

```text
docker ps -a
```

```text
CONTAINER ID      IMAGE             COMMAND
2c06ac467b38      ubuntu:latest     "/bin/bash"
```

因為 `docker create` 指令建立的容器預設為停止狀態，所以加上參數 `-a` 才能顯示包含已停止的容器。

從上面的執行結果，我們可以得知容器的 `CONTAINER ID`，利用 ID 來啟動（或停止）容器。

啟動容器：

```text
docker start 2c0
```

通常我們對容器進行操作時，為了讓指令輸入方便，並不會使用完整的 `CONTAINER ID`，只要輸入前三碼字母即可。

容器啟動後，再以 `docker ps` 查詢執行中的容器，可以看到 STATUS 的變化。

```text
docker ps
```

停止容器，使用 `docker stop` 指令。

```text
docker stop 2c0
```

### 執行容器

使用 `docker run` 可以啟動一個容器，並執行自訂的 Command Line 指令。

```text
docker run ubuntu /bin/echo Hello, Docker!
```

執行結果會輸出：

```text
Hello, Docker!
```

實際上 `echo` 指令是在 Ubuntu Linux 作業系統容器中執行。

多試試其他 Ubuntu Linux 的指令，例如 `uname`：

```text
docker run ubuntu /bin/uname -a                                 
```

輸出結果：

```text
Linux 5d45430a0a54 4.2.0-27-generic #32~14.04.1-Ubuntu SMP Fri Jan 22 15:32:26 UTC
2016 x86_64 x86_64 x86_64 GNU/Linux  
```

Docker Container 提供一個完全隔離的環境給 Ubuntu Linux，有別於傳統的虛擬機器，Docker 的容器十分輕量、隨時可以新建或刪除。

### 執行 Bash Shell

執行：

```text
docker run -it ubuntu:14.04 /bin/bash
```

在 Container 的 Shell 試試一些常用的 Linux 指令：

* `ls`
* `ps`
* `pwd`
* `dmesg`
* `uname`

要結束 Shell 只要執行：

```text
exit
```

退出後可以用 `docker ps -a` 觀察容器的狀態，可以發現每一次 Docker 執行完指令後，都會回到停止狀態。

### 背景執行

讓一個長時間執行的指令在背景運作，可以利用 `-d` 參數。

```text
docker run -d -it ubuntu ping google.com
```

參數說明：

* `-d` 表示讓程式在後台以背景狀態（Daemonized）的形式執行。

透過 `docker ps` 查詢容器的狀態：

```text
CONTAINER ID        IMAGE               COMMAND
374fb78dab41        ubuntu              "ping google.com"
```

要如何檢視 `ping google.com` 指令的輸出呢？可以透過 `docker logs` 指令：

```text
docker logs 374
```

如果想要持續顯示最新的輸出，也可以加上 `-f` 參數（結束用 Ctrl + C 組合鍵）：

```text
docker logs -f 374
```

### 進入容器

正在背景狀態執行的容器，可以利用 `docker exec` 執行新的 Bash Shell。

```text
docker exec -ti 374 /bin/bash
```

或是執行指令，例如 `ps aux`：

```text
docker exec -ti 374 ps aux
```

輸出結果：

```text
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND        
root         1  0.0  0.2  12832  2048 ?        Ss+  11:01   0:00 ping google.com
root        26  0.0  0.2  15572  2176 ?        Rs+  11:01   0:00 ps aux
```

也可以利用 `docker attach` 指令，切換至容器的終端機。

```text
docker attach 374
```

如果有多位使用者操作同一個容器，通常執行 `exec` 會比 `attach` 合適，因為 `attach` 會讓每個使用者共用同一個終端機畫面，因此造成互相牽制影響。

### 移除容器

列出所有容器（包含停止狀態的容器）。

```text
docker ps -a
```

輸出如下：

```text
CONTAINER ID        IMAGE               COMMAND
270ea7ee8b06        ubuntu              "/bin/bash"
```

如果要移除 `270ea7ee8b06` 這個容器，可以執行 `docker rm` 指令：

```text
docker rm 270
```

假如容器還在運行中會出現錯誤，加上 `-f` 參數可以強制移除容器。

```text
docker rm -f 270ea7ee8b06
```

如果同時要移除（或停止）所有容器，可以搭配以下 Shell 指令進行：

先停止所有容器。

```text
docker stop $(docker ps -a -q)
```

再移除所有容器。

```text
docker rm $(docker ps -a -q)
```



