# JSON序列化

## 1. fastjson
``` java
//json字符串-简单对象型
private static final String  JSON_OBJ_STR = "{\"studentName\":\"lily\",\"studentAge\":12}";

//json字符串-数组类型
private static final String  JSON_ARRAY_STR = "[{\"studentName\":\"lily\",\"studentAge\":12},{\"studentName\":\"lucy\",\"studentAge\":15}]";

//复杂格式json字符串
private static final String  COMPLEX_JSON_STR = "{\"teacherName\":\"crystall\",\"teacherAge\":27,\"course\":{\"courseName\":\"english\",\"code\":1270},\"students\":[{\"studentName\":\"lily\",\"studentAge\":12},{\"studentName\":\"lucy\",\"studentAge\":15}]}";
```
### 1.1 说明
* JSONArray和JSONObject都继承了JSON，即JSONObject.parseObject等同于JSON.parseObject
* JSON.parseObject(str, Bean.class): 填充名称相同的属性，对于Json字符串中没有，而Bean类有的属性，会为null；对于model类没有，而Json字符串有的属性，不做任何处理

### 1.2 使用
* JSON格式字符串转为JSON对象
```java
JSONObject jsonObject = JSONObject.parseObject(JSON_OBJ_STR);
//json object 取值
System.out.println("studentName:  " + jsonObject.getString("studentName"));
```
* JSON对象转为JSON格式字符串
```java
String jsonString = JSONObject.toJSONString(jsonObject);
```
* JSON字符串(数组类型)转换为JSONArray
```java
JSONArray jsonArray = JSONArray.parseArray(JSON_ARRAY_STR);
```
* JSONArray转换为JSON字符串(数组类型)
```java
String jsonString = JSONArray.toJSONString(jsonArray);
```
* 复杂JSON格式字符串转换为JSONObject
```java
JSONObject jsonObject = JSONObject.parseObject(COMPLEX_JSON_STR);
JSONObject jsonObjectcourse = jsonObject.getJSONObject("course");
JSONArray jsonArraystudents = jsonObject.getJSONArray("students");
```
* JSON格式字符串转换为JavaBean
```java
Student student = JSONObject.parseObject(JSON_OBJ_STR, Student.class);
```
* JSON字符串(数组类型)转换为List<JavaBean>
```java
List<Student> studentList = JSONArray.parseArray(JSON_ARRAY_STR, Student.class);
```
* 复杂JSON格式字符串转换为JavaBean
```java
Teacher teacher = JSONObject.parseObject(COMPLEX_JSON_STR, Teacher.class);
```
* JavaBean转换为Json对象
```java
JSONObject jsonObject = (JSONObject) JSONObject.toJSON(student);
```
* JavaList与JsonArray之间的转换
```java
JSONArray jsonArray = (JSONArray) JSONArray.toJSON(students);
```
* 复杂JavaBean转换为Json对象
```java
JSONObject jsonObject1 = (JSONObject) JSONObject.toJSON(teacher);
```


## 2. isOnline类型的字段

``` java
@Entity
@Table(name = "Model")
@Data
public class Model implements Serializable{

    ...

    @Column(columnDefinition = "bit DEFAULT false")
    private boolean isOnline;

    //@Column(name="content", columnDefinition="TEXT", nullable=true) 同下
    @Type(type = "text")
    private String modelInfo;

}
```
**lombok.Data** 会将 **isOnline** 属性的getter和setter方法设置为：<br/>
``` java
model.setOnline(model.isOnline());
```
在JSON解析的时候会出错，导致传递的属性为 **online** <br/>
解决方案：分别解决序列化和前端的属性不一问题

```java
//fastjson 不加该注解，序列化时未online，加上后为isOnline
@JSONField(name = "isOnline")
//jacksonJson 不加该注解，前端字段变为 online，加上后为isOnline
@JsonProperty(value = "isOnline")
private boolean isOnline;
```
### 参考
1. 高性能JSON框架之FastJson的简单使用 https://segmentfault.com/a/1190000011212806
