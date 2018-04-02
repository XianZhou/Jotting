# Set & List

**Set与List都继承了Collection类，而Collection继承了Iterable类**

## 1. Collection 集合
集合，包括多个元素的对象<br/>
Java的集合主要有两种Collection和Map，两个是独立的类，各自包含一些子类
### 1.1 Collection的遍历
  * for-each语法
  ``` java
  Collection<User> users = new ArrayList<User>();
  for (User user : users) {
      System.out.println(user.getName());  
  }  
  ```

  * Iterator 迭代器
  ```java
  Collection<User> users = new ArrayList<User>();
  Iterator iterator = users.iterator();
  while (iterator.hasNext()) {
      System.out.println(iterator.next().getName());
  }
  ```

  * aggregate operations 聚合操作
  ```java
  Collection<User> users = new ArrayList<User>();
  users.stream().forEach(new Consumer<User>() {
      @Override
      public void accept(User user) {
          System.out.println(user.getName());
      }
  });
  ```

### 1.2 Collection与Collections
Collection与Collections都引入自 ```java.util.Collection``` <br/>
**Collection** 是一个集合接口，提供了集合对象进行基本操作的通用接口方法 <br/>
**Collections** 是一个包装类，服务于Collection框架，包含各种有关集合操作的静态多态方法，不能被实例化，后面会介绍 **Collections.sort()**


## 2. Set
* 不允许有重复对象，只会显示一个
* 无序容器，部分子类可实现有序
* 只允许一个null元素，**TreeSet** 不允许有null
* 常用的实现类是 **HashSet** , **LinkedHashSet** , **TreeSet**
```java
User user1 = new User("a", 4);
User user2 = new User("b", 3);
User user3 = new User("c", 2);
User user4 = new User("d", 1);
set.add(user1);
set.add(user2);
set.add(user3);
set.add(user4);
```
  * **HashSet**
  ```java
  Set<User> set = new HashSet<>();
  ```
  输出user.getName(): **无序**
  ```  
  b d a c
  ```
  * **LinkedHashSet**
  ```java
  Set<User> set = new LinkedHashSet<>();
  ```
  输出user.getName(): **与插入顺序相同**
  ```  
  a b c d
  ```
  * **TreeSet**
  按照插入的内容进行默认排序，如1, 2, 3... 或者 a, b, c...  <br/>
  若插入类型为一个对象，则需要重写此对象的 **compareTo** 方法，定义排序方法
  ```java
  @Override
    public int compareTo(User user) {  
        return this.getAge() - user.getAge();
//        return this.getName().compareTo(user.getName());
    }
  ```
  **compareTo**: 面对对象使用, 1为大，0相等，-1为小
  ``` java
  Integer x = 5;
  System.out.println(x.compareTo(3)); //1
  System.out.println(x.compareTo(5)); //0
  System.out.println(x.compareTo(8)); //-1
  ```
  默认排序为从小到大，**升序**
  ```java
  Set<User> set = new TreeSet<>();
  ```
  输出user.getName():
  ```  
  d c b a
  ```

## 3. List
* 允许重复的对象
* 有序容器，输出顺序即插入顺序
* 可以插入多个null元素
* 常用的实现类是 **ArrayList** , **LinkedList** , **Vector**
  * **ArrayList** 数组实现，随机访问效率高，随机插入、随机删除效率低
  * **LinkedList** 双向链表，随机访问效率低，但随机插入、随机删除效率高
  * 重写 **compareTo** 对List排序无效

## 4. 排序
Set的排序可以通过TreeSet，在实体类进行 ```compareTo``` 方法的重写，也可以通过 List 与 Set 的相互转换，但是注意Set不支持有重复值
* 新建一个包含 ```set``` 内容的List对象
  ```java
  List<User> list = new ArrayList<>(set);
  ```

* 可以借用List进行排序，重写sort方法；user中age为int类型，无法用compareTo方法

 ```java
 Collections.sort(list,(User a, User b)->(Integer.compare(a.getAge(), b.getAge())));

 ```

* 然后将List转换为Set，LinkedHashSet的顺序是插入顺序

  ```java
  set = new LinkedHashSet<>(list);

  ```
