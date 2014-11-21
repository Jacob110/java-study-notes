## `JavaEE` 项目中碰到的一些问题

---
### 问题描述
`Spring MVC` 中 `ModelAndView` 返回的 `Model` 在 JSP 页面无法显示 的问题
JSP 示例代码：

    <%@ page language="java" contentType="text/html; charset=UTF-8"
    	pageEncoding="UTF-8" session="true"%>
    <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <body>
    	<div class="container">
    		<h1>This is Speaker!</h1>
    		<p>${message}</p>
    	</div>
    </body>
    </html>
注意上面的 `${message}` 运行后，页面输出依然是 `${message}`,显然 `EL` 表达式没起作用
### 解决方案：
原因是 `JSP descriptor` 版本太低导致
### 1. 低版本 `JSP descriptor` 如 `JSP 1.2` 
比如 `web.xml`

    <!DOCTYPE web-app PUBLIC
     "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
     "http://java.sun.com/dtd/web-app_2_3.dtd" >
     
    <web-app>
    //...
    </web-app>
这个 `web.xml` 定义的 `Servlet` 规范是2.3，需要在JSP页面添加如下一行

    <%@ page isELIgnored="false"%>
手动使 EL 表达式在 JSP 页面生效
### 2. 直接修改 `web.xml` ，即修改 `schema` 支持 `JSP descriptor 2.0`
修改后的如下

    <web-app id="WebApp_ID" version="2.5" 
    	xmlns="http://java.sun.com/xml/ns/j2ee" 
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
    	http://java.sun.com/xml/ns/j2ee/web-app_2_5.xsd">
    //...
    </web-app>

---
如果按 2 所述，修改了 `web.xml schema` 为2.5后 依然不起作用，需要再按1 所述，在 JSP 页面手动添加 `<%@ page isELIgnored="false"%>`.
注：用 `Maven` 创建的 `web` 项目 自动生成的 `web.xml` 多数的 `Servlet` 版本都是 `2.3`，建议最好转成 `2.5` 以上，或者修改 `web.xml` 的 `schema`

### 类似的还有另外一个问题
Cannot change version of project facet Dynamic Web Module to 2.5?
这个问题一般是将项目转换成 `web` 项目时转错版本了，或者是 直接 copy 别人的项目 导致的。有可能会导致 项目在 Server 端无法运行。
### Solution
打开资源管理器项目所在文件夹--> .settings 
在 .settings 文件夹下 找到 `org.eclipse.wst.common.project.facet.core.xml` 修改其中的 `<installed facet="jst.web" version="2.5"/>` 即 `version` 改为你需要的版本，最后在 `eclipse` 项目中 `Maven -> Update Project`

参考至 http://stackoverflow.com/questions/18122336/cannot-change-version-of-project-facet-dynamic-web-module-to-3-0






