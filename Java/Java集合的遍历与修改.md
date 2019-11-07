## Map的遍历与修改

### 遍历

```java
public static void main(String[] args) {

   // 构建一个Map 初始值为3条数据
  Map<String, String> map = new HashMap<String, String>();
  map.put("1", "xiaqiu");
  map.put("2", "pangzi");
  map.put("3", "shouzi");
  
  //第一种：普遍使用，二次取值
  System.out.println("通过Map.keySet遍历key和value：");
  for (String key : map.keySet()) {
   System.out.println("key= "+ key + " and value= " + map.get(key));
  }
  
  //第二种:通过Iterator迭代器遍历循环Map.entrySet().iterator();
  System.out.println("通过Map.entrySet使用iterator遍历key和value：");
  Iterator<Map.Entry<String, String>> it = map.entrySet().iterator();
  while (it.hasNext()) {
   Map.Entry<String, String> entry = it.next();
   System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
  }
  
  //第三种：推荐，尤其是容量大时(相对来说 比2好一点 效率高)
  System.out.println("通过Map.entrySet遍历key和value");
  for (Map.Entry<String, String> entry : map.entrySet()) {
   System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
  }

  //第四种
  System.out.println("通过Map.values()遍历所有的value，但不能遍历key");
  for (String v : map.values()) {
   System.out.println("value= " + v);
  }
 }
```

### 遍历的时候修改

对像LinkedHashMap等用 Iterator做遍历的时候，如果对其进行了修改，Iterator(Object ele=it.next())会检查HashMap的size，size发生变化，抛出错误ConcurrentModificationException。

有以下集中解决办法：

1) 通过Iterator修改Hashtable
 while(it.hasNext()) {
 	Object ele = it.next();
        it.remove();
 }

2) 根据实际程序，您自己手动给Iterator遍历的那段程序加锁，给修改HashMap的那段程序加锁。

 3) 使用“ConcurrentHashMap”替换HashMap，ConcurrentHashMap会自己检查修改操作，对其加锁，也可针对插入操作。

```java
Iterator<Map.Entry<String, Object>> it = map.entrySet().iterator();
while(it.hasNext()){
    Map.Entry<String, Object> entry=it.next();
    if(AssertUtil.isEmpty(entry.getValue())){
        it.remove();
    }else{
        if("systemId".equals(entry.getKey())){
            continue;
        }
        map.put(entry.getKey(),"%"+entry.getValue()+"%");
    }
}
```

## List的遍历时修改

### 1.for循环遍历list

```java
　　for(int i=0;i<list.size();i++){
　　　　if(list.get(i).equals("del")){
　　　　　　list.remove(i);
　　　　}
　　}
```

这种方式的问题在于,删除某个元素之后,list的大小发生了变化,而你的索引也在变化,所以导致你在遍历的时候会漏掉某些元素.比如当你删除第一个元素之后,继续根据索引访问第二个元素时,因为删除的关于后面的元素都往前移动了一位,所以实际访问的是集合中的第三个元素.因此,这种方式可以用在删除特定位置的一个元素时使用,但不适合循环删除多个元素时使用.

### 2.增强for循环

```java
　　for(String x : list){
　　　　if(x.equals("del")){
　　　　　　list.remove(x);
　　　　}
　　}
```

　　这种方式的问题在于,删除元素后继续循环会报错误信息`ConcurrentModificationException`,因为元素在使用的时候发生了并发的修改,导致异常的抛出。但是删除完毕马上使用break跳出，则不会触发报错。

### 3.iterator遍历

```java
　　Iterator<String> it = list.iterator();
　　while(it.hasNext()){
　　　　String x = it.next()
　　　　if(x.equals("del")){
　　　　　　it.remove();
　　　　}
　　}
```

这种方式可以正常的循环及删除。但要注意的是，使用iterator的`remove()`方法，如果用list的`remove()`方法同样会报上面提到的`ConcurrentModificationException`错误。

### 4.使用ListIterator 对List遍历时修改，删除

Iterator可以提供容器类的统一遍历方式，并且可以在遍历中使用remove方法删除元素，但是任然不能解决遍历时修改List的需求，`ListIterator`在Iterator的基础上添加的pre系列方法，可以实现反向操作，最重要的是添加了add和set方法，可以实现遍历List的时候同时进行添加，修改的操作。

```java
import java.util.ArrayList;
import java.util.List;
import java.util.ListIterator;
 
import org.junit.Test;
 
public class AppTest {
	@Test
	public void ListTest() {
		boolean doExceptionTest = false;
		List<Phone> phones = new ArrayList<Phone>();
		phones.add(new Phone("111"));
		phones.add(new Phone("183"));
		phones.add(new Phone("182"));
		phones.add(new Phone("185"));
		
		System.out.println("原始数据");
		for(Phone phoneElem:phones) {
			System.out.println(phoneElem.getNumber());
		}
		
		ListIterator<Phone> phoneIterator = phones.listIterator();
		while(phoneIterator.hasNext()){
			Phone tmpPhone = phoneIterator.next();
			if(tmpPhone.getNumber().equals("111")) {
				tmpPhone.setNumber("666");
				phoneIterator.set(tmpPhone);
				phoneIterator.add(new Phone("132"));	// use ListIterator add item is ok
			}
			if(tmpPhone.getNumber().equals("183")) {
				phoneIterator.remove();
			}
			
			if(doExceptionTest) {
				phones.add(new Phone("170"));  // throw java.util.ConcurrentModificationException
			}
		}
		
		System.out.println("\n修改后正向遍历：");
		for(Phone phoneElem:phones) {
			System.out.println(phoneElem.getNumber());
		}
		
		System.out.println("\n修改后反向遍历：");
		//反向遍历
		while(phoneIterator.hasPrevious()) {
			System.out.println(phoneIterator.previousIndex() + ": "+phoneIterator.previous().getNumber());
		}
	}
    
}
 
class Phone {
	private String number;
	
	public Phone(){}
	
	public Phone(String number) {
		this.number = number;		
	}
 
	public String getNumber() {
		return number;
	}
 
	public void setNumber(String number) {
		this.number = number;
	}
}
```

