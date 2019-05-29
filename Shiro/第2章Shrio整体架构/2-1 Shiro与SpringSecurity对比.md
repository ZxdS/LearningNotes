# Shiro与SpringSecurity对比

Shiro安全框架：简单灵活、可脱离Spring 独立存在、粒度较粗

Spring Security ：复杂笨重、必须依托与Spring、粒度较细

由于权限管理模块一般都是在资源一层，如果更深一层的话，则可能会与业务代码进行耦合，故提倡Shiro做权限管理，Spring官网也是用Shiro进行的权限管理。