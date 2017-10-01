# Python-Jenkins
#### 导语
> 　　Jenkins是个强大的持续集成工具，在一些特殊情况下，我们需要使用代码调动Jenkins或者获取其数据信息，所幸Jenkins在设计之时就已经支持了让我们通过[REST API](https://ci.jenkins.io/api/) 的方式拿到所有的对象的接口。下面简单介绍下如何使用Python-Jenkins：

## 一、安装
　　　`sudo pip install python-jenkins`

## 二、[使用](http://www.cnblogs.com/znicy/p/5498609.html)
> 进入python命令环境或创建新的.py文件jenkinsApiTest.py

    import jenkins

    #定义远程的jenkins master server的url，以及port
    jenkins_server_url='xxxx:xxxx'

    #定义用户的User Id 和 API Token，获取方式同上文
    user_id='xxxx'
    api_token='xxxx'

    #实例化jenkins对象，连接远程的jenkins master server
    server=jenkins.Jenkins(jenkins_server_url, username=user_id, password=api_token)

    #构建job名为job_name的job（不带构建参数）
    server.build_job(job_name)
    
    #String参数化构建job名为job_name的job, 参数param_dict为字典形式，如：
    param_dict= {"param1"：“value1”， “param2”：“value2”} 
    server.build_job(job_name, parameters=param_dict)
    
    #获取job名为job_name的job的相关信息
    server.get_job_info(job_name)

    #获取job名为job_name的job的最后次构建号
    server.get_job_info(job_name)['lastBuild']['number']
    
    #获取job名为job_name的job的某次构建的执行结果状态
    server.get_build_info(job_name,build_number)['result']　　   
    
    #判断job名为job_name的job的某次构建是否还在构建中
    server.get_build_info(job_name,build_number)['building']

## 三、[方法](http://python-jenkins.readthedocs.io/en/latest/api.html)
1. **get_job_info**(name, depth=0, fetch_all_builds=False)
>  - 描述：获取工程的信息字典.
>  - 参数：	
>    - name：工程名, `str`
>    - depth：JSON 深度, `int`
>    - fetch_all_builds：值为true会返回所有的构建，否则只会返回最近100个构建。这是以牺牲额外的API调用为代价的，该调用可能会返回大量的数据。 `bool`
>  - 返回：	
>    - 工程信息的字典
2. **get_job_info_regex**(pattern, depth=0, folder_depth=0)
>  - 描述：获得工程名字与正则表达式匹配的工程信息列表。
>  - 参数:
>    - pattern：正则表达式, `str`
>    - depth：JSON 深度, `int`
>    - folder_depth：搜索的文件夹深度 `int`
>  - 返回：
>    - 多个工程的信息列表，`list`
3. **get_job_name**(name)
>  - 描述：返回工程名。
>    这大致是一种快速的验证工程是否存在或者是否可访问的方法，而不会对服务器造成太大的压力。
>  - 参数：
>     - name：工程名，`str`
>  - 返回：
>     - 工程名或None
4. **debug_job_info**(job_name)
>  - 描述：用更可读的格式打印作业信息。
5. **jenkins_open**(req, add_crumb=True)
>  - 用于向Jenkins服务器打开HTTP请求的实用程序例程。
    这应该只用于扩展Jenkins API。
6. **get_build_info**(name, number, depth=0)
>  - 描述：获取构建信息字典。
>  - 参数：
>    - name：工程名，`str`
>    - number：构建编号，`int`
>    - depth：深度，`int`
>  - 返回:	
>    - 构建信息的字典，`dict`
>  - 举例:
>  ```
>        >>> next_build_number = server.get_job_info('build_name')['nextBuildNumber']
>        >>> output = server.build_job('build_name')
>        >>> from time import sleep; sleep(10)
>        >>> build_info = server.get_build_info('build_name', next_build_number)
>        >>> print(build_info)
>        {u'building': False, u'changeSet': {u'items': [{u'date': u'2011-12-19T18:01:52.540557Z', u'msg': u'test', u'revision': 66, u'user': u'unknown', u'paths': [{u'editType': u'edit', u'file': u'/branches/demo/index.html'}]}], u'kind': u'svn', u'revisions': [{u'module': u'http://eaas-svn01.i3.level3.com/eaas', u'revision': 66}]}, u'builtOn': u'', u'description': None, u'artifacts': [{u'relativePath': u'dist/eaas-87-2011-12-19_18-01-57.war', u'displayPath': u'eaas-87-2011-12-19_18-01-57.war', u'fileName': u'eaas-87-2011-12-19_18-01-57.war'}, {u'relativePath': u'dist/eaas-87-2011-12-19_18-01-57.war.zip', u'displayPath': u'eaas-87-2011-12-19_18-01-57.war.zip', u'fileName': u'eaas-87-2011-12-19_18-01-57.war.zip'}], u'timestamp': 1324317717000, u'number': 87, u'actions': [{u'parameters': [{u'name': u'SERVICE_NAME', u'value': u'eaas'}, {u'name': u'PROJECT_NAME', u'value': u'demo'}]}, {u'causes': [{u'userName': u'anonymous', u'shortDescription': u'Started by user anonymous'}]}, {}, {}, {}], u'id': u'2011-12-19_18-01-57', u'keepLog': False, u'url': u'http://eaas-jenkins01.i3.level3.com:9080/job/build_war/87/', u'culprits': [{u'absoluteUrl': u'http://eaas-jenkins01.i3.level3.com:9080/user/unknown', u'fullName': u'unknown'}], u'result': u'SUCCESS', u'duration': 8826, u'fullDisplayName': u'build_war #87'}
>  ```
7. **get_queue_info**()
>  - 返回：工程字典列表，`[dict]`
>  - 举例：
>  ```
>        >>> queue_info = server.get_queue_info()
>        >>> print(queue_info[0])
>        {u'task': {u'url': u'http://your_url/job/my_job/', u'color': u'aborted_anime', u'name': u'my_job'}, u'stuck': False, u'actions': [{u'causes': [{u'shortDescription': u'Started by timer'}]}], u'buildable': False, u'params': u'', u'buildableStartMilliseconds': 1315087293316, u'why': u'Build #2,532 is already in progress (ETA:10 min)', u'blocked': True}
>        ```
8. **cancel_queue**(id)
>  - 描述：取消一个排队的构建。
>  - 参数：	
>    - id：该构建的id，`int`
9. **get_info**(item='', query=None)
>  - 描述：获取Jenkins的信息或工程信息。
>    信息包括作业列表和视图信息，可以用来对诸如作业文件夹等项目的信息进行重新处理。
>  - 参数：	
>    - item：获取关于这个Jenkins的信息
>    - query：提取关于Jenkins的信息的xpath
>  - 返回：	
>    - 关于Jenkins或工程的信息字典， `dict`
>  - 举例：
>  ```
>        >>> info = server.get_info()
>        >>> jobs = info['jobs']
>        >>> print(jobs[0])
>        {u'url': u'http://your_url_here/job/my_job/', u'color': u'blue',u'name': u'my_job'}
>        ```
10. **get_whoami**()
>  - 描述：获取Jenkins中账户的信息，这是验证用户的一种简单方法。
>  - 返回：当前用户的信息，`dict`
>  - 举例：
>  ```
>        >>> me = server.get_whoami()
>        >>> print me['fullName']
>        'John'
>        ```
11. **get_version**()
>  - 描述：获取Jenkins的版本号。
>  - 返回：Jenkins版本号编码，`str`
>  - 举例：
>  ```
>        >>> info = server.get_version()
>        >>> print info
>        1.541'
>        ```
12. **get_plugins_info**(depth=2)
>  - 描述：获取Jenkins所有已安装的插件信息。
>    以JSON格式返回每个已安装的插件数据。
>    0.4.9版本后更换为`get_plugins()`。
>  - 参数：
>     - depth：JSON深度，`int`
>  - 返回：所有已安装插件信息，`[dict]`
>  - 举例：
>  ```
>        >>> info = server.get_plugins_info()
>        >>> print(info)
>        [{u'backupVersion': None, u'version': u'0.0.4', u'deleted': False,u'supportsDynamicLoad': u'MAYBE', u'hasUpdate': True,u'enabled': True, u'pinned': False, u'downgradable': False,u'dependencies': [], u'url':u'http://wiki.jenkins-ci.org/display/JENKINS/Gearman+Plugin',u'longName': u'Gearman Plugin', u'active': True, u'shortName':u'gearman-plugin', u'bundled': False}, ..]
>        ```
13. **get_plugin_info**(name, depth=2)
>  - 描述：获取某个已安装的插件信息。
>    以JSON格式返回某个插件的信息，插件名称必须严格匹配。
>  - 注意：
>    获取多个插件使用`get_plugins()`代替，它可以模糊匹配名字。
>  - 参数：
>     - name：插件名 (short 或 long)，`str`
>     - depth：JSON深度，`int`
>  - 返回：
>     - 指定插件，`dict`
>  - 举例：
>  ```
>        >>> info = server.get_plugin_info("Gearman Plugin")
>        >>> print(info)
>         {u'backupVersion': None, u'version': u'0.0.4', u'deleted': False,u'supportsDynamicLoad': u'MAYBE', u'hasUpdate': True,u'enabled': True, u'pinned': False, u'downgradable': False,u'dependencies': [], u'url':u'http://wiki.jenkins-ci.org/display/JENKINS/Gearman+Plugin',u'longName': u'Gearman Plugin', u'active': True, u'shortName':u'gearman-plugin', u'bundled': False}
>         ```
14. **get_plugins**(depth=2)
>  - 描述：返回插件信息，使用帮助类进行版本比较。
>    该方法检索所有已安装的插件的信息，并使用一个插件助手类来简化版本比较。也可以使用一个多键来允许通过短或长名字进行检索。
>    版本返回的事unicode字符串。
>  - 参数：
>     - 	depth：JSON深度，`int`
>  - 返回：所有插件的信息，`[dict]`
>  - 举例：
>  ```
>        >>> j = Jenkins()
>        >>> info = j.get_plugins()
>        >>> print(info)
>        {('gearman-plugin', 'Gearman Plugin'):
>        {u'backupVersion': None, u'version': u'0.0.4',
>        u'deleted': False, u'supportsDynamicLoad': u'MAYBE',
>        u'hasUpdate': True, u'enabled': True, u'pinned': False,
>        u'downgradable': False, u'dependencies': [], u'url':
>        u'http://wiki.jenkins-ci.org/display/JENKINS/Gearman+Plugin',
>        u'longName': u'Gearman Plugin', u'active': True, u'shortName':
>        u'gearman-plugin', u'bundled': False}, ...}
>        ```
15. **get_jobs**(folder_depth=0, view_name=None)
>  - 描述：获取工程列表。
>    每个工程都是一个带有名称、url、颜色和全名的字典。
>    如果视图名参数出现，那么作业列表将仅限于在指定视图中配置的那些。在这种情况下，作业字典的fullname键将等于作业名。
>  - 参数：
>     - folder_depth：查找的级数，`int`。默认值为0，只查找顶级目录。 None取消限制。
>     - view_name：视图名，`str`。默认值不限制视图。
>  - 返回：
>      - 工程列表：，`[{str: str, str: str, str: str, str: str}]`
>  - 举例：
>  ```
>        >>> jobs = server.get_jobs()
>        >>> print(jobs)
>        [{
>            u'name': u'all_tests',
>            u'url': u'http://your_url.here/job/all_tests/',
>            u'color': u'blue',
>            u'fullname': u'all_tests'
>        }]
>        ```
16. **get_all_jobs**(folder_depth=None)
>  - 描述：通过目录级数获取所有的工程列表。
>    每个工程都是一个带有名称、url、颜色和全名的字典。
>  - 参数：
>    - 	folder_depth：查找的级数，`int`。默认值为None，没有限制，0表示只查找顶级目录。
>  - 返回
>    - 	工程列表，`[ { str: str} ]`
>  - 注意：
>    在多个文件夹的实例中，使用run脚本方法来检索所有作业可能更有效。
>     - 举例：
>     ```
>            server.run_script("""
>                import groovy.json.JsonBuilder;
>                
>                // get all projects excluding matrix configuration
>                // as they are simply part of a matrix project.
>                // there may be better ways to get just jobs
>                items = Jenkins.instance.getAllItems(AbstractProject);
>                items.removeAll {
>                  it instanceof hudson.matrix.MatrixConfiguration
>                };
>                
>                def json = new JsonBuilder()
>                def root = json {
>                  jobs items.collect {
>                    [
>                      name: it.name,
>                      url: Jenkins.instance.getRootUrl() + it.getUrl(),
>                      color: it.getIconColor().toString(),
>                      fullname: it.getFullName()
>                    ]
>                  }
>                }
>                
>                // use json.toPrettyString() if viewing
>                println json.toString()
>              """)
>              ```
17. **copy_job**(from_name, to_name)
>  - 描述：复制一个工程。
>    当该作业的源和目标文件夹不相同时，将会抛出异常。
>  - 参数：
>     - from_name：要复制的工程名，`str`
>     - to_name：要复制成哪个工程名，`str`
>  - Throws:	
>    `JenkinsException` whenever the source and destination folder are not the same
18. **rename_job**(from_name, to_name)
>  - 描述：重命名一个已存在的工程。
>    当该作业的源和目标文件夹不相同时，将会抛出异常。
>  - 参数：
>     - from_name：旧工程名，`str`
>     - to_name：新工程名，`str`
>  - Throws:	
>     `JenkinsException` whenever the source and destination folder are not the same
19. **delete_job**(name)
>  - 描述：永久删除某工程。
>  - 参数：
>     - name：要删除的工程名，`str`
20. **enable_job**(name)
>  - 描述：开启Jenkins工程。
>  - 参数：
>    - name：工程名，`str`
21. **disable_job**(name)
>  - 描述：关闭Jenkins工程。
>    若要重新打开，调用`Jenkins.enable_job()`.
>  - 参数：
>    - name：工程名，`str`
22. **set_next_build_number**(name, number)
>  - 描述：设置某个工程的下一个构建编号。
>    下一个构建编号包含在工程信息中，调用`Jenkins.get_job_info()`可获得。如果指定的下一个构建号小于最后一个构建号，那么Jenkins将忽略该请求。
>  - 注意：为了支持此功能，必须安装[Next Build Number Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Next+Build+Number+Plugin)插件。
>  - 参数：
>     - name：工程名，`str`
>     - number：下一个构件号，`int`
>  - 举例：
>  ```
>        >>> next_bn = server.get_job_info('job_name')['nextBuildNumber']
>        >>> server.set_next_build_number('job_name', next_bn + 50)
>        ```
23. **job_exists**(name)
>  - 描述：检查工程是否存在
>  - 参数：
>     - name：工程名，`str`
>  - 返回：
>     - 若工程存在返回`True`
24. **jobs_count**()
>  - 描述：获取总工程数。
>  - 返回：总工程数，`int`
>  - 注意：
>    在使用多个文件夹的实例中，使用run脚本方法来检索工作的总数量可能更有效。
>     - 举例：
>     ```
>            # get all projects excluding matrix configuration
>            # as they are simply part of a matrix project.
>            server.run_script(
>                "print(Hudson.instance.getAllItems("
>                "    hudson.model.AbstractProject).count{"
>                "        !(it instanceof hudson.matrix.MatrixConfiguration)"
>                "    })")
>                ```
25. **assert_job_exists**(name, exception_message='job[%s] does not exist')
>  - 描述：若工程不存在，则抛出异常
>  - 参数：
>     - name：工程名，`str`
>     - exception_message：异常的消息，格式化为`name`。
>  - Throws:	
>    `JenkinsException` whenever the job does not exist
26. **create_job**(name, config_xml)
>  - 描述：创建一个工程。
>  - 参数：
>     - name：工程名，`str`
>     - config_xml：配置文件文本，`str`
27. **get_job_config**(name)
>  - 描述：获取一个已存在的工程的配置文件。
>  - 参数：
>    - name：工程名，`str`
>  - 返回：工程配置(XML 格式)
28. **reconfig_job**(name, config_xml)
>  - 描述：改变已存在的工程的配置。
>    若要新建工程，参考`Jenkins.create_job()`。
>  - 参数：
>     - name：工程名，`str`
>     - config_xml：新的XML配置，`str`
29. **build_job_url**(name, parameters=None, token=None)
>  - 描述：获取出发工程的URL。
>    身份验证的设置可能需要在服务器端配置一个令牌。
>  - 参数：
>     - parameters：工程的参数，或None。`dict`
>     - token：构建工程的token，`str`
>  - 返回：
>     - 构建工程的URL。
30. **build_job**(name, parameters=None, token=None)
>  - 描述：触发工程构建。
>  - 参数：
>     - name：工程名
>     - parameters：工程参数，或`None`。`dict`
>     - token：Jenkins API token
31. **run_script**(script)
>  - 描述：在Jenkins服务器上执行一个groovy脚本。
>  - 参数：
>     - script：groovy脚本，`string`
>  - 返回：脚本的执行结果
>  - 举例：
>  ```
>          >>> info = server.run_script("println(Jenkins.instance.pluginManager.plugins)")
>          >>> print(info)
>          u'[Plugin:windows-slaves, Plugin:ssh-slaves, Plugin:translation,
>          Plugin:cvs, Plugin:nodelabelparameter, Plugin:external-monitor-job,
>          Plugin:mailer, Plugin:jquery, Plugin:antisamy-markup-formatter,
>          Plugin:maven-plugin, Plugin:pam-auth]'
>          ```
32. **install_plugin**(name, include_dependencies=True)
>  - 从[Jenkins公共存储库](http://repo.jenkins-ci.org/repo/org/jenkins-ci/plugins)中安装一个插件
>  - 参数：
>     - name：插件简称，`string`
>     - include_dependencies：安装插件的依赖项，`bool`
>  - 返回：
>     - Jenkins是否需要被重启，`bool`
>  - 举例：
>  ```
>          >>> info = server.install_plugin("jabber")
>          >>> print(info)
>          True
>          ```
33. **stop_build**(name, number)
>  - 描述：停止一个正在执行的构建。
>  - 参数：
>     - name：工程名，`str`
>     - number：工程的构建号，`int`
34. **get_running_builds**()
>  - 描述：获取正在运行的构建列表。
>    每个构建都是一个包含‘name’、‘number’、‘url’、‘node’和‘executor’的字典。
>  - 返回：构建列表，`[ { str: str, str: int, str:str, str: str, str: int} ]`
>  - 举例：
>  ```
>          >>> builds = server.get_running_builds()
>          >>> print(builds)
>          [{'node': 'foo-slave', 'url': 'https://localhost/job/test/15/',
>            'executor': 0, 'name': 'test', 'number': 15}]
>            ```
35. **get_nodes**()
>  - 描述：获取构建节点列表
>    每个节点都是一个包含‘name’和‘offline’键的字典。
>  - 返回：
>     - 节点列表，`[ { str: str, str: bool} ]`
36. **get_node_info**(name, depth=0)
>  - 描述：获取节点信息字典。
>  - 参数：	
>     - name：节点名，`str`
>     - depth：JSON深度，`int`
>  - 返回：
>     - 节点信息的字典，`dict`
37. **node_exists**(name)
>  - 描述：检查某节点是否存在
>  - 参数：
>     - name：节点名称，`str`
>  - 返回：
>     - 若节点存在则返回`True`。
38. **assert_node_exists**(name, exception_message='node[%s] does not exist')
>  - 描述：若节点不存在则抛出一个异常。
>  - 参数：	
>     - name：节点名，`str`
>     - exception_message：异常消息，格式化为`name`
>  - Throws:	
>    `JenkinsException` whenever the node does not exist
39. **delete_node**(name)
>  - 描述：永久性删除某节点。
>  - 参数：
>     - name：节点名，`str`
40. **disable_node**(name, msg='')
>  - 描述：关闭一个节点
>  - 参数：
>     - name：节点名，`str`
>     - msg：关机消息，`str`
41. **enable_node**(name)
>  - 描述：开启一个节点。
>  - 参数：
>     - name：节点名，`str`
42. **create_node**(name, numExecutors=2, nodeDescription=None, remoteFS='/var/lib/jenkins', labels=None, exclusive=False, launcher='hudson.slaves.CommandLauncher', launcher_params={})
>  - 描述：创建一个节点
>  - 参数：
>     - name：创建的节点名，`str`
>     - numExecutors：节点的执行器数，`int`
>     - nodeDescription：结点描述，`str`
>     - remoteFS：使用的远程系统文件位置，`str`
>     - labels：与结点关联的标签，`str`
>     - exclusive：仅使用本节点进行绑定作业，`bool`
>     - launcher：节点的启动方法，`jenkins.LAUNCHER_COMMAND`, `jenkins.LAUNCHER_SSH`, `jenkins.LAUNCHER_JNLP`, `jenkins.LAUNCHER_WINDOWS_SERVICE`
>     - launcher_params：启动器的附加参数，`dict`
43. **get_node_config**(name)
>  - 描述：获取节点的配置信息。
>  - 参数：
>     - name：节点名，`str`
44. **reconfig_node**(name, config_xml)
  - 描述：更改一个已存在的节点的配置。
  - 参数：
     - name：节点名，`str`
     - config_xml：新的XML配置，`str`
45. **get_build_console_output**(name, number)
>  - 描述：获取构建的console文本。
>  - 参数：	
>     - name：工程名，`str`
>     - number：构建号，`int`
>  - 返回：
>     - 构建的控制台输出，`str`
46. **get_view_name**(name)
>  - 描述：获取某视图的名字
>    通过此方法快速判断视图是否存在。
>  - 参数：
>     - name：视图名，`str`
>  - 返回：
>     - 视图名或None
47. **assert_view_exists**(name, exception_message='view[%s] does not exist')
>  - 描述：若视图不存在则抛出异常。
>  - 参数：
>     - name：视图名，`str`
>     - exception_message：异常消息，格式化为`name`
>  - Throws:	
>    `JenkinsException` whenever the view does not exist
48. **view_exists**(name)
>  - 描述：检查一个视图是否存在。
>  - 参数：
>     - name：视图名，`str`
>  - 返回：
>     - 若视图存在则返回`True`。
49. **get_views**()
>  - 描述：获取运行的视图列表。
>    每个视图都是带有‘name’和‘url’键的字典。
>  - 返回：
>     - 视图列表`[ { str: str} ]`
50. **delete_view**(name)
>  - 描述：永久删除某视图。
>  - 参数：
>    - name：视图名，`str`
51. **create_view**(name, config_xml)
>  - 描述：创建一个新视图。
>  - 参数：
>     - name：视图名，`str`
>     - config_xml：配置文件文本，`str`
52. **reconfig_view**(name, config_xml)
>  - 描述：改变一个已存在的视图的配置。
>    若要创建新视图，参考`Jenkins.create_view()`.
>  - 参数：
>     - name：视图名，`str`
>     - config_xml：新的XML配置，`str`
53. **get_view_config**(name)
>  - 描述：获取视图配置。
>  - 参数：
>     - name：视图名，`str`
>  - 返回：
>     - 视图配置 (XML 格式)
54. **get_promotion_name**(name, job_name)
>  - 描述：获取一个promotion名。
>    此方法可用来快速检测promotion是否存在，而不用给服务器造成太大压力。
>  - 参数：
>     - job_name：工程名，`str`
>     - name：Promotion名，`str`
>  - 返回值：
>     - promotion名或None
55. **assert_promotion_exists**(name, job_name, exception_message='promotion[%s] does not exist for job[%s]')
>  - 描述：若工程缺少某promotion则抛出异常
>  - 参数：
>     - job_name：工程名，`str`
>     - name：promotion名，`str`
>     - exception_message：异常消息。Formatted with `name` and `job_name`
>  - Throws:	
>    `JenkinsException` whenever the promotion does not exist on a job
56. **promotion_exists**(name, job_name)
>  - 描述：检查某工程是否包含指定的promotion
>  - 参数：
>     - job_name：工程名，`str`
>     - name：promotion名，`str`
>  - 返回：	
>     - 若promotion存在则返回`True`。
57. **get_promotions_info**(job_name, depth=0)
>  - 描述：获取某工程的某promotion信息字典
>  - 参数：
>     - name：工程名，`str`
>     - depth：JSON深度，`int`
>  - 返回：
>     - promotion信息字典，`dict`
58. **get_promotions**(job_name)
>  - 描述：获取运行的promotions列表。
>    每个promotion是个包含‘name’和‘url’键的字典。
>  - 参数：
>     - job_name：工程名，`str`
>  - 返回：
>     - promotions列表，`[ { str: str} ]`
59. **delete_promotion**(name, job_name)
>  - 描述：永久删除某promotion。
>  - 参数：
>     - job_name：工程名，`str`
>     - name：promotion名，`str`
60. **create_promotion**(name, job_name, config_xml)
>  - 描述：创建新的promotion
>  - 参数
>     - name：promotion名，`str`
>     - job_name：工程名，`str`
>     - config_xml：配置文件文本，`str`
61. **reconfig_promotion**(name, job_name, config_xml)
>  - 描述：更改promotion的配置。
>    若要创建promotion，请参考`Jenkins.create_promotion()`.
>  - 参数：
>     - name：promotion名，`str`
>     - job_name：工程名，`str`
>     - config_xml：新的XML配置，`str`
62. **get_promotion_config**(name, job_name)
>  - 描述：获取promotion配置。
>  - 参数：
>     - name：promotion名，`str`
>     - job_name：工程名，`str`
>  - 返回：
>     - promotion配置(XML 格式)
63. **quiet_down**()
>  - 描述：关闭Jenkins
>    新构建不被允许启动，运行中的构建优先于关闭服务器。
64. **wait_for_normal_op**(timeout)
>  - 描述：等待Jenkins进入正常操作模式。
>  - 参数：
>     - timeout：等待的秒数，`int`。注意，这与通过init设置的连接超时设置不同，因为它控制了套接字超时，相反的，这是等待状态返回的时间。
>  - 返回：
>     - 若Jenkins按时准备好，则返回`True`，否则返回`False`。
>  - 注意：将超时设置为小于配置的连接超时，可能会导致至少在返回之前的连接超时时间的等待。建议这里的超时应该至少与任何设置的连接超时时间一样长。