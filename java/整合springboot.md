# 1、Springboot整合Mybatis

## 1.1、配置数据库数据源DataSource

**新建一个application.yaml文件**

~~~yaml
spring:
  datasource:
    username: root
    password: hufei169357
    url: jdbc:mysql://localhost:3306/myblog?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
~~~

**mybatis整合druid的依赖**

~~~xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.12</version>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
~~~

~~~yaml
#整合druid
spring:
  datasource:
    username: root
    password: hufei169357
    url: jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址： https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
~~~



## 1.2、测试一下，看能不能连接到数据库

~~~java
package com.feige;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.sql.DataSource;
import java.sql.SQLException;

@SpringBootTest
class BlogApplicationTests {
    @Autowired
    DataSource dataSource;

    @Test
    void contextLoads() {
        System.out.println(dataSource.getClass());
        System.out.println("***********************");
        try {
            System.out.println(dataSource.getConnection());
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

}

~~~

## 1.3、创建实体类

~~~java
package com.feige.pojo;

public class User {
    private Integer id;
    private String username;
    private String email;
    private String password;
    private Integer sex;
    private String time;

    public User() {
    }

    public User(Integer id, String username, String email, String password, Integer sex, String time) {
        this.id = id;
        this.username = username;
        this.email = email;
        this.password = password;
        this.sex = sex;
        this.time = time;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Integer getSex() {
        return sex;
    }

    public void setSex(Integer sex) {
        this.sex = sex;
    }

    public String getTime() {
        return time;
    }

    public void setTime(String time) {
        this.time = time;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", email='" + email + '\'' +
                ", password='" + password + '\'' +
                ", sex=" + sex +
                ", time='" + time + '\'' +
                '}';
    }
}
~~~

## 1.4、配置mapper接口类

~~~java
package com.feige.dao;

import com.feige.pojo.User;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Map;

//@Mapper : 表示本类是一个 MyBatis 的 Mapper，等价于以前 Spring 整合 MyBatis 时的 Mapper 接口
@Mapper
@Repository
public interface UserMapper {
    //增删改查
    int addUser(User user);
    int deleteUser(String username);
    int updateUser(Map map);
    List<User> getUsers(Map map);
    User getUser(String username);
    int getCount();
    User login(Map map);
}



~~~

## 1.5、Mapper映射文件

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.feige.dao.UserMapper">
    <insert id="addUser" parameterType="User">
        insert into user values (#{id},#{username},#{email},#{password},#{sex},#{time})
    </insert>
    <delete id="deleteUser" parameterType="String">
        delete from user where username=#{username}
    </delete>
    <update id="updateUser" parameterType="map">
        update user set username=#{username},email=#{email},password=#{password},sex=#{sex},time=#{time} where id=#{id}
    </update>
    <select id="getUsers" resultType="User" parameterType="map">
        select * from user limit #{page},#{count}
    </select>
    <select id="getUser" parameterType="String" resultType="User">
        select * from user where username=#{username}
    </select>
    <select id="getCount" resultType="int">
        select count(*) from user
    </select>
    <select id="login" parameterType="map" resultType="User">
        select * from user where username=#{username} and password=#{password}
    </select>
</mapper>
~~~

## 1.6.SpringBoot 整合！

以前 MyBatis 未与 spring 整合时，配置数据源、事务、连接数据库的账号、密码等都是在 myBatis 核心配置文件中进行的
myBatis 与 spring 整合后，配置数据源、事务、连接数据库的账号、密码等就交由 spring 管理。因此，在这里我们即使不使用mybatis配置文件也完全ok！
**既然已经提供了 myBatis 的映射配置文件，自然要告诉 spring boot 这些文件的位置**

~~~yaml
#指定myBatis的核心配置文件与Mapper映射文件
mybatis:
  mapper-locations: classpath:mapper/*.xml
  # 注意：对应实体类的路径
  type-aliases-package: com.feige.pojo
~~~

## 1.7、项目结构

![image-20200217000642793](.\整合springboot.assets\image-20200217000642793.png)

# 2、SpringSecurity

安全框架，和它功能相同的还有shiro

官网：https://spring.io/projects/spring-security

新建一个SecurityConfig类

~~~java
package com.feige.config;


import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
//WebSecurityConfigurerAdapter自定义security策略
//AuthenticationManagerBuilder自定义认证策略
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    //连式编程
    //授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //首页所有人可以访问
        http.authorizeRequests()
                .antMatchers("index").permitAll()
                .antMatchers("/index1").hasRole("feige")
                .antMatchers("/index2").hasRole("feige1")
        .antMatchers("/index3").hasRole("feige2");
        //没有权限会默认到登录页面，需要开启登录页面
        http.formLogin().loginPage("/toLogin");//自定义登录页面
        //csrf 关闭
        http.csrf().disable();
        //注销后返回的页面
        http.logout();//.logoutSuccessUrl("/");登出返回的页面
        //记住我功能
        http.rememberMe();
    }
    //认证
    //passwordEncoder密码加密方法
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //数据库中拿到数据
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("feige").password(new BCryptPasswordEncoder().encode("123456")).roles("feige")
                .and()
                .withUser("feige1").password(new BCryptPasswordEncoder().encode("123456")).roles("feige1");
    }
}

~~~

# 3、shiro

## 3.1、导入shiro的依赖

~~~xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.3.2</version>
</dependency>
<!--shiro-thymeleaf整合-->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
~~~

## 3.2、新建一个UserRealm.java

~~~java
package com.feige.config;

import com.feige.pojo.User;
import com.feige.service.UserService;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.springframework.beans.factory.annotation.Autowired;


public class UserRealm extends AuthorizingRealm {
    @Autowired
    UserService userService;
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了doGetAuthorizationInfo方法");
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //从数据库中获取某个用户的权限
        Subject subject = SecurityUtils.getSubject();
        User user = (User) subject.getPrincipal();
        String role;
        if("胡飞".equals(user.getUsername())){
            role = "user:add";
        }else {
            role = "user:update";
        }
        //给用户授权
        info.addStringPermission(role);
        //info.addStringPermission("user:add");
        return info;
    }
    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了doGetAuthenticationInfo方法");
        //用户名，密码~ 数据中取
        UsernamePasswordToken usernamePassword = (UsernamePasswordToken)authenticationToken;
        User user = userService.getUser(usernamePassword.getUsername());
        Subject subject = SecurityUtils.getSubject();
        Session session = subject.getSession();
        session.setAttribute("loginUser",user.getUsername());
        if(!usernamePassword.getUsername().equals(user.getUsername())){
            return null;//抛出异常
        }
        //密码认证，shiro做
        //可以加密md5
        return new SimpleAuthenticationInfo(user,user.getPassword(),"");
    }
}

~~~

## 3.3、新建一个ShiroConfig.java

~~~java
package com.feige.config;

import at.pollux.thymeleaf.shiro.dialect.ShiroDialect;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.LinkedHashMap;

@Configuration
public class ShiroConfig {


    //ShiroFilterFactoryBean
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);
        /*
        添加shiro的内置过滤器
        anon:无需认证就可以访问
        authc:必须认证才可以访问
        user:记住我功能才能用
        perms：拥有对某个资源的权限才可以访问perms[用户:权限]
        role：拥有某个角色权限才可以访问

         */
        LinkedHashMap<String, String> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("/add","perms[user:add]");
        linkedHashMap.put("/update","authc");
        //支持通配符linkedHashMap.put("/*","authc");
        bean.setFilterChainDefinitionMap(linkedHashMap);
        //设置登录请求
        bean.setLoginUrl("/toLogin");
        //未授权请求
        bean.setUnauthorizedUrl("/noauth");
        return bean;
    }
    //DefaultWebSecurityManager
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //关联UserRealm
        securityManager.setRealm(userRealm);
        return securityManager;
    }

    //创建realm对象，需要自定义
    @Bean//被spring托管
    public UserRealm userRealm(){
        return new UserRealm();
    }
    //整合ShiroDialect:用来整合shiro-thymeleaf
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }
}

