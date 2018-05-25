# Thrift

## 1 . 下载与安装
```
brew install thrift
```
## 2. 部署说明
* 编写idl文件，语法可以百度搜索 thrift idl定义，如下 **Project.idl**
命名后缀不重要，也可以是 **Project.thrift** <br/>
IDEA添加thrift help插件：Preferences -> Plugins -> Browse repositories或者<br/>
进入JetBrains[插件官网](http://plugins.jetbrains.com)下载zip包 -> Install plugins from disk

```idl
namespace java com.company.thrift.project

struct User{
  1: string name,
  2: i32 age,
  3: bool gender
}

service ProjectRPC{

    string ping(1:string para)
    User getUser(1:string name)
}
```
keyword: **namespace** 和 **service ProjectRPC** <br/>
据我理解：一个rpc工程会有很多的 **${Project}.idl** 文件，每个文件代表着其他工程暴露给rpc的接口有哪些，其服务端和客户端依据自身需求import不同的工程名

* 编译idl文件
```
thrift --gen java Project.idl
```
* 编译完成后，会在当前文件夹生成一个 **gen** 文件夹，将生成的 **ProjectRPC.java** 文件放入 **namespace** 定义的地址中，重点关注 **Iface** 接口 <br/>
除此之外，程序员会写下一些连接池，thrift代理的代码，很高大上，方便使用
* 在 **pom.xml** 修改版本号，从而不会影响之前版本暴露的接口
```
<groupId>com.company</groupId>
<artifactId>rpc-lib-java</artifactId>
<version>1.0.4</version>
```
* 在与 **pom.xml** 平行的目录执行
```
mvn clean deploy
```
* 可以部署到本地私服仓库，如果需要部署到线上仓库，需要对 ```${M2_HOME}/conf/settings.xml```进行修改，增加以下内容：
```xml
<server>
   <id>id</id>
   <username>username</username>
   <password>password</password>
</server>
```
如果未配置相关信息，线上仓库部署失败，但本地仓库部署成功

## 3. 客户端与服务端
首先，所有的客户端和服务端都需要加入**Thrift rpc java**的依赖
``` xml
<dependency>
    <groupId>com.name</groupId>
    <artifactId>rpc-lib-java</artifactId>
    <version>1.0.4</version>
</dependency>
```
### 3.1 服务端
ThriftRPCSever设置
``` java
@Component
public class ThriftRPCSever implements InitializingBean,DisposableBean {

    //服务端口
    @Value("${thrift.server.port}")
    private int port;

    //服务要实现的接口
    private Class[] classes = new Class[]{projectService.class};

    //thrift启动服务代理工具类
    ThriftRPCSeverProxy severProxy = new ThriftRPCSeverProxy();

    @Override
    public void destroy() throws Exception {
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        severProxy.startAsync(port,classes);
    }
}
```
以上方式不能支持spring的依赖注入，即 **Service.java** 不能与spring很好配合，修改如下
```java
@Component
public class ThriftRPCSever implements InitializingBean,DisposableBean {

    //服务端口
    @Value("${thrift.server.port}")
    private int port;

    @Autowired
    private ProjectService projectService;

    //thrift启动服务代理工具类
    ThriftRPCSeverProxy severProxy = new ThriftRPCSeverProxy();

    @Override
    public void destroy() throws Exception {

    }

    @Override
    public void afterPropertiesSet() throws Exception {
        severProxy.startAsync(port,new Object[]{projectService});
    }
}
```
服务端接口
``` java
import namespace.User;
public class ProjectService implements namespace.ProjectRPC.Iface{
    static Logger logger = LoggerFactory.getLogger(Service.class);

    @Override
    public String ping(String s) throws TException {
        logger.info("ping " + s);
        return "pong";
    }

    @Override
    public User getUser(String name) throws TException {
        logger.info("name " + name);
        User user = new User();
        user.setAge(10);
        user.setName(name);
        user.setGender(false);
        return user;
    }
}
```
### 3.2 客户端
ThriftBeanConfig.java
```java
@Configuration
 public class ThriftBeanConfig implements DisposableBean {
     static Logger logger = LoggerFactory.getLogger(ThriftBeanConfig.class);

     //某project的thrift 地址
     @Value("${thrift.connection.project.ip}")
     private String projectHost;
     @Value("${thrift.connection.project.port}")
     private int projectPort;
     private ConnectionProvider connectionProviderOnProject;

     /**
      * 创建RPC client端代理类
      */
     @Bean
     public ProjectRPC.Iface projectRPCService() throws Exception {

         connectionProviderOnProject = new ConnectionProvider(projectHost,projectPort,10*1000);
         connectionProviderOnProject.initPool();

         ProjectRPC.Iface iface = ThrfitClientManager.getThriftClient(ProjectRPC.Iface.class, connectionProviderOnProject);
         return iface;
     }


     @Override
     public void destroy() throws Exception {
         logger.info("释放 thrift 连接");
         connectionProviderOnProject.destroyPool();
     }
 }
```
使用
``` java
import namespace.ProjectRPC;
@RestController
@Api("web pc界面的相关操作")
public class TestController {

    @Autowired
    private ProjectRPC.Iface projectRPCService;


    @RequestMapping(value = "/test/thrift/ping", method = RequestMethod.GET)
    @CrossOrigin("*")
    public Object testThrift() throws TException {
        return projectRPCService.ping("test ping");
    }

    @RequestMapping(value = "/test/thrift/user", method = RequestMethod.GET)
    @CrossOrigin("*")
    public Object testThrift(String name) throws TException {
        return projectRPCService.getUser(name);
    }
}

```
