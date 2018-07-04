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

印出所有 Job

```groovy
Jenkins.instance.allItems.findAll { item -> 
  println "${item.name} = (${item.lastBuild.result})"
  item.disabled != true &&
  item.getLastBuild().result != Result.SUCCESS
}
```

印出所有 Slave

```groovy
// 情境1  刪除所有 X299-CentOS 起始的節點
Jenkins.instance.slaves.each { slave ->
  println "$slave.nodeName"
  if (slave.nodeName.startsWith('X299-CentOS')) {
    println "remove $slave.nodeName"
    hudson.removeNode(slave)
  }
}
Jenkins.instance.save()



//情境2 批次新增節點
import hudson.model.Node.Mode
import hudson.slaves.*

['06B-W10-0012', '06B-W10-0013', '06B-W10-0014']
.each { nodeName ->
  dumb = new DumbSlave(
    nodeName,				//名稱
    "/home/liteon/Jenkins", //遠端檔案系統根目錄
    new JNLPLauncher() 		//啟動模式
  )
  Jenkins.instance.addNode(dumb)
}

Jenkins.instance.save()
```

