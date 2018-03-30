# Set & List

**Set与List都继承了Collection类，而Collection继承了Iterable类**

## 1. Collection 集合
集合，包括多个元素的对象
Java的集合主要有两种Collection和Map，两个是独立的类，各自包含一些子类
### 1.1 Collection的遍历
  * for-each语法
  ``` java
  Collection<User> users = new ArrayList<User>();
  for (User user : users) {
      System.out.println(user.name);  
  }  
  ```

## 2. Set

## 3. List

## 4. 排序
 * Set有 HashSet，LinkedHashSet，TreeSet
 * List有 ArrayList， HashList
 * List 与 Set 可以相互转换，但是注意Set不允许有重复值
  ```java
    List<User> list = new ArrayList<>(set);
  ```

* 可以借用List进行排序，Set是无顺序的

 ```java
    Collections.sort(list,(User a, User b)->(Long.compare(a.getSalary(), b.getSalary())));
 ```

* 然后将List转换为Set，LinkedHashSet是由顺序的转换，使用HashSet转换后又无序了

  ```java
    set = new LinkedHashSet<>(list);

  ```
