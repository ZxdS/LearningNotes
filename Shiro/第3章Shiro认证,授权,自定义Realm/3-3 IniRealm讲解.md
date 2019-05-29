# IniRealm讲解

Shiro的IniRealm 形式：此形式主要是将数据存放到相应的user.ini即文件系统中，通过给定的格式，从文件中查找相应的数据是否存在。

步骤：

1.删除之前SimpleAccountRealm，在实体中创建IniRealm并实例化。在构造方法中传入相应的user.ini地址。此文件地址一般写为：classpath:user.ini的形式。

  需要在相应的包中建立resource文件夹，并且放入user.ini文件。

2.user.ini文件的定义格式为：[users] 用户名=密码,角色 [roles] 角色=权限名（例子：amdin=user:delete,user:update）

3.与之前认证与授权步骤一样，直接将IniRealm放入到SecurityManager中，进行登录认证，之后进行checkRoles验证与checkPersimmon验证权限等操作即可。

```
package com.imooc.test;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;
import org.junit.Test;

/**
 * @title: IniRealmTest
 * @projectName imooc-shiro
 * @description: 通过ini配置文件来提供认证和授权信息
 * @author zxd
 * @date 2019/2/25 13:49
 */

public class IniRealmTest {
    @Test
    public void testAuthentication(){
        // 配置文件路径
        IniRealm iniRealm = new IniRealm("classpath:user.ini");

        //1.构建SecurityManager环境
        //获取安全管理者对象------DefaultSecurityManager默认管理者
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        //设置管理者的管理领域
        defaultSecurityManager.setRealm(iniRealm);
        //2.主体提交认证请求
        //SecurityUtils操作securityManager的工具类，提供了getSecurityManager和setSecurityManger,getSubject的方法
        //此处是给工具类默认管理者对象
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        //Subject不要引用错误，引用shiro的Subject
        Subject subject = SecurityUtils.getSubject();
        //3.认证数据
        //用户+密码的token令牌
        UsernamePasswordToken token = new UsernamePasswordToken("Mark","123456");
        //登入
        subject.login(token);

        //判断认证是否成功
        System.out.println("isAuthenticated:"+ subject.isAuthenticated());
        //判断角色
        subject.checkRole("admin");
        //判断权限
        subject.checkPermission("user:delete");

        //登出
        subject.logout();
        //因为登出了，会返回false
        System.out.println("isAuthenticated:" + subject.isAuthenticated());
    }
}
```