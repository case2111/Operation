# SonarQube Scanner部署指南
#### 导语：
> 　　由于服务器经常无法连接外网，因此只能自己部署SonarQube Scanner。
  
## 一、Scanner统一版本
1. 下载文件Scanner：
    - 统一使用：[http://mirrors.jenkins-ci.org/updates/updates/hudson.plugins.sonar.SonarRunnerInstaller.json](http://mirrors.jenkins-ci.org/updates/updates/hudson.plugins.sonar.SonarRunnerInstaller.json)（寻找最新版本下载，这里下载：[SonarQube Scanner 3.0.3.778.zip](https://repo1.maven.org/maven2/org/sonarsource/scanner/cli/sonar-scanner-cli/3.0.3.778/sonar-scanner-cli-3.0.3.778.zip)）
    - 官网：[http://redirect.sonarsource.com/doc/analyzing-source-code.html](http://redirect.sonarsource.com/doc/analyzing-source-code.html)
    - Github：[https://github.com/jenkinsci/sonarqube-plugin](https://github.com/jenkinsci/sonarqube-plugin)
2. 上传压缩包至Jenkins服务器的/data/sonarqube目录下，解压文件，得到`/data/sonarqube/sonar-scanner-3.0.3.778`

## 二、Jenkins构建节点的Scanner安装
1. 子节点1：
    - 登录子结点
    - 将sonar-scanner-3.0.3.778复制到~/SonarqubeScanner目录下（其中sonar-scanner-3.0.3.778是第一步解压出来的Scanner文件）
    - 记录节点Scanner的目录：`/home/jenkins/SonarqubeScanner/sonar-scanner-3.0.3.778`（Jenkisn配置需要用到）

