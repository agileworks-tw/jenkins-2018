---
description: 新版 Jenkins 2.0 正式加入 Pipeline 的功能，實現持續整合專屬的 Workflow 流程定義方式。
---

# Jenkins Pipeline

Jenkins 1.x 就已經存在 Workflow Plugin，但它並不是  Jenkins 內建的功能。從 Jenkins 2.0 開始正式加入 Pipeline 的功能，它為 Jenkins 的使用者帶來更友善的 DSL（Domain Specific Language）語法，是一種專為持續整合工作流程（Workflow）特別設計的語法。

Pipeline 的程式碼實際上是以 Groovy 程式語言撰寫，這是一種 Java 平台上的 Scripting Language，很早就用於 Jenkins 的 Script Console，近來受歡迎的 Gradle 建置工具，也是以 Groovy 語言為基礎。

Pipeline 帶來的最大改變是：建置流程定義（Build Definition）以獨立的腳本形式撰寫，也能作為專案的一部分，被加到專案的版本控管。

儘管過去可以使用一些 Jenkins Plugin 實現類似的 Workflow 流程定義，但是 Jenkins 的創建者 Kohsuke Kawaguchi 在 InfoQ 指出 Pipeline 並不只是一個 Plugin，它實際由一系列的 Plugin 組成全新概念的子系統。換句話說，使用 Jenkins 2.0 版本的使用者，應該更加重視以 Pipeline 方式定義建置流程的做法，並盡可能挑選與 Pipeline 相容的 Jenkins Plugin 搭配應用。

### 開始使用 Pipeline

Pipeline 是以 Groovy 程式語言撰寫，它是一種簡單易用的 Scripting Language，可以直接在 Java 平台上執行，經常出現在 Java 開發的軟體中，用於提供自定義程式碼的擴充功能，例如 Gradle 與 JasperReport 都可以看到 Groovy Scripting 的應用。

Jenkins 則在 Pipeline 與 Script Console 等功能，可以使用 Groovy 語法撰寫 Script 代碼。

### 第一個 Pipeline 程式

使用 `echo`語法輸出一段訊息。

```groovy
echo 'Hello Pipeline'
```

執行結果：

```text
Started by user anonymous
[Pipeline] echo
hello from Pipeline
[Pipeline] End of Pipeline
Finished: SUCCESS
```

Groovy 也可以用更接近 Java 語法的方式撰寫，例如：

```groovy
echo("hello from Pipeline");
```

但提供更多彈性，並且可以更加簡潔，例如省略括號 `()` 與敘述句結尾的分號 `;`，而字串也可以使用單引號取代雙引號。

如果對於 Groovy 程式語言有興趣，可以參考 CodeData 的技術專欄文章「[認識 Groovy 的第一課](http://www.codedata.com.tw/java/groovy-tutorial-1-understanding-groovy/)」。

### 存取 Git Repository 與執行 Shell 指令

這個範例示範了如何從 GitHub 取回最新的 Git Repository 檔案，並且使用 Maven 進行建置與測試。

```groovy
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'
  sh "${mvnHome}/bin/mvn test"
}
```

因為 DSL 的特性，非常直覺地使用 `git`指令存取 Repository，這樣的流程定義本身就具備良好的可讀性。

```groovy
git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
```

也可以增加參數，例如指定某個 branch 分支：

```groovy
git url: 'https://github.com/agileworks-tw/spring-boot-sample.git', branch: 'develop'
```

如果沒有特別指定，預設就是採用 `master` 分支。

第三行`def mvnHome = tool 'M3'`則是從 Jenkins 的「Global Tool Configuration」設定中，讀取有關 `M3`的 Maven 設定：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/tool-M3.png)

以下指令則為使用該 tool 利用 mvn 進行專案的測試：

```groovy
sh "${mvnHome}/bin/mvn test"
```

而實際上執行的指令是：

```groovy
sh([script: mvnHome + '/bin/mvn test'])
```

Windows 批次檔的寫法則是：

```groovy
bat "${mvnHome}\\bin\\mvn test"
```

### 管理環境變數

在 Pipeline 我們可以使用 env 來存取 Jenkins 所設置的環境變數，我們也可以使用 env 來設置新的環境變數。

