# 實作：Pipeline + Maven

### 建立 Java / Maven 專案

使用`mvn` 建立新專案。

```text
mvn archetype:generate -DgroupId=com.example -DartifactId=MyApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

上傳至 GitLab 或 GitHub Repository。

```text
http://localhost:8081/user/MyApp.git
```

### 使用 Pipeline 存取 Git Repository

使用 `git` 指令進行複製（clone）。

```groovy
node {
  git url: 'http://localhost:8081/user/MyApp.git'
}
```

「馬上建置」執行一次，觀察錯誤訊息。

```text
ERROR: Error fetching remote repo 'origin'
...
stderr: remote: HTTP Basic: Access denied
...
```

如果搭配 GitLab Private Repository，將會看到以上的錯誤訊息，表示無法通過驗證，此時需要在 Jenkins 的「Credentials」，建立或取得 GitLab 帳號專屬的 Credentials ID，例如：`023352b7-fbb9-4ee3-9c9c-5b5d734099cc` 。

![](.gitbook/assets/image%20%283%29.png)

在 Pipeline 程式碼加入 `credentialsId` 設定。

```groovy
node {
  git url: 'http://localhost:8081/user/MyApp.git',
    credentialsId: '023352b7-fbb9-4ee3-9c9c-5b5d734099cc'
}
```

也可以用 `branch`指定 Git 的分支。

```groovy
node {
  git url: 'http://localhost:8081/user/MyApp.git',
    credentialsId: '023352b7-fbb9-4ee3-9c9c-5b5d734099cc',
    branch: 'master'
}
```

### 執行 Shell 指令

Linux / Mac OS X

```groovy
sh 'ifconfig'
sh 'ls'
```

Windows

```groovy
bat 'ipconfig'
bat 'dir'
```

### 使用 Maven 進行測試

```groovy
sh 'mvn clean test package'
```

### 封存建置結果與測試報告

```groovy
archiveArtifacts artifacts: 'target/*.jar'
junit 'target/surefire-reports/*.xml'
```

加入建置階段

```groovy
node {
  stage('clone') {
      git url: 'http://localhost:8081/demo/MyApp.git',
        credentialsId: '5ac3d6ca-193b-4f06-96c7-944ea0ccb76a',
        branch: 'master'
  }

  stage('build') {
    sh 'mvn clean test package'
  }
  
  stage('archive') {
      archiveArtifacts artifacts: 'target/*.jar'
      junit 'target/surefire-reports/*.xml'
  }
}
```



