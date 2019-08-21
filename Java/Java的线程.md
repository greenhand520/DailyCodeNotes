# Java的线程

## 线程的创建于启动

### 各种创建的方法

#### 继承Thread类创建线程类

通过继承Thread类创建线程类的具体步骤和具体代码如下：

   • 定义一个继承Thread类的子类，并重写该类的run()方法；

   • 创建Thread子类的实例，即创建了线程对象；

   • 调用该线程对象的start()方法启动线程。

```java
class SomeThead extends Thraad   { 
    public void run()   { 
     //do something here  
    }  
 } 
 
public static void main(String[] args){
 SomeThread oneThread = new SomeThread();      
 oneThread.start(); 
}
```

#### 实现Runnable接口创建线程类

通过实现Runnable接口创建线程类的具体步骤和具体代码如下：

   • 定义Runnable接口的实现类，并重写该接口的run()方法；

   • 创建Runnable实现类的实例，并以此实例作为Thread的target对象，即该Thread对象才是真正的线程对象。

```java
class SomeRunnable implements Runnable   { 
  public void run()   { 
  //do something here  
  }  
} 
Runnable oneRunnable = new SomeRunnable();   
Thread oneThread = new Thread(oneRunnable);   
oneThread.start();
```

  上面两种方式创建的线程执行完后无法得到执行结果。

#### 实现Callable接口

Callable位于java.util.concurrent包下，它也是一个接口，在它里面也只声明了一个方法，只不过这个方法叫做call()：

```java
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

这是一个泛型接口，call()函数返回的类型就是传递进来的V类型。

怎么使用Callable：

一般情况下是配合ExecutorService来使用的，在ExecutorService接口中声明了若干个submit方法的重载版本：

```java
<T> Future<T> submit(Callable<T> task);
<T> Future<T> submit(Runnable task, T result);
Future<?> submit(Runnable task);
```

第一个submit方法里面的参数类型就是Callable。暂时只需要知道Callable一般是和ExecutorService配合来使用的，具体的使用方法讲在后面讲述。一般情况下我们使用第一个submit方法和第三个submit方法，第二个submit方法很少使用。

#### Future

Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过get方法获取执行结果，该方法会阻塞直到任务返回结果。

Future类位于java.util.concurrent包下，它是一个接口：

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

在Future接口中声明了5个方法，下面依次解释每个方法的作用：

- cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。
- isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
- isDone方法表示任务是否已经完成，若任务完成，则返回true；
- get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
- get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。

　　也就是说Future提供了三种功能：

　　1）判断任务是否完成；

　　2）能够中断任务；

　　3）能够获取任务执行结果。

　　因为Future只是一个接口，所以是无法直接用来创建对象使用的，因此就有了下面的FutureTask。

#### FutureTask

FutureTask的实现：

```java
public class FutureTask<V> implements RunnableFuture<V>
```

　FutureTask类实现了RunnableFuture接口，RunnableFuture接口的实现：

FutureTask提供了2个构造器：

```java
public FutureTask(Callable<V> callable) {
}
public FutureTask(Runnable runnable, V result) {
}
```

FutureTask是Future接口的一个唯一实现类。

### Future模式的缺点

​    （1）实现了异步获取执行结果的需求，但是没有提供通知，无法知道任务什么时候完成；

​    （2）get方法获取结果的时候，会进入等待阻塞状态，这时候又变成同步操作，如果使用isDone循环判断任务是否完成，会耗费cpu资源。

### CompletableFuture



### 使用实例

#### 使用Callable+Future获取执行结果

```java
public class Test {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        Future<Integer> result = executor.submit(task);
        executor.shutdown();
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+result.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```

运行结果

> 子线程在进行计算
> 主线程在执行任务
> task运行结果4950
> 所有任务执行完毕

#### 使用Callable+FutureTask获取执行结果

```java
public class Test {
    public static void main(String[] args) {
        //第一种方式
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        executor.submit(futureTask);
        executor.shutdown();
         
        //第二种方式，注意这种方式和第一种方式效果是类似的，只不过一个使用的是ExecutorService，一个使用的是Thread
        /*Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        Thread thread = new Thread(futureTask);
        thread.start();*/
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```

如果为了可取消性而使用 Future 但又不提供可用的结果，则可以声明 Future<?> 形式类型、并返回 null 作为底层任务的结果。

## 线程的各种方法

### 线程中断方法interrupt（）的使用

中断在java中主要有3个方法，interrupt(),isInterrupted()和interrupted()。

- interrupt()，在一个线程中调用另一个线程的interrupt()方法，即会向那个线程发出信号——线程中断状态已被设置。至于那个线程何去何从，由具体的代码实现决定。不能中断在运行中的线程，它只能改变中断状态而已。
- isInterrupted()，用来判断当前线程的中断状态(true or false)。
- interrupted()是个Thread的static方法，用来恢复中断状态。

```java
public class InterruptDemo implements Runnable{

    public static void main(String[] args) throws InterruptedException {
        Thread testThread = new Thread(new InterruptDemo(),"InterruptionInJava");
        //start thread
        testThread.start();
        Thread.sleep(1000);
        //interrupt thread
        testThread.interrupt();
//        InterruptionInJava.on = true;
        System.out.println("main end");

    }

    public void run() {
        while(true){
            if(Thread.currentThread().isInterrupted()){
                System.out.println("Yes,I am interruted,but I am still running");
//				return;
            }else{
                System.out.println("not yet interrupted");
            }
        }
    }
}
```

运行结果：

> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
> Yes,I am interruted,but I am still running
>
> ......

如果在19行添加return，则可以终止线程；

第10行的开关也可以控制线程的中断。

### 线程同步方法join（）的使用

Thread类中的join方法的主要作用就是同步，它可以使得线程之间的并行执行变为串行执行。具体看代码：

join的意思是使得放弃当前线程的执行，并返回对应的线程，例如下面代码的意思就是：

程序在main线程中调用t1线程的join方法，则main线程放弃cpu控制权，并返回t1线程继续执行直到线程t1执行完毕。所以结果是t1线程执行完后，才到主线程执行，相当于在main线程中同步t1线程，t1执行完了，main线程才有执行的机会。

即在A线程中调用了B线程的join()方法时，表示只有当B线程执行完毕时，A线程才能继续执行。

```java
public class JoinDemo {
    public static void main(String [] args) throws InterruptedException {
        ThreadJoinTest t1 = new ThreadJoinTest("小明");
        ThreadJoinTest t2 = new ThreadJoinTest("小东");
        t1.start();
        t1.join();
        t2.start();

    }

}
class ThreadJoinTest extends Thread{
    public ThreadJoinTest(String name){
        super(name);
    }
    @Override
    public void run(){
        for(int i=0;i<1000;i++){
            System.out.println(this.getName() + ":" + i);
        }
    }
}
```

上面程序结果是先打印完小明线程，再打印小东线程。