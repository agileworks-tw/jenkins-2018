---
description: 使用 Maven Build Tool 自動化建置與測試 Java 專案。
---

# 實作：Maven

檢查 Maven 版本。

```text
mvn --version
```

在 workspace 建立新的 Maven 專案。

```text
mvn archetype:generate -DgroupId=com.example -DartifactId=MyApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd MyApp
```

Maven 預設的 [Standard Directory Layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)：

* `pom.xml` Maven 設定檔
* `src/main/java` 主要的程式碼
* `src/test/java` JUnit 單元測試的程式碼

Maven Build Lifecycle

* validate - validate the project is correct and all necessary information is available
* compile - compile the source code of the project
* test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
* package - take the compiled code and package it in its distributable format, such as a JAR.
* verify - run any checks on results of integration tests to ensure quality criteria are met
* install - install the package into the local repository, for use as a dependency in other projects locally
* deploy - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.

執行一次 `test`觀察產生的 `target` 資料夾。

![](.gitbook/assets/image%20%2817%29.png)

清除建置產生的檔案。

```text
mvn clean
```

在 GitLab 新建一個 Repository，命名為 `MyApp` 。

加入 `.gitignore`設定檔。

{% code-tabs %}
{% code-tabs-item title=".gitignore" %}
```text
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties
.mvn/timing.properties
```
{% endcode-tabs-item %}
{% endcode-tabs %}

`MyApp` 的檔案上傳。

```text
git init
git remote add origin http://localhost:8081/user/MyApp.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

在 Jenkins 新增一個「建置 Free-Style 軟體專案」。

![](.gitbook/assets/image%20%2830%29.png)

設置 Git 原始碼管理。

![](.gitbook/assets/image%20%2880%29.png)

新增建置「呼叫最上層 Maven 目標」，Goal 設定為 test。

![](.gitbook/assets/image%20%28100%29.png)

新增建置後動作「發布 JUnit 測試結果報告」。

![](.gitbook/assets/image%20%2889%29.png)

建置結果畫面：

![](.gitbook/assets/image%20%284%29.png)

JUnit 測試結果報告。

![](.gitbook/assets/image%20%2810%29.png)



