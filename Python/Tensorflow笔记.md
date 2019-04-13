## tf.get_variable()与tf.Variable()的区别:

tf.Variable() 每次调用都会产生一个新的变量，他会自动检测命名冲突并自行处理，变量名称是一个可选参数，例如： 

```python
a1 = tf.Variable(tf.random_normal(shape=[2, 3], mean=0, stddev=1), name='a1')
a2 = tf.Variable(tf.random_normal(shape=[2, 3], mean=0, stddev=1), name='a1')
print(a1.name)
print(a2.name)
print(a1==a2)

>>> f1/a1:0
>>> f1/a1_1:0
>>> False    # a2实际变成了a1_1并且和a1 不是同一个变量
```

而tf.get_variable()则不同，遇到重命名的变量创建且变量名没有设置成共享变量（所谓的共享是指在同一参数空间下的共享，参数空间名称不一样就不能共享了）时，就会报错 ；相对应的，变量名称这个参数是必填参数，tf.get_variable()会根据这个参数去创建或者获取变量。

## tf.variable_scope()与tf.name_scope()的区别：

tf.name_scope()主要用于管理图中各种op，而tf.variable_scope()主要用于管理图中变量的名字，在 tf.name_scope下时，tf.get_variable()创建的变量名不受 name_scope 的影响（不受它的约束）

```python
import tensorflow as tf
 
with tf.variable_scope('f2'):
    #a1 = tf.get_variable(name='a1', shape=[1], initializer=tf.constant_initializer(1))
    a1 = tf.Variable(tf.random_normal(shape=[2, 3], mean=0, stddev=1), name='a1')
    a2 = tf.Variable(tf.random_normal(shape=[2, 3], mean=0, stddev=1), name='a1')
    a5 = tf.get_variable(name='a1', shape=[1], initializer=tf.constant_initializer(1))
 
with tf.variable_scope('f2', reuse=True):
    a3 = tf.get_variable(name='a1', shape=[1], initializer=tf.constant_initializer(1))
 
    a4 = tf.Variable(tf.random_normal(shape=[2, 3], mean=0, stddev=1), name='a2')
 
print(a1.name)
print(a2.name)
print(a1==a2)
print(a5.name)
print(a3.name)
print(a4.name)

>>> f2/a1:0
>>> f2/a1_1:0
>>> False
>>> f2/a1_2:0
>>> f2/a1_2:0
>>> f2_1/a2:0
```

## tensorflow的损失函数：

### tf.nn.softmax_cross_entropy_with_logits ()





