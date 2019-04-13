### 1、jsp页面获取map的值

#### 循环获取

不需要在jsp页面直达具体的key值，直接用.key和.value就可以获取。

如果map的key或者value是自定义类的话，想获取类的某一个属性值，直接继续在key或者value的后面加对应的属性名即可。如：

```jsp
<c:forEach items="${cart}" var="item">
            <tr>
                <td>${item.key.name}</td>
                <td>${item.key.price}</td>
                <td>${item.value}</td>
            </tr>
        </c:forEach>
```

#### 非循环获取

参考：https://blog.csdn.net/ithouse/article/details/39577629?utm_source=copy 

```java
Map<String, String> map = new HashMap<String, String>();
 map.put("name", "菜菜");
 request.setAttribute("map", map);

//页面上面取得的时候，用el表达式可以这样写：
${ map['name'] }
```

### 2、循环遍历map

#### 法一：效率高

```java
Map map = new HashMap(); 
Iterator iter = map.entrySet().iterator(); 
while (iter.hasNext()) { 
	Map.Entry entry = (Map.Entry) iter.next(); 
	Object key = entry.getKey(); 
	Object val = entry.getValue(); 
} 
```

#### 法二：效率低

```java
Map map = new HashMap(); 
Iterator iter = map.keySet().iterator(); 
while (iter.hasNext()) { 
	Object key = iter.next(); 
	Object val = map.get(key); 
} 
```

上面参考：https://www.cnblogs.com/stono/p/4726177.html

#### 法三：迭代

```java
Map<Object, Object> map = new HashMap<>();
        for (Object o: map.entrySet()) {	//这里一定要是Object
            Map.Entry entry = (Map.Entry) o;
            Object key = entry.getKey();
            Object value = entry.getValue();
        }
```

**注：**

1、对像LinkedHashMap等用 Iterator做遍历的时候，如果对其进行了修改，Iterator(Object ele=it.next())会检查HashMap的size，size发生变化，抛出错误ConcurrentModificationException。

有以下集中解决办法：

1) 通过Iterator修改Hashtable
 while(it.hasNext()) {
 	Object ele = it.next();
        it.remove();
 }

2) 根据实际程序，您自己手动给Iterator遍历的那段程序加锁，给修改HashMap的那段程序加锁。

 3) 使用“ConcurrentHashMap”替换HashMap，ConcurrentHashMap会自己检查修改操作，对其加锁，也可针对插入操作。