# Java的线程

## 线程的创建于启动

### 各种创建的方法

#### 继承Thread类创建线程类

通过继承Thread类创建线程类的具体步骤和具体代码如下：

   • 定义一个继承Thread类的子类，并重写该类的`run()`方法；

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

   • 定义Runnable接口的实现类，并重写该接口的`run()`方法；

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

一般情况下是配合ExecutorService来使用的，在ExecutorService接口中声明了若干个`submit()`方法的重载版本：

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

- cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数`mayInterruptIfRunning`表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论`mayInterruptIfRunning`为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若`mayInterruptIfRunning`设置为true，则返回true，若`mayInterruptIfRunning`设置为false，则返回false；如果任务还没有执行，则无论`mayInterruptIfRunning`为true还是false，肯定返回true。
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

### 线程中断方法`interrupt()`的使用

中断在java中主要有3个方法，`interrupt()`, `isInterrupted()`和`interrupted()`。

- interrupt()，在一个线程中调用另一个线程的interrupt()方法，即会向那个线程发出信号——线程中断状态已被设置。至于那个线程何去何从，由具体的代码实现决定。不能中断在运行中的线程，它只能改变中断状态而已。线程中不中断不一定。
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

#### 线程的interrupt与线程的阻塞

在Thread中，有一些耗时操作，比如`sleep()`、`join()`、`wait()`等，都会在执行的时候check interrupt的状态，一旦检测到为true，立刻抛出`InterruptedException`。线程的`interrupt()`调用不管是在该线程的阻塞方法调用前或调用后，都会导致该线程抛出`InterruptedException`最终导致线程停止运行。

举例：

**在阻塞方法调用前**

```java
public class InterruptTest {

    public static class TestThread extends Thread {
        public volatile boolean go = false;
        @Override
        public void run() {
            test();
        }

        private synchronized void test() {
            System.out.println("running");
            while (!go) {
            }
            try {
                if (isInterrupted()) {
                    System.out.println("Interrupted");
                }
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
                System.out.println("InterruptedException");
            }
        }
    }
    
    public static void main(String[] args) {
        TestThread thread = new TestThread();
        thread.start();
        thread.interrupt();
        thread.go = true;
    }
}
```

结果：

> running
> Interrupted
> java.lang.InterruptedException
> 	at java.lang.Object.wait(Native Method)
> 	at java.lang.Object.wait(Object.java:502)
> 	at cn.mdmbct.testdemo.thread.InterruptTest TestThread.test(InterruptTest.java:18)
> 	at cn.mdmbct.testdemo.thread.InterruptTest$TestThread.run(InterruptTest.java:7)
> InterruptedException

**在阻塞方法调用后**

```java
public class InterruptTest2 {
    public static class TestThread extends Thread{
        public volatile boolean go = false;
        @Override
        public void run(){
            test();
        }
        private synchronized void test(){
            System.out.println("running");
            try {
                if(isInterrupted()){
                    System.out.println("Interrupted");
                }
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
                System.out.println("InterruptedException");
            }
        }
    }

    public static void main(String[] args) {
        TestThread thread = new TestThread();
        thread.start();
        try {
            Thread.currentThread().sleep(2000);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        thread.interrupt();
    }
}
```

结果：

> running
> java.lang.InterruptedException
> 	at java.lang.Object.wait(Native Method)
> 	at java.lang.Object.wait(Object.java:502)
> 	at cn.mdmbct.testdemo.thread.InterruptTest2 TestThread.test(InterruptTest2.java:14)
> 	at cn.mdmbct.testdemo.thread.Inter、ruptTest2$TestThread.run(InterruptTest2.java:6)
> InterruptedException

Java中凡是抛出InterruptedException的方法（再加上Thread.interrupted()），都会在抛异常的时候，将interrupt flag重新置为false。

这也就是说，当一个线程B被中断的时候（比如正在`sleep()`），它会结束sleep状态，抛出`InterruptedException`，并将interrupt flag置为false。这也就意味着，此时再去检查线程B的interrupt flag的状态，它是false，不能证明它被中断了，现在唯一能证明当前线程B被中断的证据就是我们现在catch到的`InterruptedException`。如果我们不负责任地直接把这个`InterruptedException`扔掉了，那么没有人知道刚刚发生了中断，没有人知道刚刚有另一个线程想要让线程B停下来，这是不符合程序的目的的：别的线程想让它停下来，而它直接忽略了这个操作。

#### 处理InterruptedException

##### 传递InterruptedException

避开这个异常是最简单明智的做法：直接将异常传递给调用者。这有两种实现方式：

1. 不捕获该异常，在该方法上声明会`throws InterruptedException`；
2. 捕获该异常，做一些操作，然后再**原封不动地**抛出该异常。

错误示范：

```java
try {
	Thread.sleep(100);
} catch (InterruptedException e) {
	e.printStackTrace();
	throw new RuntimeException(e);
}
```

