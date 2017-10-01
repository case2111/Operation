# Gerrit支持静态代码扫描分析
#### 导语：
> 　　我们使用SonarQube分析静态代码，为了提高入库代码质量，应在代码评审时就显示分析结果。为了支持此功能，Gerrit应做到两点：有人提交代码时触发代码分析、分析后将结果显示出来。所幸Gerrit很强大，配合Jenkins完全可以做到这两点。
> 　　Gerrit提供事件流，当某事件触发时，可以通知用户该事件被触发，我们只需在Gerrit上为某用户配置该权限即可；Gerrit提供添加评审的接口，因此三方库可以通过调用接口实现评审，我们可以利用此接口显示代码分析结果。
> 　　以下是Gerrit为支持静态代码扫描分析做的配置。

## 一、事件通知
为sonarqube_review用户配置权限：
1. Projects搜索“All-Projects” => 点击Access配置权限 => Global Capabilities	下添加Stream Events权限 => 添加Non-Interactive Users用户组拥有该权限。
2. People => 点击List Groups => 搜索Non-Interactive Users并点击进入 => Members添加sonarqube_review为该组用户。

## 二、代码评审
1. 配置sonarqube_review拥有All-Projects的Read权限。
2. 由于添加评审API需要以HTTP协议发送，需要有用户及密码（无法使用SSH密钥），因此登录sonarqube_review，在个人设置中生成HTTP Password，该密码在Jenkins配置中将会用到。
