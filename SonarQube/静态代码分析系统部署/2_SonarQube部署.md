# SonarQube部署指南
#### 导语：
> 　　SonarQube部署指南。因为国内完整使用Gerrit + Jenkins + SonarQube作为代码质量监控的很少，因此在部署静态代码分析系统时遇到不少问题，通过搜索问题和查看官网资料，磕磕碰碰的解决了。部署时参考完整的部署步骤，查看自己哪些没做，遇到问题很有可能问题就发生在没做的配置上。


## 一、创建数据库
1. 安装Mysql：`sudo apt-get install mysql-server-5.6`（需mysql5.6及以上）
2. 创建数据库：`CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;`
  
## 二、SonarQube安装
1. 下载文件SonarQube 6.5：[https://www.sonarqube.org/downloads/](https://www.sonarqube.org/downloads/)
2. 上传压缩包至Jenkins服务器的/data/sonarqube目录下，解压文件
3. 修改配置：
    - /conf/sonar.properties：
        - sonar.jdbc.username=root
        - sonar.jdbc.password=xxxxx
        - sonar.jdbc.url=`jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false`
    - wrapper.conf:
        - wrapper.java.command=/usr/lib/jvm/java-8-openjdk-amd64/bin/java

## 三、插件安装
>由于内部服务器经常无法访问外网，因此需要自己下载插件，复制到/extensions/plugins/目录下，复制后重启SonarQube方可生效。插件地址：[https://docs.sonarqube.org/display/PLUG/Plugin+Library](https://docs.sonarqube.org/display/PLUG/Plugin+Library)，安装插件如下： 

1. 汉化包：sonar-l10n-zh-plugin-1.17.jar
2. java分析插件：sonar-java-plugin-4.11.0.10660.jar（4.12版本以后需要编译才可分析，不好兼容gradel和maven，因此选择4.11，对源码进行分析，支持到java9，需将原来自带的4.12版本删除，此插件版本可从以下网址下载：[http://repo.springsource.org/libs-milestone/org/sonarsource/java/sonar-java-plugin/](http://repo.springsource.org/libs-milestone/org/sonarsource/java/sonar-java-plugin/)）
3. LDAP验证：sonar-ldap-plugin-2.2.0.608.jar
4. 安卓Lint分析：sonar-android-plugin-1.1.jar
5. C++分析：sonar-cxx-plugin-0.9.7.jar（官网的SonarCFamily收费，使用开源的cxx，下载地址：[https://github.com/SonarOpenCommunity/sonar-cxx/releases](https://github.com/SonarOpenCommunity/sonar-cxx/releases)）

## 四、启动服务器
1. 启动项目：
    - `./bin/linux-x86-64/sonar.sh start`
2. 在浏览器中输入`http://服务器IP:9000/`
3. 使用admin/admin默认管理员账号登录，修改密码
4. 为Jenkins生成一个Token


## 五、LDAP验证
1. 修改/conf/sonar.properties配置，在文件末尾添加ldap验证
       #----------------------
       # LDAP authentication
       sonar.security.realm=LDAP
       sonar.security.savePassword=true
       ldap.url=ldap://ldap.xxx.com:389

       # User Configuration
       ldap.user.baseDn=dc=xxx,dc=com
       ldap.user.request=uid={0}
       ldap.user.realNameAttribute=cn
       ldap.user.emailAttribute=mail
       # 若LDAP不支持匿名访问，还需加上以下配置
       ldap.bindDn=cn=my_admin,ou=admin,dc=xxx,dc=com
       ldap.bindPassword=xxxx
2. 重启SonarQube：`./bin/linux-x86-64/sonar.sh restart`




