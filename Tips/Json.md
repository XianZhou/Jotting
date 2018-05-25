# 用JSON进行序列化时遇到的一些坑

## 1. isOnline类型的字段

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
