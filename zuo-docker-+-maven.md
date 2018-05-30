# 實作：Docker + Maven

使用 ubuntu\_with\_git\_and\_jdk 進行擴充：

```text
docker run -i -t ubuntu_with_git_and_jdk /bin/bash
```

安裝 Maven：

```text
apt-get install -y maven
maven --version
exit
```

提交 ubuntu\_with\_git\_and\_jdk\_maven：

```text
docker commit 9821a4789b86 ubuntu_with_git_and_jdk_and_maven
```

修改 Jenkinsfile：

```groovy
pipeline {
    agent {
        docker { image 'ubuntu_with_git_and_jdk_and_maven' }
    }
    stages {
        stage('Version') {
            steps {
                sh 'java -version'
                sh 'javac -version'
            }
        }
        stage('Clone') {
            steps {
                git url: 'http://localhost:8081/user/MyApp.git',
                  credentialsId: '023352b7-fbb9-4ee3-9c9c-5b5d734099cc'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean test package'
            }
        }
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar'
                junit 'target/surefire-reports/*.xml'
            }
        }
    }
}
```

