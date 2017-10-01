# Jenkins支持静态代码扫描分析
#### 导语：
> 　　为了监控代码质量，我们做了静态代码扫描分析，Jenkins作为中转站，为触发代码扫描和提交分析结果提供了服务。以下是Jenkins为支持代码分析做的配置。

## 一、安装插件
1.  以管理员的身份登录Jenkins（若不登录管理员账号，可能会出现一些问题）
2.  搜索sonar，安装“SonarQube Plugin”、“Sonar Gerrit Plugin”两个插件。
3.  由于服务器经常无法连接外网，因此手动下载提示所需的插件，再上传安装（插件地址可在安装失败后的详情中看到）。
4.  由于workflow-step-api插件需要重启Jenkins才能生效，因此还需重启Jenkins（服务器重启要小心影响）。

## 二、全局配置
1. GerritServer配置：
    - 点击进入【系统管理】=>【Gerrit Trigger】=>【Add New Server】
    - 输入“GerritServer_Sonar”，选择默认配置，点击OK按钮，按如下值配置：
        - Gerrit Connection Setting
            - Hostname：xxx.com
            - Frontend URL：http://xxx.com/gerrit/
            - Username：sonarqube_review
            - E-mail：sonarqube_review@xxx.com.cn
            - SSH Keyfile：xxx
            - SSH Keyfile Password：xxx
            - @点击连接测试，查看配置是否正确。
        - Gerrit Reporting Values
            - 值全部设为0
            - 展开**高级**设置
                - Gerrit Verified Commands
                    - 清空Started、Successful、Not Built评审内容
                - REST API
                    - Gerrit HTTP Username：sonarqube_review
                    - Gerrit HTTP Password：Gerrit上sonarqube_review用户生成的Http Password（参考Gerrit配置2.2）
    - 点击保存
    - 在Gerrit Servers中点击GerritServer_Sonar的Status红色圆圈，使其变蓝可用
2. SonarQube配置：
    - 点击进入【系统管理】=>【系统设置】
    - SonarQube servers：
        - 勾选 Enable injection of SonarQube server configuration as build environment variables
        - Add SonarQube
            - Name：SonarQube_local
            - Server URL：http://xxx.com:9000
            - Server authentication token：token_xxx（参考SonarQube部署4.4）
    - JDK：
        - 新增JDK
            - 别名：JDK8_all
            - 去掉勾选自动安装
            - JAVA_HOME：/usr/lib/jvm/java-8-openjdk-amd64
    - SonarQube Scanner：
        - 新增SonarQube Scanner
            - Name：Scanner_slave1
            - 取消自动安装
            - SONAR_RUNNER_HOME：/home/jenkins/SonarqubeScanner/sonar-scanner-3.0.3.778（参考“构建节点Scanner部署.md”）
    - 点击保存

## 三、Job配置——首次创建分析
> 对于某一项目，我们先手动触发一次代码分析，得到当前版本的分析结果，之后修改配置，使之只分析新提交代码。

1.  在Jenkins创建一个自由风格的job：Check_Sonar_Test
2. 丢弃旧的构建：
    - 天数：30
    - 个数：10
3. Restrict where this project can be run
    - Label Expression：slave1
4. 源码管理
    - 选择git
        - Repository URL：ssh://sonarqube_review@xxx.com:29418/test/SonarTest（项目根据实际修改）
        - Credentials：sonarqube_review（Gerrit下载代码通用账号）
5. 构建
    - 添加构建步骤：Execute SonarQube Scanner
        - JDK：JDK8_all
        - SonarQube Scanner：Scanner_slave1
        - Analysis properties：
              sonar.host.url=http://xxx.com:9000
              sonar.login=token_xxx
              sonar.projectKey=SonarTest（根据项目名修改）
              sonar.projectName=SonarTest（根据项目名修改）
              sonar.sources=src（根据源码目录修改，可设为“.”）
              sonar.language=java（多语言可不填）
              sonar.projectVersion=1.0（版本号）
6. 点击保存
7. 点击立即构建，生成一次成功的代码分析。


## 四、Job配置——日常触发
> 我们已经通过第三步得到了某项目之前版本的分析结果，现在修改配置，使之只分析新提交代码。

1. 点击修改Check_Sonar_Test配置
2. 丢弃旧的构建：
    - 天数：30
    - 个数：10
3. Restrict where this project can be run
    - Label Expression：slave1
4. 源码管理
    - 选择git
        - Repository URL：`ssh://sonarqube_review@xxx.com:29418/test/SonarTest`
        - Credentials：`sonarqube_review（Gerrit下载代码通用账号）`
        - 高级->Refspec：`$GERRIT_REFSPEC`
        - Additional Behaviours：
            - Strategy for choosing what to build
                - Choosing strategy：`Gerrit Trigger`
5. 构建触发器
    - 选择Gerrit event
    - Gerrit Trigger：
        - Choose a Server：GerritServer_Sonar
    - Custom Build Messages：
        - Trigger on：Patch Created + Change Merged + Draft Published
        - Dynamic Trigger Configuration:
            - pattern：test/SonarTest
            - Branches：master
6. 构建（顺序不可颠倒）
    - 添加构建步骤：Execute SonarQube Scanner
        - JDK：JDK8_all
        - SonarQube Scanner：Scanner_slave1
        - Analysis properties：
              sonar.host.url=http://xxx.com:9000
              sonar.login=token_xxx
              sonar.projectKey=SonarTest
              sonar.projectName=SonarTest
              sonar.sources=src
              sonar.language=java
              sonar.projectVersion=1.0
    - 添加构建步骤：Execute SonarQube Scanner
        - JDK：JDK8_all
        - SonarQube Scanner：Scanner_slave1
        - Analysis properties：
              sonar.host.url=http://xxx.com:9000
              sonar.login=token_xxx
              sonar.projectKey=SonarTest
              sonar.projectName=SonarTest
              sonar.sources=src
              sonar.language=java
              sonar.projectVersion=1.0
        - Additional arguments：
              -Dsonar.analysis.mode=preview
              -Dsonar.report.export.path=sonar-report.json
7. 构建后操作
    - 添加构建后操作：Post SonarQube issues as Gerrit comments
        - SonarQube URL：`http://xxx.com:9000`
    - Project Settings->SonarQube report path：`.scannerwork/sonar-report.json`
    - Filter Settings->取消勾选Report new issues only?
    - 高级=>Report Format
        - Title of review for no...：`SonarQube 未检测到不良代码.`
        - Title of review...：`SonarQube 共检测到<total_count>处不良代码.`
        - Pattern for issue comment：
              等级：<severity>

              描述：<message>

              规则详情：<rule_url>
8. 点击保存
