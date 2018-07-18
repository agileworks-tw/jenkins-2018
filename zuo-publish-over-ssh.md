# 實作：Publish over SSH

在部署應用服務的伺服器上，建立新使用者：

```text
adduser deploy
```

複製 Public Key 到伺服器：

```text
ssh-copy-id deploy@localhost
```

透過  SSH 執行指令：

```text
ssh deploy@localhost ls -al
```

複製檔案到遠端伺服器：

```text
scp [FILENAME] deploy@localhost:~/[DIR]
```

產生 SSH Key：

```text
ssh-keygen -t rsa
```



