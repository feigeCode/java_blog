# [IDEA启动异常或者IDEA配置Tomcat插件异常](https://www.cnblogs.com/blogslee/p/9967492.html)

### 问题描述：

1.配置信息：Run Configuration Error: Broken configuration due to unavailable plugin or invalid configuration data.

![img](https://img2018.cnblogs.com/blog/1128526/201811/1128526-20181116093349704-382998954.png)

2.idea运行异常信息：

Error running 'TDM': Unknown run configuration type #com.intellij.j2ee.web.tomcat.TomcatRunConfigurationFactory![img](https://img2018.cnblogs.com/blog/1128526/201811/1128526-20181116093430863-206537662.png)

### 解决方案：

1.检查IDEA插件是否下载

2.在IDEA--PLUGIN中检查tomcat and tomee Integration是否勾选（必须勾选，才能实现idea以tomcat方式配置和启动）

![img](https://img2018.cnblogs.com/blog/1128526/201811/1128526-20181116093602918-152336446.png)

点击enable

 最后一步：重启IDEA即可