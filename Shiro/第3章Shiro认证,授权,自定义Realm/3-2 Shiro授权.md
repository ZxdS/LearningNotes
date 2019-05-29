# Shiro授权

![](E:\gitbook\Shiro\第3章Shiro认证,授权,自定义Realm\shiro授权.jpg)

```
package com.imooc.test;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.SimpleAccountRealm;
import org.apache.shiro.subject.Subject;
import org.junit.Before;
import org.junit.Test;


/**
 * @author zxd
 * @title: AuthenticationTest
 * @projectName imooc-shiro
 * @description: shiro 认证测试
 * 创建SecurutyManager对象
 * -》主体提交认证
 * -》SecurityManager认证
 * -》Authenticator认证
 * -》realm验证
 * @date 2019/2/25 11:11
 */
public class AuthenticationTest {
    SimpleAccountRealm simpleAccountRealm = new SimpleAccountRealm();

    //在认证之前添加一个用户
    @Before
    public void addUser(){
        simpleAccountRealm.addAccount("Mark","123456","admin","user");
    }
    @Test
    public void testAuthentication(){
    //1.构建SecurityManager环境
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        defaultSecurityManager.setRealm(simpleAccountRealm);
    //2.主体提交认证请求
        //设置SecurityManager环境
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        //Subject不要引用错误，引用shiro的Subject
        Subject subject = SecurityUtils.getSubject();
    //3.认证数据
        UsernamePasswordToken token = new UsernamePasswordToken("Mark","123456");
        subject.login(token);

      //判断认证是否成功
        System.out.println("isAuthenticated:"+ subject.isAuthenticated());

        //退出登录
//        subject.logout();
//        System.out.println("isAuthenticated:"+ subject.isAuthenticated());

        //检查角色授权
        subject.checkRole("admin");
        subject.checkRoles("admin","user");
    }
}

```

