# A Little Web
基于Springboot工程
## 1. Thymeleaf 模板
pom.xml引入依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
Thymeleaf的几项默认配置：
  * 默认页面映射路径 ```classpath:/templates/*.html ``` <br/>
  修改路径：在 ```properties``` 文件添加
  ```XML
  spring.thymeleaf.prefix=classpath:/static/
  ```

  * 静态文件路径为 ```classpath:/static/``` 引用如下：
    ```html
    <!-- /static/js/main.js -->
    <script type="text/javascript" src="/js/main.js"></script>

    <!-- /static/css/main.css -->
    <link rel="stylesheet" type="text/css" href="/css/main.css">
    ```

视图解析器 & ViewController:  <br/>
在页面控制类进行网页的定向
```java
@Controller
public class ViewController {

    @GetMapping("/")
    public String index() {
        return "redirect:/dist/index.html";
    }

    @GetMapping("/login")
    public String loginView() {
        return "login";
    }

    /**
     * 探活用
     */
    @GetMapping("/ping")
    @ResponseBody
    public String ping() {
        return "pong\n";
    }
}
```
* ```@Controller``` : 返回到指定页面，需要用 ```@Controller``` 配合视图解析器 InternalResourceViewResolver
* ```@RestController```: 返回JSON，XML或自定义mediaType内容到页面，注解相当于 ```@Controller``` + ```@ResponseBody```
* 如果需要指定到 ```templates``` 目录下的文件，则如
```java
return "login";
```
* 如果需要指定到 ```static```目录下的文件，则如
```java
return "redirect:/dist/index.html";
```
* 如果是在 ```static``` 目录下的文件，例如：```/static/dist/index.html``` 可以直接访问url ```/dist/index.html```
* 如果是在 ```templates``` 目录下的文件，直接访问url，不通过视图解析器，则会
```html
There was an unexpected error (type=Not Found, status=404).
No message available
```

## 2. jQuery
导入jQuery的js文件，写在项目js之上
```HTML
<script src="https://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
```
### ajax
后端写好接口，前端如何调用呢？介绍两种写法，对不同的请求类型均通用
* http GET
```javascript
$.ajax({
  type: 'get',
  url: '/web/data/apple',
  dataType: 'json',
  data: {
    appleId: appleId
  },
  success: function (response) {
    //处理数据

  }
});
```
* http POST
```javascript
$.post("/web/data/apple",
  {
    appleId: appleId,
  },
  function (response) {
  });
```
### append
[append的使用简介](http://www.w3school.com.cn/jquery/manipulation_append.asp)
```javascript
$("button").click(function(){
  $("p").append(" <b>Hello world!</b>");
});
```

## 3. Date转换
时间格式转换方法及使用
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <title>Date Format Test</title>
</head>
<body>
<script type="text/javascript">
  Date.prototype.format = function (format) {
    var date = {
      "M+": this.getMonth() + 1,
      "d+": this.getDate(),
      "h+": this.getHours(),
      "m+": this.getMinutes(),
      "s+": this.getSeconds(),
      "q+": Math.floor((this.getMonth() + 3) / 3),
      "S+": this.getMilliseconds()
    };
    if (/(y+)/i.test(format)) {
      format = format.replace(RegExp.$1, (this.getFullYear() + '').substr(4 - RegExp.$1.length));
    }
    for (var k in date) {
      if (new RegExp("(" + k + ")").test(format)) {
        format = format.replace(RegExp.$1, RegExp.$1.length == 1
          ? date[k] : ("00" + date[k]).substr(("" + date[k]).length));
      }
    }
    return format;
  };

  var time = new Date().format("yyyy-MM-dd hh:mm:ss");
  alert(time);
</script>
</body>
</html>
```

## 4. 轮询
轮询示例：
```javascript
//优化，如果两次查询结果一样则不做处理
var oldAppleResponse = "";

function appleList() {
  $.ajax({
    type: 'get',
    //拒绝缓存，加参数 time
    url: '/web/apples?time = ' + new Date(),
    dataType: 'json',
    success: function (response) {
      if (JSON.stringify(oldAppleResponse) !== JSON.stringify(response)) {
        oldAppleResponse = response;
        //查询结果不同，进行操作
      }
  });
}

appleList();
//10s 轮询
setInterval(appleList, 10000);
```

## 5. Bootstrap 基本使用
Bootstrap 是一个用于快速开发 Web 应用程序和网站的前端框架。Bootstrap 是基于 HTML、CSS、JAVASCRIPT 的
* 下载 Bootstrap 的最新版本 http://getbootstrap.com/
* 有两种文件下载： **Compiled CSS and JS** & **Source file** ；这里使用 Bootstrap 的预编译版本
```shell
➜  Downloads cd bootstrap-4
➜  bootstrap-4 ls
css js
```
* 把 ```css``` 和 ```js``` 文件夹复制到前端代码中
* 引入
  * 直接引入下载文件的相对路径；与Internet不通无法使用CDN加速，可将文件下载到本地引用
  * 引入CDN服务器下的bootstrap，CDN是Content Delivery Network的缩写，Bootstrap把自己的css、js等文件托管到某一个网络服务器上使用时调用，不需要下载 Bootstrap
```HTML
<!-- 版本自选 -->
<!-- 核心 css 文件 -->
<link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
<!-- 可选的Bootstrap主题文件 -->
<script src="http://cdn.static.runoob.com/libs/bootstrap/3.3.7/css/bootstrap-theme.min.css"></script>
<!-- jQuery文件 务必在 bootstrap.min.js 之前引入 -->
<script src="https://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
<!-- 核心 JavaScript 文件 -->
<script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
```
* 使用：参照网址 https://getbootstrap.com/docs/4.1/components/alerts/

## 6. js 里嵌套 html
含参示例：
```javascript
function Panel(apple) {

  const htmlText = `
      <tr>
          <th>${apple.id}</th>
          <td>${apple.date}</td>
      </tr>`;
  return htmlText;
}
```
