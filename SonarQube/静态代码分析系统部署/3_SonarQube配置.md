# SonarQube配置
#### 导语：
> 本文是SonarQube为支持部门的静态代码检查做的配置。


## 一、登录
浏览器打开SonarQube页面，使用admin用户登录。


## 二、质量配置
> 基于原有的插件及规则，创建一个符合自己编程规范的代码分析规则。

1. 基础规则创建：
    - JAVA:
      - 点击质量配置，复制Android Lint配置生成MY_Android配置。
      - 为MY_Android添加Sonar Way配置
      - 将MY_Android设为Java默认配置
    - 其他：
      - 复制默认规则，创建MY_XXX规则，并设为默认，以便以后修改规则
2. 规则修改：（选择MY_Android，点击左侧更多激活规则）
    - Useless imports should be removed：等级次要改为主要
    - Wildcard imports should not be used：挂起改为活动
    - Source code should be indented consistently：挂起改为活动，等级次要改为主要，缩进使用4空格
    - Lines should not be too long：挂起改为活动
    - A close curly brace should be located at the beginning of a line：挂起改为活动，等级次要改为主要
    - Close curly brace and the next "else", "catch" and "finally" keywords should be located on the same line：挂起改为活动，等级次要改为主要
    - Multiple variables should not be declared on the same line：等级次要改为主要
    - Class names should comply with a naming convention：等级次要改为主要
    - Interface names should comply with a naming convention：等级次要改为主要
    - Method names should comply with a naming convention：等级次要改为主要
    - Constant names should comply with a naming convention：修改规则为`^s[A-Z][A-Z0-9a-z]*(_[A-Z0-9a-z]+)*$`
    - Field names should comply with a naming convention：修改规则为`^m[A-Z][A-Z0-9a-z]*(_[A-Z0-9a-z]+)*$`
    - Static non-final field names should comply with a naming convention：修改规则为`^s[A-Z][A-Z0-9a-z]*$`
    - Limited dependence should be placed on operator precedence rules in expressions：挂起改为活动
    - Methods should not have too many lines：挂起改为活动
    - Files should not have too many lines of code：挂起改为活动
    - Return of boolean expressions should not be wrapped into an "if-then-else" statement：等级次要改为主要
3. 规则备份：
   点击质量配置，找到MY_Android，点击右侧下拉按钮，选择备份。

## 三、配置
1. 权限：
    - 进入配置=>权限=>全局权限：取消Anyone的执行分析权限，添加sonar-administrators的执行分析权限。
    - 进入配置=>权限=>权限模板：修改Default template：sonar-users拥有问题管理员权限