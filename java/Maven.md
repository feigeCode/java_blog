## 1、Maven

### 1.1、Maven项目架构管理工具

我们目前用来就是方便导入jar包

Maven的核心思想：约定大于配置

​	有约束，不要去违反

Maven会规定好我们该如何去写java代码

### 1.2、配置环境变量

M2_HOME:maven目录下的bin

MAVEN_HOME:maven目录

PATH:%MAVEN_HOM%\bin

测试：mvn -version

### 1.3、去conf/settings.xml

~~~xml
 <localRepository>E:\java\apache-maven-3.6.1\maven.repo</localRepository>
~~~

~~~xml
<mirror>
      <id>nexus-aliyun</id>  
      <mirrorOf>*,!jeecg,!jeecg-snapshots</mirrorOf>  
      <name>Nexus aliyun</name>  
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>  
</mirror>
~~~

### 1.4、手动更改maven->改为自己的

File -> Settings -> Build, Execution, Deployment -> Build Tools -> Maven
