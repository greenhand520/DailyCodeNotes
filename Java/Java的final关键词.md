final**关键字表示的不可变的**

## 用法

### 修饰类

当用final去修饰一个类的时候，表示这个类不能被继承。

a. 被final修饰的类，final类中的成员变量可以根据自己的实际需要设计为fianl。

b. final类中的成员方法都会被隐式的指定为final方法。将类定义成final后，结果只是禁止被继承。由于禁止了继承，所以一个final类中的所有方法都默认为final。

说明：在自己设计一个类的时候，要想好这个类将来是否会被继承，如果可以被继承，则该类不能使用fianl修饰，在这里呢，一般来说工具类我们往往都会设计成为一个fianl类。在JDK中，被设计为final类的有String、System等。

### 修饰方法

被final修饰的方法不能被重写。

a. 一个类的private方法会隐式的被指定为final方法。

b. 如果父类中有final修饰的方法，那么子类不能去重写。

### 修饰成员变量

a. 必须要赋初始值，而且是只能初始化一次。

b. 被fianl修饰的成员变量赋值，有两种方式：1、直接赋值 2、全部在构造方法中赋初值。

c. 如果修饰的成员变量是基本类型，则表示这个**变量的值不能改变**。

d. 如果修饰的成员变量是一个引用类型，则是说这个引用的**地址的值不能修改**，但是这个引用所指向的对象里面的内容还是可以改变的。如：

```java
class Person {
    String name = "张三";
}

public class FinalDemo {

    public static void main(String[] args) {
        final Person p = new Person();
        p = new Person();   // Error:无法为最终变量p分配值
         p.name = "萧萧弈寒";	// 不会报错
    }
}
```

### 空白final

Java1.1允许创建“空白final”,它们属于特殊字段。尽管被声明为final，但是却未得到一个初始值。即便如此，空白final还是必须在使用之前得到初始化。 现在强行要求对final进行赋值处理，**要么在定义字段时使用一个表达式，要么在每个构造器中。**如：

```java
class Person {}

public class FinalDemo {
    final int i;
    final Person p;

    FinalDemo() {
        i = 1;
        p = new Person();
    }

    FinalDemo(int x) {
        i = x;
        p = new Person();
    }

    public static void main(String[] args) {
        FinalDemo fd = new FinalDemo();
    }
}
```

### 修饰方法参数

很多人都说用final修饰方法参数是防止参数在调用时被修改。个人认为这种说法其实有两种理解：一种是变量的实际值不会被修改，另一种是在方法内部不能被修改。无论是基本参数类型还是引用类型，前一种说法都是错误的。因为Java是值传递。

```java
public class FinalDemo {

    static void f(final int i) {
        i++;    // 无法为final变量赋值，编译错误
    }

    public static void main(String[] args) {
        int x = 10;
        f(x);   // 调用的f方法只是将x的值赋给了i，实际上i和x是两个变量
    }
}
```

```java
class Person {
    String name = "张三";
}

public class FinalDemo {

    public static void main(String[] args) {
        final Person p = new Person();
        changeName(p);
        System.out.println(p.name);
    }

    static void changeName(final Person p) {
        p.name = "萧萧弈寒";
        p = new Person();	// 报错
    }
}
```

final并不能阻止`changeName()`方法改变p的内容,但是无法改变被final修饰的p的地址。



