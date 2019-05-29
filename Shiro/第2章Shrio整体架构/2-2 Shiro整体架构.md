# Shiro整体架构

![](E:\gitbook\Shiro\第2章Shrio整体架构\shiro整体架构.jpg)

![](E:\gitbook\Shiro\第2章Shrio整体架构\shiro整体架构2.png)

Shiro的整体架构：

shiro通过Secunrity  Manager提供安全服务,Secunrity  Manager管理着其他组件的实例

1.Authenticator(认证器)：管理登录、登出。

2.Authorizer (授权器)：赋予主体权限。

3.Session Manager：session管理器

4.Session Dao：提供对session的操作，增删该查

5.Cache Manager：缓存管理器

6.realm:可以理解为shiro和数据源之间的桥梁(shiro使用的认证,权限,角色信息都是从这里获取的)

7.Cryptography：对数据进行加密