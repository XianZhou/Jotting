# Ldap
LDAP是 **轻量目录访问协议**，英文全称是Lightweight Directory Access Protocol。LDAP目录以树状的层次结构来存储数据。与自顶向下的DNS树或UNIX文件的 **目录树** 比较类似。就像DNS的主机名那样，LDAP目录记录的标识名（Distinguished Name，简称DN）是用来读取单个记录，以及回溯到树的顶部。可用于公司内部员工的统一身份验证

## 1. Ldap结构
* **DN** : Distinguished Name 由下面三部分组成
    * **CN** : Common Name -> 用户名或者服务器名
    * **OU** : Organization Unit -> 组织单元
    * **DC** : Domain Component -> 基准，根

```
cn=xian.zhou@company.com,ou=people,dc=example,dc=com
```

* 根据DN获取SN，返回 **userDn**

```java
@Autowired
private LdapTemplate ldapTemplate;

private String getDnForUser(String cn) {
        EqualsFilter f = new EqualsFilter("cn", cn);
        List result = ldapTemplate.search(DistinguishedName.EMPTY_PATH, f
                .toString(), new AbstractContextMapper() {
            protected Object doMapFromContext(DirContextOperations ctx) {
                return ctx.getNameInNamespace();
            }
        });
        if (result.size() != 1) {
            throw new RuntimeException("User not unique or not found");
        }
        return (String) result.get(0);
    }
```
* LDAP登陆验证，输入 **userDn** 和 **password**

```java
private void LdapLogin(String password, String userDn) throws AuthException {
    DirContext ctx = null;
    try {
        //使用用户名、密码验证域用户
        ContextSource source = ldapTemplate.getContextSource();
        ctx = source.getContext(userDn, password);
    } catch (Exception e) {
        throw new AuthException("ldap account authentication fail");

    } finally {
        //关闭ldap连接
        LdapUtils.closeContext(ctx);
    }
}
```

# DNS
域名系统DNS（Domain Name System）是一种能够完成从名称到地址或从地址到名称的映射系统。在使用DNS的情况下，计算机用户可以间接地通过域名来完成通信。Internet中的DNS被设计成为一个联机分布式数据库系统，采用客户/服务器方式工作。分布式的机构使DNS具有很强的容错性。
## 1. 域名结构
DNS树的每个节点有一个**标号**， 每个标号最多包含63个字符，根节点为**空字符串**。DNS要求每个节点的子节点有不同的标号，以保证域名的唯一性。 <br/>
一个完整的域名是用符号“.”分隔的标号序列，域名总是从节点向上读取到根节点。最后一个标号是根节点的标号。根节点下，每个一级节点都包含一个或多个二级节点，以此类推向下拓展，从0级根节点到127级，共128级。所有的域名可以根据其 **一级节点** 的类型分成三个部分:
*   **国家域** : cn, uk
*   **通用域** : com, edu, net, org
*   **反向域** : 用于将地址映射为名称，其一、二级节点为arpa和inaddr，其他部分用于定义IP地址

## 2. 域名地址解析
* 1 浏览器会检查缓存中有没有这个域名对应的解析过的IP地址，如果缓存中有，这个解析过程就结束；
  * 缓存存在的问题：长时间缓存某一映射可能导致返回一个过期的映射；
  * 解决方案：
    * 1 授权服务器始终给缓存映射添加TTL(Time to Live)的信息，以秒为单位，过期则无效需再次请求到授权服务器
    * 2 浏览器或者服务器为其缓存的每一个映射保存一个TTL计数器，缓存定期检查，清除TTL过期的映射
* 2 浏览器会查找操作系统缓存中是否有这个域名对应的DNS解析结果：
  * 在Windows中可以通过 ```C:\Windows\System32\drivers\etc\hosts``` 文件来设置
  * 在Linux中可以通过 ```/etc/hosts``` 文件来设置
  * 用户可以将任何域名解析到任何能够访问的IP地址
* 3 前两个过程无法解析时，就要用到我们网络配置中的```DNS服务器地址```了。操作系统会把这个域名发送给这个```LDNS(本地区的域名服务器)```。这个DNS通常都提供给用户本地互联网接入的一个DNS解析服务，例如用户是在学校接入互联网，那么用户的DNS服务器肯定在学校；如果用户是在小区接入互联网，那么用户的DNS就是再提供接入互联网的应用提供商，即电信或联通，也就是通常说的SPA，那么这个DNS通常也会在用户所在城市的某个角落，不会很远。这个专门的域名解析服务器性能都会很好，它们一般都会缓存域名解析结果，当然缓存时间是受到域名的失效时间(TTL)控制的，LDNS将缓存的映射返回给客户机，并且将该响应标志为“非授权的”，通知客户机该响应不是来源于授权服务器。大约80%的域名解析到这里就结束了，所以LDNS主要承担了域名的解析工作。
* 4 递归解析 或者 迭代解析
  * 递归解析：
    * 1 客户机向```.edu域名服务器(学校LDNS)```发送查询请求，希望获知域名**test.china.huawei.com**对应的IP地址
    * 2 .edu域名服务器发现自己不是该域名的授权者，将该请求发送给**根域名服务器**（全球共13台）
    * 3 根域名服务器发现自己也不是该域名的授权者，将该请求发送给```主域名服务器（gTLD Server）``` —— .com域名服务器
    * 4 .com域名服务器发现自己也不是该域名的授权者，将该请求发送给.huawei.com域名服务器
    * 5 .huawei.com域名服务器发现自己也不是该域名的授权者，将该请求发送给 .china.huawei.com域名服务器
    * 6 .china.huawei.com域名服务器发现自己是该域名的授权者，查询得到该域名对应的IP地址，并将其发送给.huawei.com域名服务器
    * 7 .huawei.com域名服务器将其发送给.com域名服务器
    * 8 .com域名服务器将其发送给根域名服务器
    * 9 根域名服务器将其发送给.edu域名服务器（LDNS）
    * 10 .edu域名服务器将其发送给客户机

    ![dns_recursion](https://raw.githubusercontent.com/XianZhou/Jotting/master/img/Tips/ldap/dns_recursion.jpg)

  * 迭代解析
    * 1 客户机向```.edu域名服务器(学校LDNS)```发送查询请求，希望获知域名**test.china.huawei.com**对应的IP地址
    * 2 .edu域名服务器发现自己不是该域名的授权者，向客户机返回**根域名服务器**的IP地址
    * 3 客户机向根域名服务器发送查询请求
    * 4 根域名服务器发现自己也不是该域名的授权者，向客户机返回```主域名服务器（gTLD Server）``` —— .com域名服务器的IP地址
    * 5 客户机向.com域名服务器发送查询请求
    * 6 .com域名服务器发现自己也不是该域名的授权者，向客户机返回.huawei.com域名服务器的IP地址
    * 7 客户机向.huawei.com域名服务器发送查询请求
    * 8 .huawei.com域名服务器发现自己也不是该域名的授权者，向客户机返回 .china.huawei.com域名服务器的IP地址
    * 9 客户机向.china.huawei.com域名服务器发送查询请求
    * 10 .china.huawei.com域名服务器发现自己是该域名的授权者，查询得到该域名对应的IP地址，并将其发送给客户机

    ![dns_recursion](https://raw.githubusercontent.com/XianZhou/Jotting/master/img/Tips/ldap/dns_iteration.jpg)

## 参考
1. DNS https://blog.csdn.net/rentian1/article/details/52288545 <br/>
2. DNS域名解析过程 https://blog.csdn.net/zhangyuan19880606/article/details/51141610 <br/>
