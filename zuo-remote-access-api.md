# 實作：Remote Access API

練習：

1. 新增作業（Jenkins Job）名稱：`HelloWorld`
2. 使用 Shell 指令檢查 JDK 版本

   ```text
   java -version
   javac -version
   ```

3. 使用 Git SCM，設定 Git Repository URL

   ```text
   https://github.com/agileworks-tw/java-hello-world
   ```

4. 使用 `javac` 指令進行編譯、`java` 指令執行
5. 使用 curl 指令操作 Remote API：`curl http://localhost:8080/job/HelloWorld/build`
6. 自訂字串參數：`WHO`，並帶入 Shell 指令
7. 設定 Git 分支（branch）

   ```text
   with-name-argument
   ```

8. 使用 curl 指令操作參數化建置 Remote API：`http://localhost:8080/job/HelloWorld/buildWithParameters?WHO=Mary`
9. 使用 Git Parameter 參數化建置切換分支：`BRANCH_NAME`，並帶入 Git SCM 設定
10. 利用 Remote API 回傳 XML 或 JSON 格式的 Jenkins Job 定義資料
11. 使用 Jenkins CLI 刪除 Job

### 參考資料

下面範例需要用到使用者的 token 可在下面網址取得

[http://{jenkins\_url}/user/{username}/configure](http://%7Bjenkins_url%7D/user/%7Busername%7D/configure)

Remote API 範例

```text
curl http://<user>:<token>@localhost:8080/job/HelloWorld/api/json | python -m json.tool
curl http://<user>:<token>@localhost:8080/job/HelloWorld/api/json | python -m json.tool
```

```text
curl http://<user>:<token>@@localhost:8080/job/HelloWorld/api/json | xmllint --format -
```

Jenkins CLI 範例

```text
java -jar jenkins-cli.jar -auth <user>:<token> -s http://localhost:8080/ delete-job HelloWorld
```

關於 cli 部分，可以參考 [http://{jenkins\_url}/cli](http://%7Bjenkins_url%7D/cli)

若要透過 command line 安裝 plugin 也可以透過 cli 完成，如：

`java -jar jenkins-cli.jar -s http://{jenkins_url}:9088/ install-plugin SOURCE ... [-deploy] [-name VAL] [-restart]`

