# Script Console

Script Console（主控台）使用 Groovy 程式碼撰寫，可以在 Jenkins 伺服器或節點上運行。

![](.gitbook/assets/image%20%2899%29.png)

Groovy Script 程式範例：

```text
println System.getenv("PATH")
println "uname -a".execute().text
println "hostname".execute().text
println "/sbin/ifconfig".execute().text
```