这一通操作之后，线程还活着，并且只给上层调用者一个`RuntimeException`，这是不对的。我们必须告诉上层调用者有人想中断这个线程，至于上层怎么做，就不归我们管了。如果一个caller调用的方法可能会抛出`InterruptedException`异常，那么这个caller需要考虑怎么处理这个异常。

##### 恢复中断状态

这里的恢复中断状态指的是，既然该线程的interrupt flag在抛出`InterruptedException`的时候被置为了false，那么们再重新置为true就好了，告诉后面需要check flag的人，该线程被中断了。这样中断信息不会丢失。通过`Thread.currentThread().interrupt()`方法，将该线程的interrupt flag重新置为true。

```java
try {
	Thread.sleep(100);
} catch (InterruptedException e) {
	e.printStackTrace();
	Thread.currentThread().interrupt();
}
```

#### Demo

##### 传递InterruptedException，不捕获异常，直接抛给调用者

当主线程发出interrupt信号的时候，子线程的`sleep()`被中断，抛出`InterruptedException`。

`sleepBabySleep()`不处理`sleep()`抛出的该异常，直接交到上级caller。上级caller，即`doAPseudoHeavyWeightJob()`也不处理，继续交给上级caller，最后直接整个线程挂了。也相当于成功退出了线程。

```java
package example.thread.interrupt;

public class InterruptDemo1 extends Thread {

    @Override
    public void run() {
        try {
            doAPseudoHeavyWeightJob();
        } catch (InterruptedException e) {
            System.out.println("=.= I(a thread) am dying...");
            e.printStackTrace();
        }
    }

    private void doAPseudoHeavyWeightJob() throws InterruptedException {

        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            System.out.println(i);
            if (i > 10) {
                Thread.currentThread().interrupt();
            }
            // quit if another thread let me interrupt
            if (Thread.currentThread().isInterrupted()) {
                System.out.println("Thread interrupted. Exiting...");
                break;
            } else {
                sleepBabySleep();
            }
        }
    }

    private void sleepBabySleep() throws InterruptedException {
            Thread.sleep(1000);
            System.out.println("Slept for a while!");
    }

    public static void main(String[] args) throws InterruptedException {
        InterruptDemo1 thread = new InterruptDemo1();
        thread.start();
        Thread.sleep(5000);
        // let me interrupt
        thread.interrupt();
        System.out.println("IN MAIN:" + thread.isInterrupted());
    }
}

```

结果：

> 0
> Slept for a while!
> 1
> Slept for a while!
> 2
> Slept for a while!
> 3
> Slept for a while!
> 4
> =.= I(a thread) am dying...
> IN MAIN:false
> java.lang.InterruptedException: sleep interrupted
> 	at java.lang.Thread.sleep(Native Method)
> 	at cn.mdmbct.testdemo.thread.InterruptDemo1.sleepBabySleep(InterruptDemo1.java:47)
> 	at cn.mdmbct.testdemo.thread.InterruptDemo1.doAPseudoHeavyWeightJob(InterruptDemo1.java:41)
> 	at cn.mdmbct.testdemo.thread.InterruptDemo1.run(InterruptDemo1.java:22)

##### 恢复中断状态

当主线程发出interrupt信号的时候，子线程的`sleep()`被中断，抛出`InterruptedException`。

`sleepBabySleep()`在处理该异常的时候，重新设置interrupt flag为true，则子线程在检测中断flag的时候，成功退出线程。（当然，如果子线程始终不检查是否被中断了，也永远不会退出。所以我们在做一个很耗时的操作时，应该有觉悟检查中断状态，以便收到中断信号时退出。）

```java
package cn.mdmbct.testdemo.thread;

public class InterruptDemo2 extends Thread {

    @Override
    public void run() {
        doAPseudoHeavyWeightJob();
    }

    private void doAPseudoHeavyWeightJob() {

        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            System.out.println(i);
            if (i > 10) {
                Thread.currentThread().interrupt();
            }
            // quit if another thread let me interrupt
            if (Thread.currentThread().isInterrupted()) {
                System.out.println("Thread interrupted. Exiting...");
                break;
            } else {
                sleepBabySleep();
            }
        }
    }

    private void sleepBabySleep() {
        try {
            Thread.sleep(1000);
            System.out.println("Slept for a while!");
        } catch (InterruptedException e) {
            System.out.println("Interruption happens...");
            Thread.currentThread().interrupt();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        InterruptDemo2 thread = new InterruptDemo2();
        thread.start();
        Thread.sleep(5000);
        // let me interrupt
        thread.interrupt();
        System.out.println("IN MAIN:" + thread.isInterrupted());
    }
}
```

结果：

> 0
> Slept for a while!
> 1
> Slept for a while!
> 2
> Slept for a while!
> 3
> Slept for a while!
> 4
> Slept for a while!
> 5
> Thread interrupted. Exiting...
> IN MAIN:true

当然，如果子线程在耗时操作doAPseudoHeavyWeightJob()里始终不检查是否被中断了，也永远不会退出

### 线程同步方法`join()`的使用

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

## 线程池

