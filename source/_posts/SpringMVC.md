---
title: Mybatis知识点
date: 2021-05-04 17:14:00
author: yangjing
summary: SpringMVC学习记录
categories: SSM
tags: 
  - SpringMVC
---



# SpringMVC

## 一、导入依赖

```java
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.1</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.1</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
</dependencies>
```

## 二、springMVC执行原理![执行原理](C:\Users\yang\Pictures\springMVC\执行原理.png)

### 1、实现过程web.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--配置DisPacherServlet,springMVC的核心，q请求分发器，前端控制器-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!--    绑定spring-mvc配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
<!--        启动级别为1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

<!--
/:匹配所有请求，不包括jsp
/*:包括jsp
-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

### 2、springmvc-servlet.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


<!--    处理器映射器-->
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"></bean>
<!--    处理器适配器-->
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"></bean>

    <!--    处理器解析器 模板引擎thymeleaf -->
<bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
    <bean id="/hello" class="com.yang.controller.HelloController"/>


</beans>
```

### 3、实现controller

### ![image-20210406184638661](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210406184638661.png)



## 三、注解使用

#### 1、springmvc-servlet.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">


    <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
    <context:component-scan base-package="com.yang.controller"/>
    <!-- 让Spring MVC不处理静态资源  .css ,.html,.mp3 .... -->
    <mvc:default-servlet-handler />
    <!--    代替原有的适配器 映射器-->
    <mvc:annotation-driven />
    <!-- 视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!-- 前缀 -->
        <property name="prefix" value="/WEB-INF/jsp/" />
        <!-- 后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>

</beans>
```

#### 2、controller

```java
@Controller
public class HelloController {
    @RequestMapping("/hello")
    public String Hello(Model model){
        model.addAttribute("msg","hello");
        return "hello";
    }


}
```

## 四、转发和重定向

```java
package com.yang.controller;

import org.springframework.http.HttpRequest;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@Controller
public class HelloController {
    @RequestMapping("/hello")
    public String Hello(Model model ){
        model.addAttribute("msg","hello");
        return "WEB-INF/jsp/hello.jsp";
    }
    @RequestMapping("/hello1")
    public String Hello1(Model model ){
        model.addAttribute("msg","hello");
        return "forward:WEB-INF/jsp/hello.jsp";
    }

    @RequestMapping("/index")
    public String Getssesion(HttpServletRequest res, HttpServletResponse resp){
        HttpSession session=res.getSession();
//        System.out.println(session);
        return "redirect:/index.jsp";
    }


}
```

## 五、解决乱码

### 1、过滤器

```
package com.yang.controller.filter;
import javax.servlet.*;

import java.io.IOException;

public class EncodingFilter implements Filter {

    public void init(FilterConfig filterConfig) throws ServletException {
        
    }

    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            request.setCharacterEncoding("utf-8");
            response.setCharacterEncoding("utf-8");
            chain.doFilter(request,response);
    }
    public void destroy() {
    
    }
}
```

### 2、web.xml

```
<!--    配置过滤器-->
    <filter>
        <filter-name>encodding</filter-name>
        <filter-class>com.yang.filter.EncodingFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>encodding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

### 3、spring自带过滤器web.xml

<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

## 六、jackson乱码

### spring.xml

```java
<mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

### 时间工具类

```
 @RequestMapping( "/j2")
    @ResponseBody//加了这个就不会走视图解析器,之间吧return返回到前端。
    public String test2() throws JsonProcessingException {
        //jackson,ObjectMapper
        Date date=new Date();
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        ObjectMapper mapper = new ObjectMapper();
        String str=mapper.writeValueAsString(sdf.format(date));
        return str;
    }
```

## 七、fastJson

```java
@ResponseBody
@RequestMapping("/k4")
public String Getfsjaon(){
    User user = new User("赵本山","男",15);
    System.out.println("java 对象转json 字符串！");
    String str=JSON.toJSONString(user);
    System.out.println(str);
    System.out.println("json字符串转java对象");
    user=JSON.parseObject(str,User.class);
    System.out.println(user);
    System.out.println("java 转json对象");
    JSONObject js = (JSONObject) JSON.toJSON(user);
    System.out.println(js);
    System.out.println("json对象 转java");
    user=JSON.toJavaObject(js,User.class);
    System.out.println(user);
    return null;
}
```

![image-20210407203354353](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210407203354353.png)

## 八、ssm依赖

```
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.22</version>
        </dependency>
<!--        连接池-->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.1</version>
        </dependency>
<!--        mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.6</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>
<!--        spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.1</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.1</version>
        </dependency>
           <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>
```

### 1.db.properties

```
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=GMT
jdbc.username=root
jdbc.password=123456
```

### 2.mybaties-config.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>

    <mappers>
        <mapper class="com.yang.dao.BookMapper"/>
    </mappers>

</configuration>
```

### 3.spring-dao.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>

    <mappers>
        <mapper class="com.yang.dao.BookMapper"/>
    </mappers>

</configuration>
```

### 4.spring-service.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>

    <mappers>
        <mapper class="com.yang.dao.BookMapper"/>
    </mappers>

</configuration>
```

### 5.spring-mvc.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>

    <mappers>
        <mapper class="com.yang.dao.BookMapper"/>
    </mappers>

</configuration>
```

### 6.applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <import resource="spring-dao.xml"/>
    <import resource="spring-service.xml"/>
    <import resource="spring-mvc.xml"/>
</beans>
```

## 九、springMVC-拦截器

1. 从拦截级别上来说，SpringMVC是方法级别得拦截，而struts2是类级别的拦截。

2. 2、数据独立性：SpringMVC方法间独立，独享request和response
3. 4、拦截机制：SpringMVC 用的是独立的aop方式，struts2 有自己的interceptor机制

### 1. 配置：spring-servlet.xml

```
<!--    配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
<!--            /**所有请求-->
            <mvc:mapping path="/**"/>
            <bean class="com.yang.config.MyInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>

    <mvc:interceptors>
        <mvc:interceptor>
            <!--            /**所有请求-->
            <mvc:mapping path="/user/**"/>
            <bean class="com.yang.config.LoginInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```



### 2. logininterceptor.class

```
package com.yang.config;

import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

public class LoginInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        if(session.getAttribute("name")!=null){
            return true;
        }
        if(request.getRequestURI().contains("login1"))
            return true;
        if(request.getRequestURI().contains("books"))
            return true;
//        跳转去登录
        request.getRequestDispatcher("/index.jsp").forward(request,response);
    return false;
    }
}
```

## 十、文件上传下载

### 1. 添加依赖

```
    <dependencies>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
<!--       导入高版本api-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
        </dependency>

    </dependencies>
```

### 2.前端演示页面

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>文件上传</title>
  </head>
  <body>
    <form method="post" action="/upload" enctype="multipart/form-data">
      <input type="file" name="file">
      <input type="submit" value="upload">
    </form>

  <a download="背景图片" href="${pageContext.request.contextPath}/statics/bck.jpg">下载图片</a>
  </body>
</html>
```

### 3.上传的两种方式

```java
package com.yang.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.commons.CommonsMultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;

@Controller
public class FileController {
    //采用流的方式上传文件
    @RequestMapping("/upload")
    @ResponseBody
    public String upload(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {
        //获得文件名
        String filename = file.getOriginalFilename();
        if ("".equals(filename)){
            return "文件不存在";
        }
        //上传文件保存路径
        String path = request.getServletContext().getRealPath("/upload");
        File realPath = new File(path);
        if (!realPath.exists()){
            realPath.mkdir();
        }
        //文件上传
        InputStream inputStream = file.getInputStream();
        FileOutputStream outputStream = new FileOutputStream(new File(realPath, filename));
        int len = 0;
        byte[] bytes = new byte[1024];
        while ((len=inputStream.read(bytes))!=-1){
            outputStream.write(bytes,0,len);
            outputStream.flush();
        }
        //关闭流
        outputStream.close();
        inputStream.close();
        return "上传完毕";
    }

    //采用file.Transto上传文件
    @RequestMapping("/upload2")
    @ResponseBody
    public String upload2(@RequestParam("file") CommonsMultipartFile file,HttpServletRequest request) throws IOException {
        //上传文件保存路径
        String path = request.getServletContext().getRealPath("/upload");
        File realPath = new File(path);
        if (!realPath.exists()){
            realPath.mkdir();
        }
        //transferTo将文件写入磁盘，参数传入一个文件
        file.transferTo(new File(realPath+"/"+file.getOriginalFilename()));
        return "上传完毕";
    }
}
```

### 3.下载

```java
package com.yang.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.*;
@Controller
public class DownloadController {
    @RequestMapping("/download2")
    public String download(HttpServletRequest request, HttpServletResponse response) {
        String fileName = "bck.jpg";
        System.out.println(fileName);
        response.setContentType("text/html;charset=utf-8");
        try {
            request.setCharacterEncoding("UTF-8");//设定请求字符编码
        } catch (UnsupportedEncodingException e1) {
            e1.printStackTrace();
        }
        BufferedInputStream bis = null;//创建输入输出流
        BufferedOutputStream bos = null;
        String realPath = request.getSession().getServletContext().getRealPath("/") + "upload/";//获取文件真实路径
        System.out.println(realPath);
        String downLoadPath = realPath + fileName;
        System.out.println(downLoadPath);
        try {
            long fileLength = new File(downLoadPath).length();//获取文件长度
            //设置响应头信息；【固定的不用记，保存即可】
            response.setContentType("application/x-msdownload;");
            response.setHeader("Content-disposition", "attachment; filename=" + new String(fileName.getBytes("utf-8"), "ISO8859-1"));
            response.setHeader("Content-Length", String.valueOf(fileLength));
            bis = new BufferedInputStream(new FileInputStream(downLoadPath));//创建输入输出流实例
            bos = new BufferedOutputStream(response.getOutputStream());
            byte[] buff = new byte[2048];//创建字节缓冲大小
            int bytesRead;
            while (-1 != (bytesRead = bis.read(buff, 0, buff.length))) {
                bos.write(buff, 0, bytesRead);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (bis != null)
                try {
                    bis.close();//关闭输入流
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            if (bos != null)
                try {
                    bos.close();//关闭输出流
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
        }
        return "下载成功！";
    }
}
```