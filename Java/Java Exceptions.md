## 一、JavaWeb相关

### 1、**java.lang.IllegalArgumentException：**

 An invalid character [34] was present in the Cookie value

> Caused by: java.lang.IllegalArgumentException: An invalid character [34] was present in the Cookie value
> 	at org.apache.tomcat.util.http.Rfc6265CookieProcessor.validateCookieValue(Rfc6265CookieProcessor.java:182)
> 	at org.apache.tomcat.util.http.Rfc6265CookieProcessor.generateHeader(Rfc6265CookieProcessor.java:115)
> 	at org.apache.catalina.connector.Response.generateCookieString(Response.java:974)
> 	at org.apache.catalina.connector.Response.addCookie(Response.java:926)
> 	at org.apache.catalina.connector.ResponseFacade.addCookie(ResponseFacade.java:385)
> 	at cn.betterme.lsrs.web.servlet.StuServlet.login(StuServlet.java:69)

原因：在cookie中出现特殊字符”（双引号）了。

问题代码：

```java
//cookie内容
//{"password":"123456","university":"计算机学院",num":"1610411034",sex":"男",
//"name":"张三","className":"计算机科学与技术班"}
Cookie loginCookie = new Cookie("loginCookie", JSON.toJSONString(dbStu));
loginCookie.setMaxAge(COOKIE_EXPIRES);
response.addCookie(loginCookie);
```

**解决办法：**使用URLEncode()方法，将json以“utf-8”形式编码，在浏览器获取的时候再以utf-8形式解码。

代码：

```java
Cookie loginCookie = new Cookie("loginCookie", URLEncoder.encode(JSON.toJSONString(dbStu), "utf-8"));
```

浏览器看到的cookie：

> **Set-Cookie:** 
>
> loginCookie=%7B%22className%22%3A%22%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6%E4%B8%8E%E6%8A%80%E6%9C%AF%E7%8F%AD%22%2C%22name%22%3A%22%E5%BC%A0%E4%B8%89%22%2C%22num%22%3A%221610411034%22%2C%22password%22%3A%22123456%22%2C%22sex%22%3A%22%E7%94%B7%22%2C%22university%22%3A%22%E8%AE%A1%E7%AE%97%E6%9C%BA%E5%AD%A6%E9%99%A2%22%7D; Max-Age=300; Expires=Tue, 20-Nov-2018 12:43:43 GMT

## 二、Spring相关

### 1、**java.lang.IllegalStateException: Failed to load ApplicationContext**

> 十月 16, 2018 7:35:18 下午 org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
> 信息: Loading XML bean definitions from URL [file:/media/betterme/Myfiles/AppData/Projects/IdeaProjects/spring/target/classes/spring/applicationContext.xml]
> 十月 16, 2018 7:35:18 下午 org.springframework.test.context.TestContextManager prepareTestInstance
> 严重: Caught exception while allowing TestExecutionListener [org.springframework.test.context.support.DependencyInjectionTestExecutionListener@2d928643] to prepare test instance [cn.betterme.test.spring.service.UserServiceTest@4b4523f8]
> **java.lang.IllegalStateException: Failed to load ApplicationContext**
>
> ......省略若干行
>
> Caused by: org.springframework.beans.factory.xml.XmlBeanDefinitionStoreException: Line 40 in XML document from URL [file:/media/betterme/Myfiles/AppData/Projects/IdeaProjects/spring/target/classes/spring/applicationContext.xml] is invalid; nested exception is org.xml.sax.SAXParseException; lineNumber: 40; columnNumber: 71; **cvc-complex-type.2.4.c: 通配符的匹配很全面, 但无法找到元素 'tx:advice' 的声明。**

**解决：**

applicationContext.xml关于tx的约束文件引入不全

原本是这样的

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">
```

需要在`xsi:schemaLocation`后再添加这两个约束

```
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
```

