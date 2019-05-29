# Shiro集成Spring

Shiro集成Spring，步骤以及注意事项如下：

1. 创建Maven的Web项目，创建完毕之后，按照Maven的规范创建出相应的Java源码文件以及相应的test测试目录。并且将web-info下的文件删除。

2. 设置java文件夹以及resources文件夹分别为源码类型以及资源文件类型，具体可以点击文件夹右键，找到Mark Dirctory as选择。

3. 在resources中创建spring文件夹，并且创建spring.xml以及spring-mvc.xml文件。

4. 在web-info中创建web.xml文件。一般的idea不可创建出web.xml文件，所以此时按照网上给的方法，直接选择菜单中的File---Project Stru----Facts 第一个空白点击处，添加web.xml文件。点击确定即可。路径要写对，是在web-info下。此种方法如果不行，则需要手动写入web.xml文件。

   web.xml代码如下：

5. ```
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
            version="3.1">
   
   
   
       <!-- 指定Spring Bean的配置文件所在目录。默认配置在WEB-INF目录下 -->
       <context-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath*:spring/spring.xml</param-value>
       </context-param>
   
   
   
       <listener>
           <listener-class>
               org.springframework.web.context.ContextLoaderListener
           </listener-class>
       </listener>
   
       <listener>
           <listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
       </listener>
   
   
       <filter>
           <filter-name>shiroFilter</filter-name>
           <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
       </filter>
       <filter-mapping>
           <filter-name>shiroFilter</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
   
   
       <servlet>
           <servlet-name>dispatcherServlet</servlet-name>
           <servlet-class>
               org.springframework.web.servlet.DispatcherServlet
           </servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath*:spring/spring-mvc.xml</param-value>
           </init-param>
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>dispatcherServlet</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   
   
   </web-app>
   ```

6. 其次在Maven的pom.xml文件中引入相应的spring、springmvc 、shiro 、shiro-spring、shiro-web的jar包，注意必须版本保持匹配，否则启动容易报404错误，我就是因为这个问题。如下是pom.xml文件引入的包：

7. ```
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>4.2.4.RELEASE</version>
   </dependency>
   <!-- 2)SpringWeb Dependency -->
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>4.2.4.RELEASE</version>
   </dependency>
   <!-- 3)Shiro Dependency -->
   <dependency>
       <groupId>org.apache.shiro</groupId>
       <artifactId>shiro-core</artifactId>
       <version>1.4.0</version>
   </dependency>
   <!-- 4)ShiroWeb Dependency -->
   <dependency>
       <groupId>org.apache.shiro</groupId>
       <artifactId>shiro-web</artifactId>
       <version>1.4.0</version>
   </dependency>
   <!-- 5)ShiroSpring Dependency -->
   <dependency>
       <groupId>org.apache.shiro</groupId>
       <artifactId>shiro-spring</artifactId>
       <version>1.4.0</version>
   </dependency>
   ```

8. 在相应的resources资源文件下建立spring文件夹，在spring-mvc.xml文件中写入扫描文件以及驱动、过滤的标签，如下：

9. ```
   <context:component-scan base-package="com.imooc.controller"/>
   <mvc:annotation-driven />
   <mvc:resources mapping="/*" location="/"/>
   ```

10. 在spring.xml文件中，写入相应的自定义的Realm的bean标签、加密的Hashed的bean标签（注入到Realm中）、写入默认的web的权限管理的bean标签（注入Realm）、写入ShiroFilterFactoryBean的bean标签（将web的权限管理注入进去）即可。

    此外ShiroFilter的bean标签里面，还可以规定loginurl的值，即登录的页面，unauthor..登录失败没权限访问的页面，filterChainDefinitions过滤链，（过滤链里面可以以a=b的形式进行过滤，例：/login.html = anon 表示在访问login.html页面的时候不需要任何权限，anon表示无需权限，authc代表必须认证通过才能访问，一般链是从上往下匹配，只要匹配的相应结果，则直接返回。所以一般把/*=authc放在最后。）下面是源码：

11. ```
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
         <property name="securityManager" ref="securityManager"/>
        <property name="loginUrl" value="login.html"/>
        <property name="unauthorizedUrl" value="403.html"/>
        <property name="filterChainDefinitions">
            <value>
                /login.html = anon
                /subLogin = anon
                /* = authc
            </value>
        </property>
    
    </bean>
    <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <property name="realm" ref="customRealm"/>
    </bean>
    <bean id="customRealm" class="com.imooc.realm.CustomRealm">
         <property name="credentialsMatcher" ref="hashedCredentialsMatcher"/>
    </bean>
    
    <bean id="hashedCredentialsMatcher" class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
        <property name="hashAlgorithmName" value="md5"/>
        <property name="hashIterations" value="1"/>
    </bean>
    ```

12. 之后基本上就是写入controller以及html网页即可。之后启动tomcat自动访问，其中关于编码的问题需要在@RequestMapping中设置produces="application/json;charset=utf-8"的形式即可。