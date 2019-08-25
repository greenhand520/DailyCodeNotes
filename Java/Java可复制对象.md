# 场景

如下代码，将player添加到whitePlayers和blackPlayers中，但是添加时分别设置player的StoneType属性，如果向下面这段代码的话，whitePlayers与blackPlayers中的对象的StoneType属性值都是StoneType.STONE_WHITE。达不到设置StoneType不一样的目的。

将player对象完整的复制一份，再只改变StoneType属性值，再添加到whitePlayers中即可解决。

```java
  public void addPlayers(List<Player> players) {

        for (Player player : players) {
            String name = player.getName();
            if (!name.equals(Strings.PLAYER_HUMAN) && !name.equals(Strings.PLAYER_FOOL)) {
                player.setStoneType(StoneType.STONE_BLACK);
                this.blackPlayers.add(player);
                this.allPlayers.add(player);
                player.setStoneType(StoneType.STONE_WHITE);
                this.whitePlayers.add(player);
                this.allPlayers.add(player);
            }
        }
    }
```

# 对象复制方法

### 将A对象的值分别通过set方法加入B对象中

使用略

### 通过重写`java.lang.Object`类中的方法clone()

先介绍一下两种不同的克隆方法，**浅克隆(ShallowClone)**和**深克隆(DeepClone)**。在Java语言中，数据类型分为值类型（基本数据类型）和引用类型，值类型包括int、double、byte、boolean、char等简单数据类型，引用类型包括类、接口、数组等复杂类型。浅克隆和深克隆的主要区别在于是否支持引用类型的成员变量的复制，下面将对两者进行详细介绍。

#### 浅克隆

##### 原理

在浅克隆中，如果原型对象的成员变量是值类型，将复制一份给克隆对象；如果原型对象的成员变量是引用类型，则将引用对象的地址复制一份给克隆对象，也就是说原型对象和克隆对象的成员变量指向相同的内存地址。

简单来说，在浅克隆中，当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制。
![img](assets/690102-20160727132640216-1387063948.png)

##### 一般步骤

1、被复制的类需要实现`Clonenable`接口（不实现的话在调用clone方法会抛出`CloneNotSupportedException`异常)， 该接口为标记接口(不含任何方法)

2、覆盖`clone()`方法，访问修饰符设为public。方法中调用`super.clone()`方法得到需要的复制对象。（native为本地方法)

```java
class Student implements Cloneable{  
    private int number;  
  
    public int getNumber() {  
        return number;  
    }  
  
    public void setNumber(int number) {  
        this.number = number;  
    }  
      
    @Override  
    public Object clone() {  
        Student stu = null;  
        try{  
            stu = (Student)super.clone();  
        }catch(CloneNotSupportedException e) {  
            e.printStackTrace();  
        }  
        return stu;  
    }  
}  
public class Test {  
    public static void main(String args[]) {  
        Student stu1 = new Student();  
        stu1.setNumber(12345);  
        Student stu2 = (Student)stu1.clone();  
          
        System.out.println("学生1:" + stu1.getNumber());  
        System.out.println("学生2:" + stu2.getNumber());  
          
        stu2.setNumber(54321);  
      
        System.out.println("学生1:" + stu1.getNumber());  
        System.out.println("学生2:" + stu2.getNumber());  
    }  
}  
```

结果：

> 学生1:12345
> 学生2:12345
> 学生1:12345
> 学生2:54321

#### 深克隆

```java
 class Address {  
    private String add;  
  
    public String getAdd() {  
        return add;  
    }  
  
    public void setAdd(String add) {  
        this.add = add;  
    }   
}  
  
class Student implements Cloneable{  
    private int number;  
  
    private Address addr;  
      
    public Address getAddr() {  
        return addr;  
    }  
  
    public void setAddr(Address addr) {  
        this.addr = addr;  
    }  
  
    public int getNumber() {  
        return number;  
    }  
  
    public void setNumber(int number) {  
        this.number = number;  
    }  
      
    @Override  
    public Object clone() {  
        Student stu = null;  
        try{  
            stu = (Student)super.clone();   //浅复制  
        }catch(CloneNotSupportedException e) {  
            e.printStackTrace();  
        }   
        return stu;  
    }  
}  
public class Test {  
      
    public static void main(String args[]) {  
          
        Address addr = new Address();  
        addr.setAdd("杭州市");  
        Student stu1 = new Student();  
        stu1.setNumber(123);  
        stu1.setAddr(addr);  
          
        Student stu2 = (Student)stu1.clone();  
          
        System.out.println("学生1:" + stu1.getNumber() + ",地址:" + stu1.getAddr().getAdd());  
        System.out.println("学生2:" + stu2.getNumber() + ",地址:" + stu2.getAddr().getAdd());  
          
        addr.setAdd("西湖区");  
          
        System.out.println("学生1:" + stu1.getNumber() + ",地址:" + stu1.getAddr().getAdd());  
        System.out.println("学生2:" + stu2.getNumber() + ",地址:" + stu2.getAddr().getAdd());  
    }  
}
```

