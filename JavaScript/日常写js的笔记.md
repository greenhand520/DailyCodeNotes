





## 一、基本JavaScript

### 1、创建json串

```js
function createJson(ids) {
        var json = [];
        var data = {};
        for (var i = 0; i < ids.length; i++) {
            // 获取值用[],不是()
            data["goodIds"] = ids
        }
        json.push(data);
        return JSON.stringify(json);
    //返回结果是：{"goodIds":["1","2","3"]}， 其中["1","2","3"]是ids这个数组的内容
    }
```

上面的json是带[]的，也可以将var json=[]去掉，在JSON.stringify()传入data，这样创建的json最外层不带[]；

虽然输出jsons已经是个json串的样子，但是本身并不是json，一定需要JSON.stringify()这个函数处理下才可以，否则达不到json的效果。

比如下面用ajax向后台发送json数据（代码里ids是数组），如果不用该函数处理下，后台就接受不到

```js
$.ajax({
            type: 'post',
            url: '<c:url value="/list"/>',
            dataType: 'json',
            data: {"goodIds": JSON.stringify(ids)},
            success: function (data, status) {
            }
        });
```

**注意：**

1、因为创建json串的函数已经用JSON.stringify()处理了，如果在ajax中再次用这个函数处理的话，后台接受数据显示的结果就会不一样。比如，前者的话后台接受的数据是{"goodIds":["1","3","2"]}，后者后台接受的数据就是这样的"{\"goodIds\":[\"1\",\"2\",\"3\"]}"，这个就会导致后台解析json时报 “A  JSONObject text must begin with '{' at 1 [character 2 line 1]” 的错。

2、这里data如果写成 data: {"goodIds": ids} 的话（ids是数组），后台也是无法接收到数据的，即使把发送的数据类型改成text，后台也是无法接受的。因为jQuery需要调用jQuery.param序列化参数， jQuery.param( obj, traditional ) 默认的话，traditional为false，即jquery会深度序列化参数对象，以适应如PHP和Ruby on Rails框架， 但servelt api无法处理，我们可以通过设置traditional 为true阻止深度序列化，然后序列化结果如下：

 "goodIds":["1","2","3"]   =>    goodIds=1&goodIds=2&goodIds=3，

随即，我们就可以在后台通过request.getParameterValues()来获取参数的值数组了。

