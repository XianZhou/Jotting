# Springboot 启动

## 1. IntelliJ IDEA运行
运行这个项目的Application类的main方法 <br/>
**注意:** 工程里所有的代码必须在Application类的同级目录或者下级目录
 ![springbootStart_1](https://raw.githubusercontent.com/XianZhou/Jotting/master/img/Tips/SpringbootNote/springbootStart_1.png)

## 2. 手动运行
需要能执行 **mvn** 命令；添加maven的环境变量 <br/>
  * ```vi ~/.bash_profile```
  * 添加以下内容：
  ```
    # added maven
    export M2_HOME=/Users/xxxx/apache-maven-3.5.2
    export PATH=$PATH:$M2_HOME/bin
  ```
  * 让配置文件在修改后立即生效 ```source ～/.bash_profile```
  * 验证 ```mvn -v```

### 2.1 直接运行
在该项目的根目录下执行： ```mvn spring-boot:run```
### 2.2 生成jar包运行
#### 手动生成jar包
在该项目的根目录下执行 ```mvn install``` <br/>
该命令会在项目根目录下生成 **target** 文件夹 <br/>
 进入该目录然后执行 ```java -jar xxxx-SNAPSHOT.jar``` 注意保证项目的jdk版本与本机一致

#### IntelliJ IDEA运行
IDEA 右侧边 **maven projects** -> **project name** -> **Liftcycle** -> **clean** -> **package** <br/>
  * **mvn clean** 是maven的一个清洁生命周期，目的是删除build目录下的构建输出，体现在删除了整个target文件夹
  * **mvn package** 把jar打到本项目的target下； **maven install** 把target下的jar安装到本地仓库，可供其他项目引入依赖

或者在 **Terminal** 执行 ```mvn clean package -Dmaven.test.skip=true``` <br/>
  * **-DskipTests** 不执行测试用例，但编译测试用例类生成相应的class文件至target/test-classes下
  * **-Dmaven.test.skip=true** 不执行测试用例，也不编译测试用例类

# Swagger
Swagger可以方便工程测试以及文档的编写，具体用法是：运行项目后访问 **projectURL/swagger-ui.html**
## 1. Springboot引用Swagger
* 1 在 **pom.xml** 文件添加依赖
  ```xml
  <dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.5.0</version>
  </dependency>

  <dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.5.0</version>
  </dependency>
  ```
* 2 在**controllr**层中添加Swagger的解释性注解
* 3 在**Application**类添加Swagger注解
  ```java
  @EnableSwagger2    //允许Swagger
  @SpringBootApplication
  public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
  ```
