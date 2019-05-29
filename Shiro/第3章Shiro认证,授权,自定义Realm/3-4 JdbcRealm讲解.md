# JdbcRealm讲解



```
package com.imooc.test;

import com.alibaba.druid.pool.DruidDataSource;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.jdbc.JdbcRealm;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;
import org.junit.Test;

/**
 * @author zxd
 * @title: JdbcRealmTest
 * @projectName imooc-shiro
 * @description:
 * JdbcRealm的方式访问数据库，通过与数据库的连接，验证相应的登录用户与授权。
 *jdbcRealm新建其实例，并初始化。
 *Maven中引入mysql的驱动以及相应数据源的类。
 *新建数据源作为成员变量，并且在静态快中初始化url、username、password等。
 *将数据源设置到到jdbcrealm中，并且将此realm设置到权限管理中。
 *之后进行登录等验证，jdbcrealm会在源码的地方写入了默认的进行认证与授权的sql语句，以及表名什么的都规定好了。如果想更改，则写入新的sql语句，并且调用jdbcrealm中的设置认证Query、设置权限Query、设置角色查询的方法，进行修改。
 *当查询权限的时候，会报错，因为你需要在jdbcrealm中开启检查persimisson的开关为true即可。
 * @date 2019/2/25 15:35
 */
public class JdbcRealmTest {
    // 创建数据源
    DruidDataSource dataSource = new DruidDataSource();
    // 在静态快中初始化url、username、password
    {
         dataSource.setUrl("jdbc:mysql://localhost:3306/test");
         dataSource.setUsername("root");
         dataSource.setPassword("123456");
    }
    @Test
    public void testAuthentication(){

        JdbcRealm jdbcRealm = new JdbcRealm();
        jdbcRealm.setDataSource(dataSource);
        // jdbcRealm检查权限的开关默认为false，要设置为true
        jdbcRealm.setPermissionsLookupEnabled(true);
        // 自定义查询sql
        String sql ="select password from test_user where username = ?";
        //使用自定义的sql语句查询
        jdbcRealm.setAuthenticationQuery(sql);

        // 查询角色sql
        String roleSql ="select role_name from test_user_role where user_name =?";
        // 覆盖jdbcRealm原有的查询用户角色的sql
        jdbcRealm.setUserRolesQuery(roleSql);

        // 查询权限sql
        String permissionSql ="select permission from test_role_permissions where role_name = ?";
        // 覆盖jdbcRealm原有的查询角色权限的sql
        jdbcRealm.setPermissionsQuery(permissionSql);



        //1.构建SecurityManager环境
        //获取安全管理者对象------DefaultSecurityManager默认管理者
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        //设置管理者的管理领域
        defaultSecurityManager.setRealm(jdbcRealm);
        //2.主体提交认证请求
        //SecurityUtils操作securityManager的工具类，提供了getSecurityManager和setSecurityManger,getSubject的方法
        //此处是给工具类默认管理者对象
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        //Subject不要引用错误，引用shiro的Subject
        Subject subject = SecurityUtils.getSubject();
        //3.认证数据
        //用户+密码的token令牌
        UsernamePasswordToken token = new UsernamePasswordToken("xiaoming","654321");
        //登入
        subject.login(token);

        //判断认证是否成功
        System.out.println("isAuthenticated:"+ subject.isAuthenticated());

        //判断角色
        subject.checkRole("admin");
        //判断权限
        subject.checkPermission("admin:delete");
    }
}
```