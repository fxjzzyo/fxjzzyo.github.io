---
published: true
layout: post
tags:
  - springMVC
categories: java-web
description: 本文章将介绍如何使用新浪云SAE搭建一个公网可访问的网页应用。
---
## 准备工作
开发工具：Eclipse
所需jar包：
![jar包](http://img.blog.csdn.net/20171020222443292?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
## 1. 新建Dynamic web project，工程名为SpringMVC
一路next，**注意最后一步勾选生成web.xml的勾选框**，然后finish。

![这里写图片描述](http://img.blog.csdn.net/20171020222908426?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

新生成的工程目录如下图：

![工程目录](http://img.blog.csdn.net/20171020223336158?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 2. 将准备好的jar包拷贝到WEB-INF/lib下
然后选中所有jar包，右键-->build path-->add to path。

![这里写图片描述](http://img.blog.csdn.net/20171020223722934?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 3. 新建并配置springmvc-sevlet.xml
在WEB-INF目录下新建名为springmvc-sevlet的xml文件，其内容如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:task="http://www.springframework.org/schema/task"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd 
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-4.2.xsd 
        http://www.springframework.org/schema/mvc 
        http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd 
        http://www.springframework.org/schema/task 
        http://www.springframework.org/schema/task/spring-task-4.2.xsd">

	<!-- 扫描路径 -->
	<context:component-scan base-package="com.fxj.controller">
		<context:include-filter type="annotation"
			expression="org.springframework.stereotype.Controller" />
	</context:component-scan>

	<!-- 配置根视图 -->
	<mvc:view-controller path="/" view-name="index" />

	<!-- 激活基于注解的配置 @RequestMapping, @ExceptionHandler,数据绑定 ,@NumberFormat , 
		@DateTimeFormat ,@Controller ,@Valid ,@RequestBody ,@ResponseBody等 -->
	<mvc:annotation-driven />

	<!-- 静态资源配置 -->
	<mvc:resources location="/assets/" mapping="/assets/**"></mvc:resources>

	<!-- 视图层配置 -->
	<bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/pages/" />
		<property name="suffix" value=".jsp" />
	</bean>

</beans>
```
**注意：**
扫描路径里的base-packge，要写在java src里的包名，我这里是com.fxj.controller

```
<context:component-scan base-package="com.fxj.controller">

```

## 4. 配置web.xml
web.xml是新建项目时自动生成的，位于WEB-INF目录下，将其内容配置为如下所示：

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	id="WebApp_ID" version="3.1">
  <display-name>SpringMVC</display-name>
	<!-- 统一编码 -->
	<filter>
		<filter-name>charsetEncoding</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>charsetEncoding</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<!-- 前端控制器 -->
	<servlet>
		<servlet-name>springmvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<!-- 加载/WEB-INF/[servlet-name]-servlet.xml -->
		<load-on-startup>1</load-on-startup>

	</servlet>
	<servlet-mapping>
		<servlet-name>springmvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<!-- <welcome-file-list>
		<welcome-file>index.html</welcome-file>
		<welcome-file>index.htm</welcome-file>
		<welcome-file>index.jsp</welcome-file>
		<welcome-file>default.html</welcome-file>
		<welcome-file>default.htm</welcome-file>
		<welcome-file>default.jsp</welcome-file>
	</welcome-file-list> -->
</web-app>
```
## 5. 建立视图页
在WEB-INF下新建文件夹pages，在其下新建index.jsp，内容如下：

```
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>hello</title>
</head>
<body>
<h1>hello Spring MVC</h1>
</body>
</html>
```

## 6. 建立Controller
在Java Resource下的src中新建包com.fxj.controller。
然后新建HelloController.class文件，其内容如下：

```
package com.fxj.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
@RequestMapping(value = "/hello")
public class HelloController {
	@RequestMapping(value="/world",method = RequestMethod.GET)
	public String hello(Model model){
		System.out.println("hello");
		model.addAttribute("msg","你好Spring MVC!");
		return "index";
		
	}
}
```
通过url示例就可直观明白上述controller的访问方法
要想访问hello函数，这里的url是:
http://localhost:8888/SpringMVC/hello/world.html
我这里的tomcat端口是8888，如果你的是8080就写8080。
在浏览器中打开链接，即可看见激动人心的hello world 啦！

![这里写图片描述](http://img.blog.csdn.net/20171020230852083?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果要往index.jsp中传数据该怎么做呢？下面的这行代码就是答案
```
model.addAttribute("msg","你好Spring MVC!");
```
它通过键值对的形式传递，要想在index.jsp中显示msg所指的值，只需需要显示的地方这样引用：
${msg}

如下图所示：

![这里写图片描述](http://img.blog.csdn.net/20171020231309108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

再次访问，结果如下：

![这里写图片描述](http://img.blog.csdn.net/20171020231354671?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

至此，一个SpringMVC最简实例hello world就已经完成了！
凡事开头难，只要懂了这个简单的hello world例子，其他业务需求也可照葫芦画瓢，触类旁通！

----------
### 接下来，在上面的基础是增加一个登录的简单示例 
#### 1. 在java source 下的com.fxj.controller包中新增LoginAction.class，内容如下：

```
package com.fxj.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class LoginAction {
	
	@RequestMapping("/login.do")
	public String login(String name,String pwd){
		System.out.println(name +" 登录成功");
		
		return "loginSuccess";
	}

}

```
#### 2. 修改index.jsp
在index.jsp中新增

```
<form action="login.do" method="post">
    username:<input type="text" name = "name" ><p> 
    password:<input type="password" name = "pwd" ><p>
    <input type="submit" value="登录"> 
</form>
```
如下图：

![这里写图片描述](http://img.blog.csdn.net/20171020232100769?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



####3. 在WEB-INF/pages/下新增loginSuccess.jsp,内容如下：

```
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>success</title>
</head>
<body>
<h1>Login Success !</h1>
</body>
</html>
```

再次运行，访问：http://localhost:8888/SpringMVC/
**默认启动index.jsp页，所以把登录表单放在index.jsp中，post提交的表单action=login.do，指向了controller中的login函数。**
运行结果：

![这里写图片描述](http://img.blog.csdn.net/20171020233148612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

点击登录

![这里写图片描述](http://img.blog.csdn.net/20171020233217346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

最后贴一下整个项目的工程目录：

![这里写图片描述](http://img.blog.csdn.net/20171020234059717?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
