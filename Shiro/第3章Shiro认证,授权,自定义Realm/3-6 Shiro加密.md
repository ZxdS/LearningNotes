# Shiro加密

1. 通过HashedCredentialsMaster的方式加密，创建对象，并设置加密次数以及加密名称，将此加密对象设置到Realm的setCredentialsMaster的方法中，即该Realm采用此密码加密的方式。

2. 后台的密码采用MD5的Hash码值来存储。如果想将密码转成MD5Hash码可以采用MD5Hash对象，在构造方法中写入密码，

   打印输出其对象的toString方法即时期hash码值。

3. 加盐，实际为在密码中加入相应的其他名称拼接在一起，组成一个新的密码。如果想存储加盐之后的Hash码，仍旧采用MD5Hash对象，在构造方法的第二个参数中加入盐的名称，同样打印toString方法即可显示出来。其次在自定义的Realm认证方法中，返回Simple之前，设置Simple的加盐名称，即采用setCredentailSalt方法写入盐的名称即可。

4. 以上两种加密方式，对Realm改动较小，除非加盐才改动。

5. 在自定义的Realm加盐的时候，需要将字符值转成相应的ByteSource,则需要用ByteSource.Util.bytes(字符)即可，将此值设置到Simple的返回值中。

CusromRealm.java

```
package com.imooc.shiro.realm;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.crypto.hash.Md5Hash;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;

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
       // 在这里把密码换成加盐后的密文 userMap.put("Mark","283538989cef48f3d7d8a1c1bdf2008f");

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
    //加盐，提升密码密文安全性
        authenticationInfo.setCredentialsSalt(ByteSource.Util.bytes("Mark"));
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

    public static void main(String[] args){
        Md5Hash md5Hash = new Md5Hash("123456","Mark");
        System.out.println(md5Hash);
    }
}
```



CusromRealmTest.java

```
package com.imooc.test;

import com.imooc.shiro.realm.CustomRealm;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
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

        HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
        // 设置加密名称
        matcher.setHashAlgorithmName("md5");
        // 设置加密次数
        matcher.setHashIterations(1);
        customRealm.setCredentialsMatcher(matcher);

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