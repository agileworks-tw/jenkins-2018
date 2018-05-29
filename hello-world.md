---
description: 以執行 Shell 方式完成程式編譯與執行。
---

# 實作：Hello World

### 登入 GitLab

請先以 user 帳號登入 GitLab 系統。

![http://localhost:8081](.gitbook/assets/image%20%2816%29.png)

選擇「Create a project」建立新專案。

![](.gitbook/assets/image%20%2817%29.png)

新專案名稱填入「helloworld」，按下「Create project」建立。

![](.gitbook/assets/image%20%2822%29.png)

檢視新建的「helloworld」專案頁面。

![](.gitbook/assets/image%20%28105%29.png)

點選「Add Readme」加入專案說明，使用 Markdown 語法撰寫說明檔，完成後按下「Commit changes」儲存修改。

![](.gitbook/assets/image%20%2862%29.png)

![](.gitbook/assets/image%20%2860%29.png)

從「＋」選單選擇「New file」加入新檔案。

![](.gitbook/assets/image%20%2863%29.png)

從 Template 選擇「.gitignore」，並在「Apply a .gitignore template」選擇「Java」程式語言。

![](.gitbook/assets/image%20%2882%29.png)

確認自動從範本產生的 `.gitignore` 檔案內容，按下「Commit changes」儲存新檔案。

![](.gitbook/assets/image%20%2872%29.png)

目前專案的 Repository 已有兩個檔案，分別是「README.md」與「.gitignore」。

![](.gitbook/assets/image%20%2812%29.png)

返回專案的首頁，複製 Git Repository URL。

```text
http://localhost:8081/user/helloworld.git
```

![](.gitbook/assets/image%20%28106%29.png)

備註：新增 Repository 也可以在 GitHub 平台進行操作，以下為 GitHub Repository URL 的格式，`{USERNAME}` 是個人 GitHub 帳號名稱。

```text
https://github.com/{USERNAME}/helloworld.git
```

{% hint style="info" %}
為管控專案原始碼安全，企業開發團隊自建 GitLab 服務，可以架設於實體伺服器或雲端虛擬主機，並定時進行資料備份。
{% endhint %}

### 複製專案到本地端進行修改

接下來，請回到 Cloud9 使用 `git clone`指令複製整個專案的檔案到本地端：

```bash
cd ~/workspace
git clone http://localhost:8081/demo/helloworld.git
cd helloworld
```

![](.gitbook/assets/image%20%28108%29.png)

### 撰寫 Hello World 程式

分別使用 Java、C 與 Python 撰寫 Hello World 程式。

1. HelloWorld.java
2. main.c
3. hello.py

#### Hello World / Java

{% code-tabs %}
{% code-tabs-item title="HelloWorld.java" %}
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello! World!");
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

編譯與執行：

```bash
javac HelloWorld.java
java HelloWorld
```

#### Hello World / C

{% code-tabs %}
{% code-tabs-item title="main.c" %}
```c
#include <stdio.h>

int main()
{
    printf("Hello World\n");
    return 0;
}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

編譯與執行：

```bash
gcc -o main main.c
./main
```

#### Hello World / Python

{% code-tabs %}
{% code-tabs-item title="hello.py" %}
```bash
print("Hello World")
```
{% endcode-tabs-item %}
{% endcode-tabs %}

執行：

```bash
python hello.py
```

參考螢幕截圖：

![](.gitbook/assets/image%20%2857%29.png)

### 將專案檔案變更上傳到 Git Repository

先執行 `git status`檢視檔案異動。

![](.gitbook/assets/image%20%2881%29.png)

Why？！為什麼異動清單中，並沒有包含 `HelloWorld.class`檔案？

使用 `git add` 加入新增的程式碼檔案。

```bash
git add HelloWorld.java
git add hello.py
git add main.c
```

設置 Git 使用者名稱與電子郵件（只有第一次 commit 前需要設置）：

```bash
git config --global user.email "user@example.com"
git config --global user.name "user"
```

使用 `git commit` 與 `git push` 指令，將檔案的變動記錄上傳至 GitHub。

```bash
git commit -a -m 'add source files'
git push -u origin master
```

![](.gitbook/assets/image%20%2877%29.png)

回到 GitLab 檢視是否上傳成功，檢視 Git Repository 的 URL 格式為：

```text
http://localhost:8081/user/helloworld
```

![](.gitbook/assets/image%20%28100%29.png)

### 建立 Jenkins Job

點選左側選單的「新增作業」進入工作定義的畫面，在「Enter an item name」欄位輸入新工作名稱「 helloworld」，再選擇「建置 Free-Style 軟體專案」，按下「OK」完成建立。

![](.gitbook/assets/image%20%2828%29.png)

進入 Free-Style Job 的任務組態設定。

![](.gitbook/assets/image%20%2890%29.png)

在原始碼管理區塊，選擇「Git」並設定「Repository URL」的內容，這邊填入的 URL 就是 GitLab Repository 的 `.git`位址：

```text
http://localhost:8081/demo/helloworld.git
```

![](.gitbook/assets/image%20%2852%29.png)

解決缺少存取權限的問題，先點「Crendentials」的「Add」按鈕，填入 GitLab 使用者的 Username 與 Password。

![](.gitbook/assets/image%20%2821%29.png)

選取新建的 Credential 設定，測試通過後，錯誤訊息就會消失。

![](.gitbook/assets/image%20%2856%29.png)

接著在「建置」的「新增建置步驟」選擇「執行 Shell」：

![](.gitbook/assets/image%20%2810%29.png)

撰寫建置所需的 Shell 指令：

```bash
echo GIT_URL=$GIT_URL

uname -a
pwd

echo "- java -"

javac HelloWorld.java
java HelloWorld

echo "- c -"

gcc -o main main.c
./main

echo "- python -"

python hello.py
```

### 執行建置任務

編輯完成後，按下最下方的「儲存」按鈕，回到專案的主頁面，再點選「馬上建置」。

![](.gitbook/assets/image%20%28101%29.png)

等待建置完成，在「建置歷程」出現建置記錄，點選燈號進入 `#1` 的建置歷程頁面。

![](.gitbook/assets/image%20%2837%29.png)

![](.gitbook/assets/image%20%2896%29.png)

Jenkins 的燈號代表每次建置與測試結果，有些開發團隊建置「實體」的紅綠燈號，讓專案的健康警訊更容易被察覺。

![&#x5716;&#x7247;&#x53D6;&#x81EA; build-light - https://www.npmjs.com/package/build-light](.gitbook/assets/image%20%28119%29.png)

點選「Console Output」觀察終端機輸出的訊息。

![](.gitbook/assets/image%20%2894%29.png)

撰寫在 Shell 的建置指令，執行結果都會在 Console Output 中記錄。

```text
+ echo GIT_URL=http://localhost:8081/demo/helloworld.git
GIT_URL=http://localhost:8081/demo/helloworld.git
+ uname -a
Linux ubuntu 4.4.0-116-generic #140-Ubuntu SMP Mon Feb 12 21:23:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
+ pwd
/var/lib/jenkins/workspace/helloworld
+ echo - java -
- java -
+ javac HelloWorld.java
+ java HelloWorld
Hello! World!
+ echo - c -
- c -
+ gcc -o main main.c
+ ./main
Hello World
+ echo - python -
- python -
+ python hello.py
Hello World
Finished: SUCCESS
```

最後一行 `Finished: SUCCESS` 代表專案建置成功。

### 實作練習

1. 請建立一個 Git 分支（branch）名為 `develop`，並使用新的分支進行建置。
2. 故意製造程式碼的錯誤，讓 Jenkins Job 的執行結果失敗、顯示紅色燈號。
3. 請找出建置過程編譯出的執行檔放在哪個路徑？
4. 如何刪除前次建置遺留的檔案？或是在每次建置後刪除不必保留的檔案？



