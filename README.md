# 单点登录

标签（空格分隔）：登陆

---
### 基本概念
单点登录（Single Sign On），简称为 SSO，是目前比较流行的企业业务整合的解决方案之一。SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。
SSO 实现模式一般包括以下三个原则：
1、所有的认证登录都在 SSO 认证中心进行；
2、SSO 认证中心通过一些方法来告诉 Web应用当前访问用户究竟是不是已通过认证的用户；
3、SSO 认证中心和所有的 Web 应用建立一种信任关系，也就是说 web应用必须信任认证中心。（单点信任）

### SSO 主要实现方式
1、   共享 cookies

基于共享同域的 cookie 是 Web 刚开始阶段时使用的一种方式，它利用浏览同域名之间自动传递 cookies 机制，实现两个域名之间系统令牌传递问题；另外，关于跨域问题，虽然 cookies本身不跨域，但可以利用它实现跨域的 SSO 。如：代理、暴露 SSO 令牌值等。

缺点：不灵活而且有不少安全隐患，已经被抛弃。

2、   Broker-based( 基于经纪人 )

这种技术的特点就是，有一个集中的认证和用户帐号管理的服务器。经纪人给被用于进一步请求的电子身份存取。中央数据库的使用减少了管理的代价，并为认证提供一个公共和独立的 “第三方 ” 。例如 Kerberos 、 Sesame 、 IBM KryptoKnight （凭证库思想 ) 等。 Kerberos是由麻省理工大学发明的安全认证服务，已经被 UNIX 和 Windows 作为默认的安全认证服务集成进操作系统。

3、   Agent-based （基于代理人）

在这种解决方案中，有一个自动地为不同的应用程序认证用户身份的代理程序。这个代理程序需要设计有不同的功能。比如，它可以使用口令表或加密密钥来自动地将认证的负担从用户移开。代理人被放在服务器上面，在服务器的认证系统和客户端认证方法之间充当一个 ” 翻译 “。例如 SSH 等。

4、   Token-based

例如 SecureID,WebID ，现在被广泛使用的口令认证，比如 FTP 、邮件服务器的登录认证，这是一种简单易用的方式，实现一个口令在多种应用当中使用。

5、   基于网关

6、   基于 SAML

SAML(Security Assertion Markup Language ，安全断言标记语言）的出现大大简化了 SSO ，并被 OASIS 批准为 SSO 的执行标准 。开源组织 OpenSAML 实现了 SAML 规范。

### 我的一些想法
1、简单的SSO系统
![流程图][1]
原理是根据同一用户请求同一网站，在会话未失效的情况下，SessionId是不变的。
这里无论是否跨域都可以实现，但是问题是：

+ 如何会话同步
当用户登录子系统1后不再请求SSO系统，这样会造成SSO系统过期时，而子系统1没有过期，当用户此时请求子系统2将会需要用户再次输入用户名密码登录SSO系统。

+ 安全性
由于所有数据都是由URL地址栏传递，所以有一定的风险。

2、进阶的SSO系统
![流程图][2]
用户登录后会获取一个身份令牌(token),然后根据这个token来取得用户信息。
token存放在Cookies中。
问题是：

+ 不能跨域
Cookies 不能跨域，并且Cookies的domain属性必须设置为顶级域名。所有的子系统的顶级域名相同。

+ 不能跨浏览器

```C#
HttpCookie cookie = new HttpCookie(FormsAuthCookieName, encryptedTicket);
cookie.Expires = rememberMe ? expirationDate : DateTime.MinValue;
cookie.HttpOnly = true;
cookie.Path = "/";
cookie.Domain = "domain.com";
context.Response.Cookies.Set(cookie);
```

+ 安全性
由于token存放在Cookies中，所以有一定的风险。




  [1]: http://ww4.sinaimg.cn/large/843c8035gw1eyt9ndbbvhj20no0el76x.jpg
  [2]: http://ww4.sinaimg.cn/large/843c8035gw1eyti8li3y0j20mz0eo0va.jpg