(部分参考：http://folyred.iteye.com/blog/1554825)

### 2、提取字符串中的数字

#### i、用parseInt(str)

str可以是一个只有数字的字符串，也可以是以数字开头的字符串

#### ii、用正则表达式

当字符串不是以数字开头的话，上面的方法便没有作用，如：

```js
var pdb = “pdb520”;
pdbId = pdbId.replace(/[^0-9]/ig, "");
```

## 二、jQuery

### 1、获取当前点击元素的信息

####  	1）用$(this)获得当前元素

```js
$(".phonetic span").click(function () {
                play($(this).parent(".phonetic").attr("id"));
            });
```

获取当前元素的class为“phonetic”的父元素的id值

#### 	2）用$(e.target)或得当前元素

```js
$(".phonetic span").click(function (e) {
                play($(e.target).parent(".phonetic").attr("id"));
            });
```

同上     

### 2、  获取及设置元素内容和值     

用val()、text()、html()这三个函数，三者有不同的地方，其中text和html是获取innerHTML部分，而val是获取value部分。如果括号中传入数据，则代表设置元素的值。

#### 1）text()        

   obj.text（）获取元素中间部分内容，但是不包括html标记。

   obj.text("内容")设置元素中间的文本值，如果内容里含有html标记，不会生效，依旧视作文本内容。     

```js
$("#show-phonetic-on-detail").text($("#pdb1").prev().text());
```

意思是：

将  id为“pdb1”的元素上一个兄弟节点的内容（text中不传数据获取它的内容）设置为id为“show-phonetic-on-detail”的元素的内容。

对应html部分：即将<lable>元素中间设置为[f]。如果<lable>中间有其它元素的话，此方法不起作用。

```html
<td id="p27" class="phonetic" title="" style="cursor: pointer;">
    <span>[f]</span>
    <img src="" id="pdb1" class="phonetic-detail-button">
</td>

<label id="show-phonetic-on-detail">&nbsp;</label>
```

#### 2）val()

用于获取和设置value, 用于获取和设置属性value的值，如input，li元素，但是button的value属性无法获取，

```js
$(document).ready(function(){
            alert($("input").val());
            $("input").val("<p style='color:red'>测试内容</p>");
        });
```

对应html部分：

```html
<input type="text" value="测试内容">
```

#### 3）html()

和text类似，但是html取值时候，可以取到html的标记；设值的时候，**内容含有html标记会被解析执行**

## 3、获取、设置元素属性的值

参考：https://blog.csdn.net/u013746071/article/details/52449925

attr()函数

例子：

```html
<p title="你最喜欢的明星是？">你最喜欢的明星是？</p>
<ul>
   <li title="刘亦菲">刘亦菲</li>
   <li title="李易峰" value="123">李易峰</li>
   <li title="杨洋">杨洋</li>
</ul>
```

### 1）获取属性的值

```js
alert($("ul li").attr("title"));//结果刘亦菲
alert($("ul li：eq(0)").attr("title"));//结果：刘亦菲
alert($("ul li:eq(1)").attr("title"));//结果：李易峰
alert($("ul li").attr("value"));//结果：undefined
alert($("ul li:eq(1)").attr("value"));//结果：123
```

eq()是按数组查找的，传入0表示第一个

```js
$(".phonetic span").click(function () {
                play($(this).parent(".phonetic").attr("id"));
            });
```

意思是：

获取当前执行此动作的元素的class为“phonetic”的父元素的id属性值，传入到play()函数中。

### 2）设置属性值

#### i、attr(name,value)  

设置属性的值

```js
$("ul li:eq(2)").attr("title","胡歌");
alert($("ul li:eq(2)").attr("title"));//结果：胡歌
```

#### ii、attr(name,fn)  

设置属性的函数值

```js
$("ul li:eq(1)").attr("title",function(){ return this.value});
alert($("ul li:eq(1)").attr("title"));//123
```

#### iii、attr(properties) 

将一个“名/值”形式的对象设置为所有匹配元素的属性，传入大括号扩的键值对，设置所有选定元素的对应属性值，如id，class，style等

```js
$("ul li").attr({title:"不是李易峰",value:"不是123"});
    alert($("ul li:eq(1)").attr("title"));//不是李易峰
    alert($("ul li:eq(1)").attr("value"));//不是123 
    alert($("ul li:eq(2)").attr("title"));//不是李易峰
    alert($("ul li:eq(2)").attr("value"));//不是123
    alert($("ul li:eq(0)").attr("title"));//不是李易峰
    alert($("ul li:eq(0)").attr("value"));//不是123
```

```js
$("ul li:eq(1)").attr({style:"color:red"});
//李易峰字体的颜色变成红色
```

另，删除属性：removeAttr()

```js
$("ul li:eq(1)").removeAttr ("title");
```

## 4、元素筛选

### 1）获取祖先元素、父元素、兄弟元素、孩子元素

所有的都可以传入元素选择器进行过滤

#### ii、获取祖先元素

parents()是查找所有祖先元素，不限于父元素

#### ii、获取父元素

parent() 找父亲节点，比如$("span").parent()或者$("span").parent(".class")3）获取兄弟元素

#### iii、获取兄弟元素

```js
$("#test1").prev();  // 上一个兄弟节点
$("#test1").prevAll(); // 之前所有兄弟节点
$("#test1").next(); // 下一个兄弟节点
$("#test1").nextAll(); // 之后所有兄弟节点
$("#test1").siblings(); // 所有兄弟节点
$("#test1").siblings("#test2");
```

### v、获取孩子元素

children()返回所有子节点，这个方法只会返回直接的孩子节点，不会返回所有的子孙节点

contents()返回下面的所有内容，包括节点和文本。这个方法和children()的区别就在于，包括空白文本，也会被作为一个jQuery对象返回，children()则只会返回节点

```js
$("#test").children(); // 全部子节点
$("#test").children("#test1");
$("#test").contents(); // 返回#test里面的所有内容，包括节点和文本
```

### 2）jQuery子元素选择器

```js
 // 以下方法都返回一个新的jQuery对象，他们包含筛选到的元素
    $("ul li").eq(1); // 选取ul li中匹配的索引顺序为1的元素(也就是第2个li元素)
    $("ul li").first(); // 选取ul li中匹配的第一个元素
    $("ul li").last(); // 选取ul li中匹配的最后一个元素
    $("ul li").slice(1, 4); // 选取第2 ~ 4个元素
    $("ul li").filter(":even"); // 选取ul li中所有奇数顺序的元素
```

find()跟filter()完全不一样。后者是从初始的jQuery对象集合中筛选出一部分，而前者的返回结果，不会有初始集合中的内容，比如$("p"),find("span"),是从p元素开始找,等同于$("p span")。

### 3）js获取元素(父节点,子节点,兄弟节点)

```js
var test = document.getElementById("test");
　　var parent = test.parentNode; // 父节点
　　var chils = test.childNodes; // 全部子节点
　　var first = test.firstChild; // 第一个子节点
　　var last = test.lastChile; // 最后一个子节点　
　　var previous = test.previousSibling; // 上一个兄弟节点
　　var next = test.nextSbiling; // 下一个兄弟节点
```

## 5、更新视频的src

不能用attr("src", src)，要用<source/>标签更新视频，然后还要用load()函数。

load()函数一定要和下面这样。

```js
var src = "${pageContext.request.contextPath}" + "/" + data;
var viderPlayer = $("#phonetic-video");
viderPlayer.html('<source src="' + src + '" type="video/mp4"/>');
viderPlayer[0].load();
viderPlayer[0].play()；	//自动播放
```

参考：https://stackoverflow.com/questions/15422592/html5-audio-to-reload-file

## 6、关于js的定时器

1、setTimeout(param1, param2)，param1是要执行的函数，param2是经过多长ms后执行函数的时间，单位是ms，第一个参数是将函数以字符串的形式传入，第二个直接传入时间，比如：

```js
window.onload = function () {
        setTimeout("insertBPage()", 5000);
    };

    function insertBPage() {
        alter("here");
    }
```

如果第一个参数不是以字符串的形式传入话，会立即执行该函数。

## 7、js向页面添加jsp:include标签来包含另一个页面，jsp是不会对其进行解析的

解释：

js是客户端的，网页执行的顺序是由服务端执行jsp，然后发给客户端  客服端再添加jsp标签也不会返回到服务端去执行，也就是在客户端添加jsp标签是向html添加jsp标签，但是浏览器&html不认识jsp标签的作用，不知道怎么去解析它。