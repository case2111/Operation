# 一、构建步骤：
1. 下载gerrit.war、lib依赖包、plugin插件、容器僵尸进程处理文件(、hooks、mail邮件模板)：
    `./download.sh`
    (注意，下载网站不稳定，可能下载失败，重复执行脚本即可)
2. 执行构建命令：`sudo docker build -t gerrit:2.14.9 .`

# 二、启动容器：
1. 创建数据库容器：参考如下`mysql_run.sh`脚本
```
sudo docker run\
 --name mysql\
 -p 3306:3306\
 -v /home/yourname/gerrit-mysql:/var/lib/mysql\
 -e MYSQL_ROOT_PASSWORD=root@mysql\
 -e MYSQL_DATABASE=reviewdb\
 -e MYSQL_USER=gerrit\
 -e MYSQL_PASSWORD=gerrit@mysql\
 -d mysql:5.7\
 --sql_mode=''
```
2. 启动容器：参考如下`run.sh`脚本（注意映射端口/数据卷/域名/数据库的配置）
参数可见： https://github.com/openfrontier/docker-gerrit/tree/2.14.9-slim（支持gerrit.config所有配置）
```
sudo docker run\
 --name gerrit\
 -p 8090:8080\
 -p 29418:29418\
 -v /home/yourname/gerrit-review_site:/home/gerrit/review_site\
 -e WEBURL=http://localhost:8090/gerrit/\
 -e HOOKCOMMANDURL=localhost:8090/gerrit\
 -e GITWEB_TYPE=gitiles\
 -e DATABASE_TYPE=mysql\
 -e DB_PORT_3306_TCP_ADDR=localhost\
 -e DB_PORT_3306_TCP_PORT=3308\
 -e DB_ENV_MYSQL_DB=reviewdb\
 -e DB_ENV_MYSQL_USER=gerrit\
 -e DB_ENV_MYSQL_PASSWORD=gerrit@mysql\
 -d gerrit:2.14.9
```
3. 备注：
    a) 若挂载空目录时报空指针错误，则可能是连接了有数据的数据库，请确保数据库也为空；
    b) 若想拷贝etc/mail邮件模板目录，修改`download.sh`脚本，且`run.sh`脚本中配置【-e COPY_MAILS=true】；
    c) 若想拷贝器钩子脚本，修改`download.sh`脚本，且`run.sh`脚本中配置【-e COPY_HOOKS=true】；
    d）端口转发：https://blog.csdn.net/qq_21439971/article/details/54926389 
