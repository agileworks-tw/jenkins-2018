# 認識 Docker

Docker是一個開放原始碼軟體專案，讓應用程式布署在軟體容器下的工作可以自動化進行，藉此在Linux作業系統上，提供一個額外的軟體抽象層，以及作業系統層虛擬化的自動管理機制。Docker利用Linux核心中的資源分離機制，例如cgroups，以及Linux核心命名空間，來建立獨立的軟體容器。

Docker, as the clear leader of the containerization movement, has dominated the software industry in the recent years. It allows the packaging of an application in the environment- agnostic image and therefore treats servers as a farm of resources, rather than machines that must be configured for each application. Docker was a clear choice for this book because it perfectly fits the \(micro\) service world and the Continuous Delivery process.

Together with Docker comes additional technologies, which are as follows:

* Docker Hub: This is a registry for Docker images
* Docker Compose: This is a tool to define multicontainer Docker applications
* Docker Swarm: This is a clustering and scheduling tool

### 比較虛擬機器與虛擬化容器

![](.gitbook/assets/image%20%2884%29.png)

由多個 Layer 組成 Docker image。

![](.gitbook/assets/image%20%28115%29.png)

使用 Ubuntu image 運行 Docker 容器。

![](.gitbook/assets/image%20%2888%29.png)

### 參考資料

* [Containers 101: Docker fundamentals](https://www.infoworld.com/article/3077875/linux/containers-101-docker-fundamentals.html)



