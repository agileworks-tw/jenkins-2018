# Jenkins Pipeline

#### 課程難度：進階

Jenkins 2.0 版現在已經發佈。該版本增加了一種新的定義構建流程的方式，改善了新用戶的體驗。

Jenkins workflow plugin 並不是什麼新東西，但是2.0版本將提供一種新的核心特性（名字就叫 Pipeline），該特性允許用戶在 DSL（Domain Specific Language）的幫助下定義他們的構建流程。DSL 是 Groovy 其中之一的特性，允許把「建構設定\(build definitioin\)」當作一般程式碼撰寫。因此，建構設定就不只是定義在 Jenkins 的 task，而是可以被版本控制。

其實在此之前，有一些其他 Plugin 用來解決同樣的問題，但是 Jenkins 的創建者 Kohsuke Kawaguchi 對 InfoQ 說 Pipeline 不只是一款 Plugin。

實際上 Pipeline 是由一系列 Plugin 組成的一種意義重大的子系統。關於 2.0 版，我們其中一部分想法是想讓用戶拋棄「從內核開始然後安裝插件」的心態。取而代之的是，當你拿到 Jenkins 2.0 版的時候，你就會得到我們認為能覆蓋 80% 使用場景的功能，其中有些來自內核，有些來自 Plugin。 把管道功能作為核心特性也是業界中其他同行的做法。

紐約市的一名軟體工程師 Jacques Chester 認為，在關注 Jenkins Pipeline 功能的開發者也應該瞭解一下 Concourse。在一次 InfoQ 的採訪中，Chester 說Jenkins 的 Pipeline Plugin 系統其實是為其固有缺陷上的 Patch。

該系統將「通過 Plugin 而實現 Concourse 從設計之初就具有的部分功能從而重塑 Jenkins。Concourse 的基因是可以對整合流程進行版本控制，用完即可丟棄的構建環境以及可以將任務計劃委派給工具而不是手動組裝」。

另外，2.0 版的「Getting Started」有了一種新的體驗，在安裝插件方面為用戶提供一些建議。

### 課程內容

此章節將介紹 Jenkins 2.0 的新特性，以及在前面章節關於 Docker 的應用進行整合，透過 Pipeline 進行 CI 建置。

#### 資料來源

{% embed data="{\"url\":\"http://www.infoq.com/cn/news/2016/04/jenkins-2-beta-pipeline-system\",\"type\":\"link\",\"title\":\"Jenkins 2.0 Beta版发布，添加了管道构建系统\",\"description\":\"Jenkins 2.0 Beta版现在已经发布。该版本包含了一个新的管道构建交付系统，该系统使用了基于Groovy的DSL（Domain Specific Language）。这个新特性和新的用户设置流程旨在以开箱即用的方式为用户提供他们需要的大部分功能\",\"icon\":{\"type\":\"icon\",\"url\":\"http://cdn.infoqstatic.com/statics\_s2\_20180508-0315/apple-touch-icon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"http://cdn4.infoqstatic.com/statics\_s2\_20180508-0315/styles/i/logo-big.jpg\",\"width\":200,\"height\":200,\"aspectRatio\":1}}" %}

## Pipeline 使用

Pipeline 是以 Groovy Script 作為撰寫，為 Java 的動態語言。

### 第一個 Pipeline: Hello World

```text
echo 'hello from Pipeline'
```

#### 執行結果

```text
Started by user anonymous
[Pipeline] echo
hello from Pipeline
[Pipeline] End of Pipeline
Finished: SUCCESS
```

以上面的例子來說，完整的語法為：

```text
echo("hello from Pipeline");
```

我們可以省略 `;`, `()` 並且 `''`, `""` 皆可以使用。

### 建立簡單的 Pipeline

```text
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'
  sh "${mvnHome}/bin/mvn test"
}
```

其中

```text
git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
```

也可以指定 branch，指令如下

```text
git url: 'https://github.com/agileworks-tw/spring-boot-sample.git', branch: 'master'
```

若沒有指定的話，預設為 Master，完整程式碼如下

```text
git([url: 'https://github.com/agileworks-tw/spring-boot-sample.git', branch: 'master'])
```

tool 實際上是讀取 jenkins 中的 `Global Tool Configuration` 所設定的建置工具，如下圖：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/tool-M3.png)

