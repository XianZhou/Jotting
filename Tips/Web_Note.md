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
  ```spring.thymeleaf.prefix=classpath:/static/```

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
* ```@Controller``` : 返回到指定页面，需要用 ```@Controller``` 配合视图解析器InternalResourceViewResolver
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

## 2. jQuery ajax
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
