---
description: 基本的 Docker 指令操作。
---

# 實作：Docker Hello World

使用 `docker run`執行指定的 Docker Image。

```text
docker run hello-world
```

![](.gitbook/assets/image%20%286%29.png)

### 製作 Docker Images

以 Ubuntu Linux 為基礎，加入 Git 與 JDK 等開發工具。

![](.gitbook/assets/image%20%2886%29.png)

搜尋 Ubuntu 的 Docker Images。

```text
docker search ubuntu
docker search java
```

運行一個 `ubuntu:16.04`容器。

```text
docker run -i -t ubuntu:16.04 /bin/bash
```

執行後會進入容器的 Shell 模式：

```text
root@3cc3dd21077a:/# 
```

其中 `3cc3dd21077a`是容器的代號（Container ID）。

透過 Shell 輸入指令安裝 Git 工具：

```text
apt-get update
apt-get install -y git
which git
exit
```

檢查檔案系統的異動：

```text
docker diff 3cc3dd21077a
```

提交安裝 Git 的容易，變成新的 Image。

```text
docker commit 3cc3dd21077a ubuntu_with_git
```

查詢目前存在系統中的容器：

```text
docker images
```

再次運行容器，這次改以 `ubuntu_with_git`執行 Shell：

```text
docker run -i -t ubuntu_with_git /bin/bash
```

從 Shell 提示取得 Container ID。

```text
root@ffc306724a74:/# 
```

安裝 OpenJDK 開發工具。

```text
apt-get install -y openjdk-9-jdk-headless
exit
```

再次提交新的 Docker Image。

```text
docker commit ffc306724a74 ubuntu_with_git_and_jdk
```

檢查 `java` 與 `javac` 的版本。

```text
docker run --rm ubuntu_with_git_and_jdk java -version
docker run --rm ubuntu_with_git_and_jdk javac -version
```

撰寫 HelloWorld 程式碼。

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello! World!");
    }
}
```

使用 Docker 進行編譯。

```text
docker run --rm -v $PWD:/app -w /app ubuntu_with_git_and_jdk javac Main.java
```

使用 Docker 執行程式。

```text
docker run --rm -v $PWD:/app -w /app ubuntu_with_git_and_jdk java Main
```

建立一個 Git Repository，並將程式碼上傳。

使用 Pipeline 進行編譯與執行。

```groovy
node('master') {
  git url: 'http://localhost:8081/user/HelloWorld.git',
    credentialsId: '023352b7-fbb9-4ee3-9c9c-5b5d734099cc',
    branch: 'master'
  
  sh 'docker run --rm -v $PWD:/app -w /app ubuntu_with_git_and_jdk java -version'
  sh 'docker run --rm -v $PWD:/app -w /app ubuntu_with_git_and_jdk javac -version'
    
  sh 'docker run --rm -v $PWD:/app -w /app ubuntu_with_git_and_jdk javac Main.java'
  sh 'docker run --rm -v $PWD:/app -w /app ubuntu_with_git_and_jdk java Main'
}
```



