# SonarQube插件安装
#### 导语
> SonarQube平台提供插件安装，但服务器无法连接外网，因此需要我们手动下载插件进行安装。

## 安装步骤
1. 登录[https://docs.sonarqube.org/display/PLUG/Plugin+Library](https://docs.sonarqube.org/display/PLUG/Plugin+Library)下载想要的插件（jar包）；
2. 将jar包上传到jenkins服务器的/data/sonarqube/sonarqube-6.5/extensions/plugins目录下（可以试用rz命令）
3. 重启SonarQube：`/data/sonarqube/sonarqube-6.5/bin/linux-x86-64/sonar.sh restart`
    **注意**：重启服务器要当心！最好在周末重启，查看**Jenkins**上面有没有相关的任务在运行，没有的话才可重启。

备注：有些插件是收费的，没有许可证无法使用，如SonarCFamily.jar。