结果：

> 学生1:123,地址:杭州市
> 学生2:123,地址:杭州市
> 学生1:123,地址:西湖区
> 学生2:123,地址:西湖区

怎么两个学生的地址都改变了？

原因是**浅复制只是复制了addr变量的引用，并没有真正的开辟另一块空间，将值复制后再将引用返回给新对象。**

为了达到真正的复制对象，而不是纯粹引用复制。我们需要将Address类可复制化（Address实现`Cloneable`接口），并且修改clone方法，完整代码如下：

```java
class Address implements Cloneable {  
    private String add;  
  
    public String getAdd() {  
        return add;  
    }  
  
    public void setAdd(String add) {  
        this.add = add;  
    }  
      
    @Override  
    public Object clone() {  
        Address addr = null;  
        try{  
            addr = (Address)super.clone();  
        }catch(CloneNotSupportedException e) {  
            e.printStackTrace();  
        }  
        return addr;  
    }  
}  
  
class Student implements Cloneable{  
    private int number;  
  
    private Address addr;  
      
    public Address getAddr() {  
        return addr;  
    }  
  
    public void setAddr(Address addr) {  
        this.addr = addr;  
    }  
  
    public int getNumber() {  
        return number;  
    }  
  
    public void setNumber(int number) {  
        this.number = number;  
    }  
      
    @Override  
    public Object clone() {  
        Student stu = null;  
        try{  
            stu = (Student)super.clone();   //浅复制  
        }catch(CloneNotSupportedException e) {  
            e.printStackTrace();  
        }  
        stu.addr = (Address)addr.clone();   //深度复制  
        return stu;  
    }  
}  
public class Test {  
      
    public static void main(String args[]) {  
          
        Address addr = new Address();  
        addr.setAdd("杭州市");  
        Student stu1 = new Student();  
        stu1.setNumber(123);  
        stu1.setAddr(addr);  
          
        Student stu2 = (Student)stu1.clone();  
          
        System.out.println("学生1:" + stu1.getNumber() + ",地址:" + stu1.getAddr().getAdd());  
        System.out.println("学生2:" + stu2.getNumber() + ",地址:" + stu2.getAddr().getAdd());  
          
        addr.setAdd("西湖区");  
          
        System.out.println("学生1:" + stu1.getNumber() + ",地址:" + stu1.getAddr().getAdd());  
        System.out.println("学生2:" + stu2.getNumber() + ",地址:" + stu2.getAddr().getAdd());  
    }  
}
```

在深克隆中，无论原型对象的成员变量是值类型还是引用类型，都将复制一份给克隆对象，深克隆将原型对象的所有引用对象也复制一份给克隆对象。

简单来说，在深克隆中，除了对象本身被复制外，对象所包含的所有成员变量也将复制。

![](assets/690102-20160727132838528-120069275.png)

### 通过org.apache.commons中的工具类BeanUtils和PropertyUtils进行对象复制

这种写法无论多少种属性都只需要一行代码搞定，很方便吧！除BeanUtils外还有一个名为PropertyUtils的工具类，它也提供copyProperties()方法，作用与BeanUtils的同名方法十分相似，主要的区别在于BeanUtils提供类型转换功能，即发现两个JavaBean的同名属性为不同类型时，在支持的数据类型范围内进行转换，而PropertyUtils不支持这个功能，但是速度会更快一些。在实际开发中，BeanUtils使用更普遍一点，犯错的风险更低一点。

```java
Student stu1 = new Student();  
stu1.setNumber(12345);  
Student stu2 = new Student(); 
BeanUtils.copyProperties(stu2,stu1);
```

### 通过序列化实现对象的复制

序列化就是将对象写到流的过程，写到流中的对象是原有对象的一个拷贝，而原对象仍然存在于内存中。通过序列化实现的拷贝不仅可以复制对象本身，而且可以复制其引用的成员对象，因此通过序列化将对象写到一个流中，再从流里将其读出来，可以实现深克隆。需要注意的是能够实现序列化的对象其类必须实现Serializable接口，否则无法实现序列化操作。

### 使用Cglib的BeanCopier实现Bean的拷贝

性能比`BeanUtils`的`copyProperties()`性能好

```java
private static BeanCopier copy = BeanCopier.create(Bean.class, Bean2.class, false);
 
public MemberVO copy(MemberDO member){
    MemberVO memberVO = new MemberVO();
    copy.copy(member, memberVO, null); 
    return memberVO;
}
```

