# Python学习笔记

## 一、线程

python是在一个时刻让一个线程执行一个任务，多线程的过程中是在不停地切换各个线程

```python
import threading

threading.active_count()	#当前激活的线程数
threading.enumerate()	#所有的线程，type:list
threading.current_thread()	#当前的线程
```

### 创建线程

```python
def thread_job():
    #todo: thread_job...
    
   
added_thread = threading.Thread(target=thread_job, name=thread1)
added_thread.start()
```

target是线程执行的函数，这里可以看成是引用不带括号，name是这个添加的线程的名字

别忘记通过`start()`启动线程

### join

thread.join()后面的各种指令函数线程都必须等到调用这个函数的线程执行完毕后才能继续执行。

如果这个线程没有调用这个函数，这个线程没有执行完，其他线程可以去执行自己的部分而不必等到线程执行完毕。

```python
added_thread = threading.Thread(target=thread_job, name=thread1)
added_thread.start()
added_thread.join()
```

### 多线程与队列

队列的函数无法有返回值。因此想到创建一个队列，讲各个线程的运算结果放入队里中，等计算完毕后，在主线程中将队列中的各个值取出放入list中打印出来。

示例程序：

计算list各个元素的平方

```python
# -*- coding: utf-8 -*-
import threading
import queue


# 计算列表每个元素的平方，并将结果放在队列中
def element_square_to_queue(list, queue):
    for i in range(len(list)):
        list[i] = list[i] ** 2
    queue.put(list)


def multithread():
    q = queue.Queue()
    threads = []
    data = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
    for i in range(3):
        t = threading.Thread(target=element_square_to_queue, args=(data[i], q))  # args是传入target调用的函数所需的参数的
        t.start()
        threads.append(t)	#将队列放入list中，然后让每个队列调用join()函数，等到全部线程执行完毕后再让主线程执行
    for t in threads:	
        t.join()
    results = []
    for _ in range(3):
        results.append(q.get())
    print(results)

if __name__ == '__main__':
    multithread()
```

运行结果：

> [[1, 4, 9], [16, 25, 36], [49, 64, 81]]

**注：**

有时候没有使用join()运算结果也没有影响，那是因为电脑的处理器运行速度太快导致的。当运算量太大时，没使用join()和使用join()执行结果差异还是很大的。

### 线程的lock

通过threading.Lock()来锁住共享内存中的数据防止该数据被其线程修改，如一个全局变量

示例程序：

```python
# -*- coding: utf-8 -*-
import threading


def thread1_job():
    global A
    global lock
    with lock:
        for _ in range(10):
            A += 2;
            print("thread1 A: ", A)


def thread2_job():
    global A
    global lock
    lock.acquire()
    for _ in range(10):
        A -= 2;
        print("thread2 A: ", A)
        lock.release()


if __name__ == '__main__':
    A = 0
    lock = threading.Lock()
    thread1 = threading.Thread(target=thread1_job())
    thread2 = threading.Thread(target=thread2_job())
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
```

lock.acquire()与lock.release()配套使用，和with lock：的效果一样。

## 二、进程

### 创建进程

和创建线程类似

```python
# -*- coding: utf-8 -*-
import multiprocessing as mp

def process_job(a, b):
    print(a + b)

if __name__ == '__main__':
    process1 = mp.Process(target=process_job, args=(1, 2)， name=‘process1’)
    process1.start()
```

### 多进程与对列

将各个进程的运算结果放入到queue中，等到各进程运行完毕后从对列中取出运算结果。

**这里用的队列不是queue中的，而是multiprocessing模块中的。**

```python
import multiprocessing as mp

def job(q):
    res = 0
    for i in range(1000):
        res += i+i**2+i**3
    q.put(res) # queue

if __name__ == '__main__':
    q = mp.Queue()
    p1 = mp.Process(target=job, args=(q,))
    p2 = mp.Process(target=job, args=(q,))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    res1 = q.get()
    res2 = q.get()
    print(res1+res2)
```

### 进程池

**apply是阻塞式的。**

等待当前子进程执行完毕后，在执行下一个进程。 

**apply_async是异步非阻塞式的。**

多使用这个

不用等待当前进程执行完毕，随时根据系统调度来进行进程切换。完全没有等待子进程执行完毕，主进程就已经执行完毕，并退出程序 。



`mp.Pool(process=2).map(target=process_job, range(10))`中target所引用的函数是可以有返回值的，`range(10)`是传入引用的函数的参数，Pool()中process=2是使用的处理器的核心数，如果不写默认是使用全部核心。

```python
import multiprocessing as mp

def job(x):
    return x*x

def multicore():
    pool = mp.Pool(processes=2)
    res = pool.map(job, range(10))
    print(res)
    res = pool.apply_async(job, (2,))	#运算一个值
    print(res.get())
    multi_res =[pool.apply_async(job, (i,)) for i in range(10)]		#运算多个
    print([res.get() for res in multi_res])

if __name__ == '__main__':
    multicore()
```

### 共享内存

共享内存变量的定义

```python
import multiprocessing as mp
value = mp.Value('d', 1)	#第一个参数是后面值的种类，具体有哪几种参见下面的表
array = mp.Array('i', [[1, 2, 3]])		#只能是一维的
```

