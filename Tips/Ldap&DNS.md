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
### 2.1 递归解析

* 1.主机进程调用解析器
* 2.解析器向最近的DNS服务器发送映射请求：
    * 将名称映射为地址：解析器向服务器提交域名。
    * 将地址映射为名称：解析器向服务器提交经过处理的地址（例33.13.252.140.inaddr.arpa.）
* 3.服务器判断是否含有解析器需要的信息：
  * 是，向解析器发送映射响应
  * 否，将解析器指向其他服务器，或者请求其他服务器提供映射信息。解析器或其他服务器进行类似处理，直至服务器向解析器发送映射响应
* 4.解析器收到并解析该映射响应，以确定该响应是一个真正的解析还是一个错误
* 5.解析器将结果传递给发出解析请求的主机进程
### 2.2 迭代解析

## 参考
1. DNS https://blog.csdn.net/rentian1/article/details/52288545 <br/>
