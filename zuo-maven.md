# 實作：Maven

檢查 Maven 版本。

```text
mvn --version
```

在 workspace 建立新的 Maven 專案。

```text
mvn archetype:generate -DgroupId=com.example -DartifactId=MyApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Maven 預設的 Java Project Layout：

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

![](.gitbook/assets/image%20%2814%29.png)

清除建置產生的檔案。

```text
mvn clean
```



