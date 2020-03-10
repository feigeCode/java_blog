### 1、**Tomcat**

#### 	1.1、**Tomcat启动与配置**

​			shutdown.bat关闭

​			startup.bat启动

​			访问测试<localhost:8080>

#### 	1.2、**可能遇到的问题**

​		1、java环境没有配置

​		2、闪退问题：需要配置兼容性

​		3、乱码问题：配置文件设置

#### 	1.3、**服务器核心配置文件server.xml**

## 2、servlet

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/feige1")
public class ServletContextDemo1 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        //获取多个值，返回一个数组
        String[] hobbys = req.getParameterValues("hobbys");
        ServletContext context = this.getServletContext();
        //String username = "飞哥feige";
        context.setAttribute("username",username);
        context.setAttribute("password",password);
        context.setAttribute("hobbys",hobbys);
        //请求转发url不变307
        //重定向url改变302
        req.getRequestDispatcher("/feige2").forward(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}

```

```java
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Arrays;

@WebServlet("/feige2")
public class ServletContextDemo2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context = this.getServletContext();
        String username = (String) context.getAttribute("username");
        String password = (String) context.getAttribute("password");
        String[] hobbys = (String[]) context.getAttribute("hobbys");
        //resp.setContentType("text/html;charset=UTF-8");
        resp.setHeader("Content-Type","text/html;charset=UTF-8");
        resp.getWriter().println("username="+username);
        resp.getWriter().println("password="+password);
//        for (String hobby:hobbys) {
//            resp.getWriter().print("hobby="+hobby);
//        }
        resp.getWriter().println(Arrays.toString(hobbys));
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}

```



```jsp
<%--
  Created by IntelliJ IDEA.
  User: love。com
  Date: 2020/1/17
  Time: 22:08
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
    <h1>feige</h1>
    <form action="${pageContext.request.contextPath}/feige1" method="post">
        <input type="text" name="username">
        <input type="password" name="password">
        <input type="text" name="hobbys">
        <input type="text" name="hobbys">
        <input type="submit" value="提交">
    </form>
</body>
</html>

```



​			