来自[这里](https://docs.python.org/3.6/library/array.html)

| Type code | C Type             | Python Type       | Minimum size in bytes | Notes |
| --------- | ------------------ | ----------------- | --------------------- | ----- |
| `'b'`     | signed char        | int               | 1                     |       |
| `'B'`     | unsigned char      | int               | 1                     |       |
| `'u'`     | Py_UNICODE         | Unicode character | 2                     | (1)   |
| `'h'`     | signed short       | int               | 2                     |       |
| `'H'`     | unsigned short     | int               | 2                     |       |
| `'i'`     | signed int         | int               | 2                     |       |
| `'I'`     | unsigned int       | int               | 2                     |       |
| `'l'`     | signed long        | int               | 4                     |       |
| `'L'`     | unsigned long      | int               | 4                     |       |
| `'q'`     | signed long long   | int               | 8                     | (2)   |
| `'Q'`     | unsigned long long | int               | 8                     | (2)   |
| `'f'`     | float              | float             | 4                     |       |
| `'d'`     | double             | float             | 8                     |       |

1. The `'u'` type code corresponds to Python’s obsolete unicode character ([`Py_UNICODE`](https://docs.python.org/3.6/c-api/unicode.html#c.Py_UNICODE) which is `wchar_t`). Depending on the platform, it can be 16 bits or 32 bits.

   `'u'` will be removed together with the rest of the [`Py_UNICODE`](https://docs.python.org/3.6/c-api/unicode.html#c.Py_UNICODE) API.

   Deprecated since version 3.3, will be removed in version 4.0.

2. The `'q'` and `'Q'` type codes are available only if the platform C compiler used to build Python supports C `long long`, or, on Windows, `__int64`.

### 进程的lock

没有使用lock：

```python
mport multiprocessing as mp
import time

def job(v, num, l):
    # l.acquire()
    for _ in range(10):
        time.sleep(0.1)
        v.value += num
        print(v.value)
    # l.release()

def multicore():
    l = mp.Lock()
    v = mp.Value('i', 0)
    p1 = mp.Process(target=job, args=(v, 1, l))
    p2 = mp.Process(target=job, args=(v, 3, l))
    p1.start()
    p2.start()
    p1.join()
    p2.join()

if __name__ == '__main__':
    multicore()
```

运算结果：杂乱，没有按着顺序

> 1
> 1
> 4
> 5
> 8
> 8
> 11
> 12
> 15
> 15
> 16
> 16
> 17
> 17
> 18
> 18
> 19
> 19
> 22
> 22

使用lock：

```python
# -*- coding: utf-8 -*-
import multiprocessing as mp
import queue
import time


def job(v, num, l, q):
    l.acquire()
    for _ in range(10):
        time.sleep(0.1)
        v.value += num
        q.put(v.value)
        # print(v.value)
    l.release()


def multicore():
    l = mp.Lock()
    v = mp.Value('i', 0)
    q = mp.Queue()
    res = []
    p1 = mp.Process(target=job, args=(v, 1, l, q))
    p2 = mp.Process(target=job, args=(v, 3, l, q))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    while not q.empty():
        res.append(q.get())
    print(res)


if __name__ == '__main__':
    multicore()

```

运算结果：按着顺序

> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 13, 16, 19, 22, 25, 28, 31, 34, 37, 40]

## 三、队列

### 先进先出队列（FIFO）

**class Queue.Queue(maxsize=0)**

FIFO即First in First Out,先进先出。Queue提供了一个基本的FIFO容器，使用方法很简单,maxsize是个整数，指明了队列中能存放的数据个数的上限。一旦达到上限，插入会导致阻塞，直到队列中的数据被消费掉。如果maxsize小于或者等于0，队列大小没有限制。

```python
import queue

q = Queue.Queue()

for i in range(4):
    q.put(i)

while not q.empty():
    print q.get()
```

运行结果：

> 0
>
> 1
>
> 2
>
> 3



### 后进先出队列（LIFO）

**class Queue.LifoQueue(maxsize=0)**

LIFO即Last in First Out,后进先出。与栈的类似，使用也很简单，maxsize用法同上。

```python
import queue

q = Queue.LifoQueue()

for i in range(4):
    q.put(i)

while not q.empty():
    print q.get()
```

运行结果：

> 3
>
> 2
>
> 1
>
> 0

### 常用的方法

#### **task_done()**

意味着之前入队的一个任务已经完成。由队列的消费者线程调用。每一个get()调用得到一个任务，接下来的task_done()调用告诉队列该任务已经处理完毕。

如果当前一个join()正在阻塞，它将在队列中的所有任务都处理完时恢复执行（即每一个由put()调用入队的任务都有一个对应的task_done()调用）。

#### **join()**

阻塞调用线程，直到队列中的所有任务被处理掉。

只要有数据被加入队列，未完成的任务数就会增加。当消费者线程调用task_done()（意味着有消费者取得任务并完成任务），未完成的任务数就会减少。当未完成的任务数降到0，join()解除阻塞。

#### **put(item[, block[, timeout]])**

将item放入队列中。

1. 如果可选的参数block为True且timeout为空对象（默认的情况，阻塞调用，无超时）。
2. 如果timeout是个正整数，阻塞调用进程最多timeout秒，如果一直无空空间可用，抛出Full异常（带超时的阻塞调用）。
3. 如果block为False，如果有空闲空间可用将数据放入队列，否则立即抛出Full异常

其非阻塞版本为`put_nowait`等同于`put(item, False)`

#### **get([block[, timeout]])**

从队列中移除并返回一个数据。block跟timeout参数同`put`方法。其非阻塞方法为｀get_nowait()｀相当与`get(False)`

#### empty()

如果队列为空，返回True，反之返回False