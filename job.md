---
description: 最常用的 Job 定義方式分別是 Free-Style 與 Pipeline 兩種。
---

# 認識 Jenkins Job

### Free-Style 軟體專案

傳統 Jenkins 任務定義方式，搭配各種版本控制與建置系統，使用 Ant、Maven、Bash Shell 或 Windows Batch 方式定義 Script 程式。

### Pipeline

從 Jenkins 2.0 開始帶來的新功能，加入工作流程（Workflow）的概念，使用 Groovy 程式語言撰寫 Script，是一種為持續整合與持續發布所帶來的 DSL（領域描述語言）。

{% hint style="info" %}
使用 Pipeline 是新趨勢，但是有一些 Jenkins Plugins 仍未支援 Pipeline 的整合。 
{% endhint %}

Pipeline Script 程式碼範例。

```groovy
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "SERVER_ID"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo

    stage('Clone sources') {
        git url: 'https://github.com/jfrogdev/project-examples.git'
    }

    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "Maven-3.3.9"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
    }

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install'
    }

    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
```



