# Map

## 1. 遍历
```java
Map<String, String>map = new HashMap<>();
    map.put("a","aa");
    map.put("b","bb");
    map.put("c","cc");
    map.put("d","dd");

    //第一种
    for (String key: map.keySet()){
        System.out.println("key: "+ key+" value: "+ map.get(key));
    }

    //第二种 实习常用
    Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
    while (iterator.hasNext()){
        String key = iterator.next().getKey();
        // map.remove(key); 会抛出ConcurrentModificationException异常
        //但是如果使用iterator.remove()方法就不会报错
        System.out.println("key: "+ key+" value: "+ map.get(key));
    }

    //第三种
    for (Map.Entry<String, String>entry: map.entrySet()){
        String key = entry.getKey();
        // map.remove(key); 会抛出ConcurrentModificationException异常
        System.out.println("key: "+ key+" value: "+ map.get(key));
    }
```