`def mvnHome = tool 'M3'`

就是取得該 tool 定義的結果來進行專案建置。

而下面指令

```text
sh "${mvnHome}/bin/mvn test"
```

則為使用該 tool 利用 mvn 進行專案 test，而實際指令為

```text
sh([script: mvnHome + '/bin/mvn test'])
```

可以看到簡化不少

for windows 可以改為

```text
bat "${mvnHome}\\bin\\mvn test"
```

### 管理環境變數

在 Pipeline 我們可以使用 env 來存取 jenkins 所設置的環境變數，我們也可以使用 env 來設置新的環境變數。

延續上一個範例：

```text
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'
  env.PATH = "${mvnHome}/bin:${env.PATH}"
  sh 'mvn test'
}
```

透過 env 的使用，我們就不需要使用 `${mvnHome}/bin/mvn` 這麼長的程式路徑。

上述的處理方式，只有當你使用但一 slave 時比較沒有問題，有時候我們需要指定某項建置需要特定的環境變數時，我們可以改為

```text
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {
    sh 'mvn test'
  }
}
```

`withEnv(["PATH+MAVEN=${tool 'M3'}/bin"]) {` 得意思就是原有的 `PATH` 變數，加上新的 `Maven` 環境變數

如此就可以限制該環境變數限制在特定的 scope 一但執行完成，環境變數就會還原成原本的內容。

除此之外，一些 Jnekins 本來在建置過程中就會可用的變數也可以透過 env 直接存取，比如 `env.BUILD_TAG` 可在 groovy script 中使用，或是在 `sh` 時使用 `$BUILD_TAG` 進行該變數存取。

### 紀錄測試結果，以及建置封存

```text
node {
  git url: 'https://github.com/agileworks-tw/spring-boot-sample.git'
  def mvnHome = tool 'M3'
  sh "${mvnHome}/bin/mvn test package"
  step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar', fingerprint: true])
  step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
}
```

其中 `$class` 為縮寫，以 JUnitResultArchiver 為例，完整路徑為 `hudson.tasks.junit.JUnitResultArchiver`

可以從 jenkins UI 找到一些線索，如下圖：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/plugin-params.png)

### 指定特定 Slave 進行建置

假設已經有設置 jenkins slave 節點

```text
node('master') {
    // as before
}
```

我們可以透過上述方式指定特定的 node 進行建置，若沒有指定將由 jenkins 自行判斷

除了節點的 name，也可以指定特定的 label，如

```text
node('unix && 64bit') {
    // as before
}
```

定義方式可參考下圖

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/slave-label.png)

若點選建置，你將可以看到下列訊息輸出

`Running on <yourslavename> in /<slaveroot>/workspace/<jobname>`

### 中途暫停

```text
node('remote') {
  input 'Ready to go?'
  // rest as before
}
```

使用情境如：用於確認 QA 是否正確，若確認完成進行 production deploy，又或者輸入特定參數。

### 使用 try... catch... final

我們在進行建置時，就像在寫程式一樣，一定會有另外產生，同樣我們可以透過 `try... catch... final` 來定義，如：

```text
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

stage 的定義，可以讓你的建置步驟更清楚，可讀性更好

```text
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

且 jenkins 在 task 中 stage view 進度上可以看到每個 stage 的執行過程與結果，如下圖：

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/tutorial/stage-view.png)

## 使用 Pipeline 進行建置

我們可以把前面章節所完成的相關 task 濃縮為單一個 Jenkinsfile

```text
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

執行結果如下

![](https://github.com/agileworks-tw/tutorial/raw/ithome/jenkinsWithDocker/jenkins/jenkins2/pipeline/build/stage-view.png)



