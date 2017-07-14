---
title: java错误解决记录(持续更新)
tags: 
     - java
     - 错误解决记录
---
- dom4j读取文件 出现：  不能解析x字节的utf-8
- struts2框架，date类型请求参数自动类型转换错误
- org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'sessionFactory' is defined
- 使用beanuitls组件，注册事件转换器报错:空指针

一:dom4j读取文件 出现：  不能解析x字节的utf-8
---
当你解析的xml格式出现这个错误可能就是在生成xml时没有保存为utf-8的字符编码格式.
中文版的window下Java的默认的编码为GBK，也就是所虽然我们标识了要将xml保存为utf-8格式但实际上文件是以GBK格式来保存的.

所以我们读取时，手动解码:

	new BufferedReader(new InputStreamReader(new FileInputStream(path),"utf-8"))；
                               
写入时，我们不能用字符流,改为使用字节流写入:

	new FileOutputStream(path), format)；

二： struts2框架，date类型请求参数自动类型转换错误
---
struts2框架对于date时间类型默认支持类型: yyyy-MM-dd、yyyy年MM月dd日。**但是这个支持的格式是浏览器编码为zh-cn，如果编码为en-us，就会出现类型转换错误。**通过源码可以发现，struts2会根据不同编码决定来使用不同的时间转换格式。

三:org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'sessionFactory' is defined
---
原因来自于:

	<filter>
	        <filter-name>OpenSessionInViewFilter</filter-name>
	        <filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>
	</filter>
	
	<filter-mapping>
		<filter-name>OpenSessionInViewFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

这个是解决hibernate懒加载问题的配置，此配置默认加载名为**sessionFactory** 的Bean对象，如果

	 <bean id="sessionFactory" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
 
此id名字不一致，就会报错。（整合hibernate配置文件后，会出现问题，之前不会）
如果不想改名字，可配置:

	<filter>  
	    <filter-name>HibernateOpenSession</filter-name>   
	    <filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>  
	    <init-param>  
	        <param-name>sessionFactoryBeanName</param-name>  
	        <param-value>mySessionFactory</param-value>  此处的值和bean名字一致就可解决
	    </init-param>  
	</filter>  
	<filter-mapping>  
	    <filter-name>HibernateOpenSession</filter-name>  
	    <url-pattern>/*</url-pattern>  
	</filter-mapping>

四:使用beanuitls组件，注册事件转换器报错:空指针
---
 原因:导错包，应该导
 
	 import org.apache.commons.beanutils.ConvertUtils;
	 import org.apache.commons.beanutils.locale.converters.DateLocaleConverter;
 
 错的包和此包非常相似。
 