延續上一個範例：

```groovy
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'
  env.PATH = "${mvnHome}/bin:${env.PATH}"
  sh 'mvn test'
}
```

透過 env 的使用，我們就不需要使用 `${mvnHome}/bin/mvn` 這麼長的程式路徑。

上述的處理方式，只有當你使用但一 slave 時比較沒有問題，有時候我們需要指定某項建置需要特定的環境變數時，我們可以改為

```groovy
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {
    sh 'mvn test'
  }
}
```

`withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {` 得意思就是原有的 `PATH` 變數，加上新的 `Maven` 環境變數

如此就可以限制該環境變數限制在特定的 scope 一但執行完成，環境變數就會還原成原本的內容。

除此之外，一些 Jenkins 本來在建置過程中就會可用的變數也可以透過 env 直接存取，比如 `env.BUILD_TAG` 可在 Groovy Script 中使用，或是在 `sh` 時使用 `$BUILD_TAG` 進行該變數存取。

### 記錄測試結果，以及建置封存

```groovy
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'
  sh "${mvnHome}/bin/mvn test package"
  step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar', fingerprint: true])
  step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
}
```

其中 `$class` 為縮寫，以 JUnitResultArchiver 為例，完整路徑為：

`hudson.tasks.junit.JUnitResultArchiver`

可以從 Jenkins UI 找到一些線索，如下圖：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/plugin-params.png)

### 指定特定 Slave 進行建置

假設已經有設置 Jenkins Slave 節點：

```groovy
node('master') {
    // as before
}
```

我們可以透過上述方式指定特定的 `node` 進行建置，若沒有指定將由 Jenkins 自行判斷。

除了節點的 name，也可以指定特定的 label，例如：

```groovy
node('unix && 64bit') {
    // as before
}
```

定義方式可參考下圖：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/slave-label.png)

若點選建置，你將可以看到下列訊息輸出

`Running on <yourslavename> in /<slaveroot>/workspace/<jobname>`

### 中途暫停

```groovy
node('remote') {
  input 'Ready to go?'
  // rest as before
}
```

使用情境如：用於確認 QA 是否正確，若確認完成進行 production deploy，又或者輸入特定參數。

### 使用 try... catch... final

我們在進行建置時，就像在寫程式一樣，不時會有例外（Exception）產生，同樣我們可以透過 `try... catch... final` 來定義，例如：

```groovy
try{
  stage 'Approve, go production'
  def url = 'http://localhost:8000/'
  input message: "Does staging at $url look good? ", ok: "Deploy to production"
}finally{
  sh "docker-compose stop server"
}
```

使用 docker 進行 QA 驗證，無論最後是否要要進行 production deploy 皆需要將 docker 停止。

### stage 定義

stage 的定義，可以讓你的建置步驟更清楚，可讀性更好：

```groovy
node {
  stage 'setup'
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'

  stage 'test'
  sh "${mvnHome}/bin/mvn test package"


  stage 'report'
  step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])

  stage 'Artifact'
  step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar', fingerprint: true])

}
```

且 Jenkins 在 task 中 stage view 進度上可以看到每個 stage 的執行過程與結果，如下圖：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/stage-view.png)

### 使用 Jenkinsfile 定義 Pipeline as Code

我們可以把前面章節所完成的相關 Task，撰寫成一個 Jenkinsfile 定義檔：

```groovy
node {
  stage 'checkout project'
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'

  stage 'check env'

  sh "mvn -v"
  sh "java -version"

  stage 'test'
  sh "mvn test"

  stage 'package'
  sh "mvn package"

  stage 'preview'
  sh 'make deploy-default'

  stage 'report'
  step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])

  stage 'Artifact'
  step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar', fingerprint: true])

  try{
    stage 'Approve, go production'
    def url = 'http://localhost:8000/'
    input message: "Does staging at $url look good? ", ok: "Deploy to production"
  }finally{
    sh "ssh jenkins@localhost 'kill `cat deploy/release/run.pid`'"
  }

  stage 'deploy'
  sh 'make deploy-default'
}
```

執行結果如下：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/build/stage-view.png)