~~~

## 3.4、controller类

~~~java
package com.feige.Controller;


import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.session.Session;
import org.apache.shiro.subject.Subject;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class MyController {
    @RequestMapping("/index")
    public String index(){
        return "index/index";
    }
    @RequestMapping("/add")
    public String add(){
        return "index/add";
    }
    @RequestMapping("/update")
    public String update(){
        return "index/update";
    }
    @RequestMapping("/toLogin")
    public String toLogin(){
        return "index/login";
    }
    @RequestMapping("/login")
    public String login(String username, String password, Model model){
        //获取当前用户
        Subject subject = SecurityUtils.getSubject();
        //封装用户的登录数据
        UsernamePasswordToken token = new UsernamePasswordToken(username,password);

        try {
            subject.login(token);//执行登录方法
            return "index/index";
        }catch (UnknownAccountException e){
            model.addAttribute("msg","用户名不存在");
            return "index/login";
        }catch (IncorrectCredentialsException e){
            model.addAttribute("msg","密码错误");
            return "index/login";
        }


    }
    @GetMapping("/noauth")
    public String nonuth(){
        return "index/auth";
    }
}

~~~

## 3.5、前端

~~~java
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"
xmlns:shiro="http://www.thymeleaf.org/thymeleaf-extras-shiro">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<p th:if="${session.loginUser==null}"><a th:href="@{/toLogin}">登录</a></p>
<div shiro:hasPermission="user:add">
    <a th:href="@{/add}">add</a>
</div>
<br>
<div shiro:hasPermission="user:update">
    <a th:href="@{/update}">update</a>
</div>
</body>
</html>
~~~

