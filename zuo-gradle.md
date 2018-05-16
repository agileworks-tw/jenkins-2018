---
description: Gradle 是新一代的 Java 自動化建置工具。
---

# 實作：Gradle

Gradle 簡單說就是 Java 世界的 Makefile，它可以幫忙打理那些在專案開發過程中的瑣事，舉凡編譯、測試、檢查程式碼、產生文件、清理或壓縮檔案、上傳、發佈、重新啟動伺服器到送出電子郵件，都可以利用 Gradle 撰寫的 Script 來自動完成作業。

Gradle - [https://gradle.org/](https://gradle.org/)

檢查 Gradle 版本。

```text
gradle --version
```

開始建立 Gradle 專案。

```text
mkdir basic-demo
cd basic-demo
gradle init --type java-application
```

Gradle Project Directory Layout：

* `build.gradle`  Project configuration script for configuring tasks in the current project
* `gradle / wrapper / gradle-wrapper.jar` Gradle Wrapper executable JAR
* `gradle / wrapper / gradle-wrapper.properties` Gradle Wrapper configuration properties
* `gradlew` Gradle Wrapper script for Unix-based systems
* `gradlew.bat` Gradle Wrapper script for Windows
* `settings.gradle` Settings configuration script for configuring which Projects participate in the build
* `src / main / java / App.java` Default Java source folder
* `src / test / java / AppTest.java` Default Java test folder

列出專案有哪些 Gradle Task 可用？

```text
gradle tasks
```

執行建置與測試：

```text
gradle test
```

測試結果的輸出：

![](.gitbook/assets/image%20%286%29.png)

建立 Jenkins Job：

```text
./gradlew test
```

修改 `App.java` 程式碼，加入產生 Java Swing 視窗的功能：

```java
import javax.swing.*;

public class App {
    public String getGreeting() {
        return "Hello world.";
    }
    
    private static void createAndShowGUI() {
        JFrame frame = new JFrame("HelloWorldSwing");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JLabel label = new JLabel("Hello World");
        frame.getContentPane().add(label);

        frame.pack();
        frame.setVisible(true);
    }

    public static void main(String[] args) {
        System.out.println(new App().getGreeting());

        javax.swing.SwingUtilities.invokeLater(new Runnable() {
            public void run() {
                createAndShowGUI();
            }
        });
    }
}
```

產生打包的應用程式：

```text
./gradlew distZip
```

下載 `basic-demo.zip` 解壓縮並執行。

