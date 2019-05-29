# 自定义Realm

自定义的Realm，主要注意以下几个步骤：

1. 首先继承并实现类AuthorizingRealm的方法。其中方法

   doGetAuthenticationInfo 主要做认证操作，即可以通过获取其中的

   用户名，查询出相应的密码，然后将用户名与密码一并返回，shiro会自动根据传入的用户名与密码与此Realm返回的用户名和密码作比对，返回你想要的结果。同理doGetAuthorizationInfo主要是用来做角色与权限的验证，也是通过用户名，从数据库中查找到相应的角色或者权限的数据并返回一个Simple的授权类，授权系统会根据传入的参数与返回的结果集对比，存在返回true不存在则抛异常。

2. 两者方法只能获取用户名称，通过用户名称连接数据库获取其他信息。这里只是做数据准备操作，并不做判断是否传入的值与其相符的操作，此操作在此之后进行。

3. 根据两者的返回对象分别返回Simple类型的对象。认证的对象需要将你获取的用户名和密码放到构造方法中。授权的对象需要你set到相应的方法中。

4. 例子中只是从本地写死静态类，之后必须写入相应的数据库连接。

   

CusromRealm.java

```
package com.imooc.shiro.realm;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

/**
 * @author zxd
 * @title: CustomRealm
 * @projectName imooc-shiro
 * @description: TODO
 * @date 2019/2/26 10:55
 */
public class CustomRealm extends AuthorizingRealm {
    Map<String,String> userMap =new HashMap<>(16);
    // 在静态代码块中给userMap 赋值
    {
        userMap.put("Mark","123456");

        super.setName("customRealm");
    }
    // doGetAuthorizationInfo这个方法用来做授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        String userName = (String) principalCollection.getPrimaryPrincipal();
        // 模拟从数据库或缓存中获取角色数据,权限数据
        Set<String> roles = getRolesByUserName(userName);
        Set<String> permissions = getPermissionsByUserName(userName);
        // 设置权限数据和角色数据
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        simpleAuthorizationInfo.setStringPermissions(permissions);
        simpleAuthorizationInfo.setRoles(roles);
        return simpleAuthorizationInfo;
    }

    private Set<String> getPermissionsByUserName(String userName) {
        Set<String> sets = new HashSet<>();
        sets.add("user:delete");
        sets.add("user:add");
        return sets;
    }

    private Set<String> getRolesByUserName(String userName) {
        Set<String> sets = new HashSet<>();
        sets.add("admin");
        sets.add("user");
        return sets;
    }

    // doGetAuthenticationInfo这个方法用来做认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
    // 1.从主体传过来的认证信息中，获得用户名   
    String userName = (String) authenticationToken.getPrincipal();
    
    // 2.通过用户名到数据库中获取凭证
    String password = getPasswordByUserName(userName);
    if (password == null){
        return null;
    }
    // 创建一个返回对象authenticationInfo
        SimpleAuthenticationInfo authenticationInfo = new SimpleAuthenticationInfo
                ("Mark",password,"customRealm");

        return authenticationInfo;
    }

    /**
     * @Author: zxd
     * @Description: 模拟数据库查询凭证
     * @Date: 2019/2/26 11:16
     * @Param:userName
     * @return:
     **/
    private String getPasswordByUserName(String userName) {
        return userMap.get(userName);
    }
}
```



CustomRealmTest.java

```
package com.imooc.test;

import com.imooc.shiro.realm.CustomRealm;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.subject.Subject;
import org.junit.Test;

/**
 * @author zxd
 * @title: CustomRealmTest
 * @projectName imooc-shiro
 * @description: TODO
 * @date 2019/2/26 11:32
 */
public class CustomRealmTest {
    @Test
    public void testAuthentication(){
        CustomRealm customRealm = new CustomRealm();


        //1.构建SecurityManager环境
        //获取安全管理者对象------DefaultSecurityManager默认管理者
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        defaultSecurityManager.setRealm(customRealm);
        //设置管理者的管理领域
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
       /* subject.checkRole("admin");
        //判断权限
        subject.checkPermission("user:delete");

        //登出
        subject.logout();
        //因为登出了，会返回false
        System.out.println("isAuthenticated:" + subject.isAuthenticated());*/

        subject.checkRole("admin");
        subject.checkPermissions("user:add","user:delete");
    }
}
```