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

