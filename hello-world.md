---
description: 以執行 Shell 方式完成程式編譯與執行。
---

# 實作：Hello World

### 登入 GitLab

請先以 user 帳號登入 GitLab 系統。

![http://localhost:8081](.gitbook/assets/image%20%2811%29.png)

選擇「Create a project」建立新專案。

![](.gitbook/assets/image%20%2812%29.png)

新專案名稱填入「helloworld」，按下「Create project」建立。

![](.gitbook/assets/image%20%2814%29.png)

檢視新建的「helloworld」專案頁面。

![](.gitbook/assets/image%20%2872%29.png)

點選「Add Readme」加入專案說明，使用 Markdown 語法撰寫說明檔，完成後按下「Commit changes」儲存修改。

![](.gitbook/assets/image%20%2847%29.png)

![](.gitbook/assets/image%20%2845%29.png)

從「＋」選單選擇「New file」加入新檔案。

![](.gitbook/assets/image%20%2848%29.png)

從 Template 選擇「.gitignore」，並在「Apply a .gitignore template」選擇「Java」程式語言。

![](.gitbook/assets/image%20%2861%29.png)

確認自動從範本產生的 `.gitignore` 檔案內容，按下「Commit changes」儲存新檔案。

![](.gitbook/assets/image%20%2853%29.png)

目前專案的 Repository 已有兩個檔案，分別是「README.md」與「.gitignore」。

![](.gitbook/assets/image%20%287%29.png)

返回專案的首頁，複製 Git Repository URL。

```text
http://localhost:8081/user/helloworld.git
```

![](.gitbook/assets/image%20%2873%29.png)

備註：新增 Repository 也可以在 GitHub 平台進行操作，以下為 GitHub Repository URL 的格式，`{USERNAME}` 是個人 GitHub 帳號名稱。

```text
https://github.com/{USERNAME}/helloworld.git
```

### 複製專案到本地端進行修改

接下來，請回到 Cloud9 使用 `git`指令複製（Clone）整個專案到本地端：

```bash
cd ~/workspace
git clone https://github.com/{USERNAME}/helloworld.git
cd helloworld
```

![](.gitbook/assets/image%20%2882%29.png)

### 撰寫 Hello World 程式

Hello World / Java

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

Hello World / C

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

Hello World / Python

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

![](.gitbook/assets/image%20%2859%29.png)

### 上傳編輯過的專案檔案到 GitHub

先使用 `git add` 加入被修改過的檔案。

```bash
git add HelloWorld.java
git add hello.py
git add main.c
```

設置 Git 使用者名稱與 Email：

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

使用 `git commit` 與 `git push` 指令，將檔案的變動記錄上傳至 GitHub。

```bash
git commit -a -m 'add source files'
git push -u origin master
```

回到 GitHub 檢視是否上傳成功，檢視 Git Repository 的 URL 格式為：

```text
https://github.com/{USERNAME}/hellworld
```

![](.gitbook/assets/image%20%2841%29.png)

### 建立 Jenkins Job

點選左側選單的「新增作業」進入工作定義的畫面，在「Enter an item name」欄位輸入新工作名稱，例如 `helloworld` ，再選擇「建置 Free-Style 軟體專案」。

![](.gitbook/assets/image%20%2871%29.png)

進入 Free-Style Job 的任務組態設定。

![](.gitbook/assets/image%20%283%29.png)

在原始碼管理區塊，選擇「Git」並設定「Repository URL」的內容，這邊使用的 URL 就是在 GitHub 提供的 `.git `位址：

```text
https://github.com/{USERNAME}/helloworld.git
```

![](.gitbook/assets/image%20%2828%29.png)

在「建置」的「新增建置步驟」選擇「執行 Shell」：

![](.gitbook/assets/image%20%2838%29.png)

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

python hello.
```

### 執行建置任務

編輯完成後，按下最下方的「儲存」按鈕，回到專案的主頁面，再點選「馬上建置」。

![](.gitbook/assets/image%20%2835%29.png)

等待建置完成，在「建置歷程」出現建置記錄，點選燈號進入 `#1` 的建置歷程頁面。

![](.gitbook/assets/image%20%2870%29.png)

Jenkins 的燈號代表每次建置與測試結果，有些開發團隊建置「實體」的紅綠燈號，讓專案的健康警訊更容易被察覺。

![&#x5716;&#x7247;&#x53D6;&#x81EA; build-light - https://www.npmjs.com/package/build-light](.gitbook/assets/image%20%2883%29.png)

點選「Console Output」觀察終端機輸出的訊息。

![](.gitbook/assets/image%20%2821%29.png)

撰寫在 Shell 的建置指令，執行結果都會在 Console Output 中記錄。

```text
+ echo https://github.com/agileworks-tw/helloworld.git
https://github.com/agileworks-tw/helloworld.git+ uname -aLinux ubuntu 4.4.0-121-generic #145-Ubuntu SMP Fri Apr 13 13:47:23 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux+ pwd/var/lib/jenkins/workspace/helloworld+ echo - java -- java -+ javac HelloWorld.java+ java HelloWorldHello! World!+ echo - c -- c -+ gcc -o main main.c+ ./mainHello World+ echo - python -- python -+ python hello.pyHello WorldFinished: SUCCESS
```

最後一行 `Finished: SUCCESS` 代表專案建置成功。

### 實作練習

1. 請建立一個 Git 分支（branch）名為 `develop`，並使用新的分支進行建置。
2. 故意製造程式碼的錯誤，讓 Jenkins Job 的執行結果失敗、顯示紅色燈號。
3. 請找出建置過程編譯出的執行檔放在哪個路徑？
4. 如何刪除前次建置遺留的檔案？或是在每次建置後刪除不必保留的檔案？



