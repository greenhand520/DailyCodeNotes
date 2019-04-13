keras可同时支持卷积神经网络和循环神经网络，以及两者的组合，可在 CPU 和 GPU 上无缝运行。

## 搭载CNN需要的基本神经网络层

## 神经网络层

输入层、卷积层、激活层、池化层、全连接层

### Conv2D（卷积层）

2D 卷积层 (例如对图像的空间卷积)

该层创建了一个卷积核， 该卷积核对层输入进行卷积， 以生成输出张量。 如果 `use_bias` 为 True， 则会创建一个偏置向量并将其添加到输出中。 最后，如果 `activation` 不是 `None`，它也会应用于输出。 

```python
keras.layers.Conv2D(filters, kernel_size, 
                    strides=(1, 1), 
                    padding='valid', 
                    data_format=None, 
                    dilation_rate=(1, 1), 
                    activation=None, 
                    use_bias=True, 
                    kernel_initializer='glorot_uniform', 
                    bias_initializer='zeros', 
                    kernel_regularizer=None,
                    bias_regularizer=None, 
                    activity_regularizer=None, 
                    kernel_constraint=None, 
                    bias_constraint=None) 
```

当使用该层作为模型第一层时，需要提供 `input_shape` 参数 （整数元组，不包含样本表示的轴），例如， 在 `data_format="channels_last"` 时 ,`input_shape=(128, 128, 3)` 表示 128x128 RGB 图像，此时输入的图形最后一个维度表示通道数。

**参数**

- **filters**: 整数，输出空间的维度 （即卷积中卷积核（滤波器）的输出数量）。
- **kernel_size**: 一个整数，或者 2 个整数表示的元组或列表， 指明卷积核的宽度和高度。 可以是一个整数，为所有空间维度指定相同的值。
- **strides**: 一个整数，或者 2 个整数表示的元组或列表， 指明卷积沿宽度和高度方向的步长。 可以是一个整数，为所有空间维度指定相同的值。 指定任何 stride 值 != 1 与指定 `dilation_rate`值 != 1 两者不兼容。
- **padding**: `"valid"` 或 `"same"` (大小写敏感)。
- **data_format**: 字符串， `channels_last` (默认) 或 `channels_first` 之一，表示输入中维度的顺序。`channels_last` 对应输入尺寸为 `(batch, height, width, channels)`， `channels_first` 对应输入尺寸为 `(batch, channels, height, width)`。 它默认为从 Keras 配置文件 `~/.keras/keras.json` 中 找到的 `image_data_format` 值。 如果你从未设置它，将使用 `channels_last`。
- **dilation_rate**: 一个整数或 2 个整数的元组或列表， 指定膨胀卷积的膨胀率。 可以是一个整数，为所有空间维度指定相同的值。 当前，指定任何 `dilation_rate` 值 != 1 与 指定 stride 值 != 1 两者不兼容。
- **activation**: 要使用的激活函数 (详见 [activations](https://keras.io/zh/activations/))。 如果你不指定，则不使用激活函数 (即线性激活： `a(x) = x`)。
- **use_bias**: 布尔值，该层是否使用偏置向量。
- **kernel_initializer**: `kernel` 权值矩阵的初始化器 (详见 [initializers](https://keras.io/zh/initializers/))。
- **bias_initializer**: 偏置向量的初始化器 (详见 [initializers](https://keras.io/zh/initializers/))。
- **kernel_regularizer**: 运用到 `kernel` 权值矩阵的正则化函数 (详见 [regularizer](https://keras.io/zh/regularizers/))。
- **bias_regularizer**: 运用到偏置向量的正则化函数 (详见 [regularizer](https://keras.io/zh/regularizers/))。
- **activity_regularizer**: 运用到层输出（它的激活值）的正则化函数 (详见 [regularizer](https://keras.io/zh/regularizers/))。
- **kernel_constraint**: 运用到 `kernel` 权值矩阵的约束函数 (详见 [constraints](https://keras.io/zh/constraints/))。
- **bias_constraint**: 运用到偏置向量的约束函数 (详见 [constraints](https://keras.io/zh/constraints/))。

**输入尺寸**

- 如果 data_format='channels_first'， 输入 4D 张量，尺寸为 `(samples, channels, rows, cols)`。
- 如果 data_format='channels_last'， 输入 4D 张量，尺寸为 `(samples, rows, cols, channels)`。

**输出尺寸**

- 如果 data_format='channels_first'， 输出 4D 张量，尺寸为 `(samples, filters, new_rows, new_cols)`。
- 如果 data_format='channels_last'， 输出 4D 张量，尺寸为 `(samples, new_rows, new_cols, filters)`。

由于填充的原因， `rows` 和 `cols` 值可能已更改

### Activation（激活层）

```
keras.layers.Activation(activation)
```

将激活函数应用于输出。

**参数**

- **activation**: 要使用的激活函数的名称 (详见: [activations](https://keras.io/zh/activations/))， 或者选择一个 Theano 或 TensorFlow 操作。

**输入尺寸**

任意尺寸。 当使用此层作为模型中的第一层时， 使用参数 `input_shape` （整数元组，不包括样本数的轴）。

**输出尺寸**

与输入相同。

### MaxPooling2D（池化层）

对于空间数据的最大池化 。

```python
keras.layers.MaxPooling2D(pool_size=(2, 2), strides=None, padding='valid', data_format=None) 
```

**参数**

- **pool_size**: 整数，或者 2 个整数表示的元组， 沿（垂直，水平）方向缩小比例的因数。 （2，2）会把输入张量的两个维度都缩小一半。 如果只使用一个整数，那么两个维度都会使用同样的窗口长度。
- **strides**: 整数，2 个整数表示的元组，或者是 `None`。 表示步长值。 如果是 `None`，那么默认值是 `pool_size`。
- **padding**: `"valid"` 或者 `"same"` （区分大小写）。
- **data_format**: 字符串，`channels_last` (默认)或 `channels_first` 之一。 表示输入各维度的顺序。`channels_last` 代表尺寸是 `(batch, height, width, channels)` 的输入张量， 而 `channels_first` 代表尺寸是 `(batch, channels, height, width)` 的输入张量。 默认值根据 Keras 配置文件 `~/.keras/keras.json` 中的 `image_data_format` 值来设置。 如果还没有设置过，那么默认值就是 "channels_last"。

**输入尺寸**

- 如果 `data_format='channels_last'`: 尺寸是 `(batch_size, rows, cols, channels)` 的 4D 张量
- 如果 `data_format='channels_first'`: 尺寸是 `(batch_size, channels, rows, cols)` 的 4D 张量

**输出尺寸**

- 如果 `data_format='channels_last'`: 尺寸是 `(batch_size, pooled_rows, pooled_cols, channels)` 的 4D 张量
- 如果 `data_format='channels_first'`: 尺寸是 `(batch_size, channels, pooled_rows, pooled_cols)` 的 4D 张量

### Flatten

将输入展平。

不影响批量大小，它表明输入的维度的顺序。此参数的目的是当模型从一种数据格式切换到另一种数据格式时保留权重顺序。 

```python 
keras.layers.Flatten(data_format=None) 
```

data_format与上面的意义一样

```python
model = Sequential()
model.add(Conv2D(64, (3, 3),
                 input_shape=(3, 32, 32), padding='same',))
# 现在：model.output_shape == (None, 64, 32, 32)

model.add(Flatten())
# 现在：model.output_shape == (None, 65536)
```

655536 = 644 * 32 * 32

### Dense（全连接层）

```python
keras.layers.Dense(units, 
                   activation=None, 
                   use_bias=True, 
                   kernel_initializer='glorot_uniform', 
                   bias_initializer='zeros', 
                   kernel_regularizer=None,
                   bias_regularizer=None, 
                   activity_regularizer=None,
                   kernel_constraint=None, 
                   bias_constraint=None)
```

`Dense` 实现以下操作： `output = activation(dot(input, kernel) + bias)` 其中 `activation` 是按逐个元素计算的激活函数，`kernel` 是由网络层创建的权值矩阵，以及 `bias` 是其创建的偏置向量 (只在 `use_bias` 为 `True` 时才有用)。

**注意**: 如果该层的输入的秩大于2，那么它首先被展平然后 再计算与 `kernel` 的点乘。

**参数**

- **units**: 正整数，输出空间维度。
- **activation**: 激活函数 (详见 [activations](https://keras.io/zh/activations/))。 若不指定，则不使用激活函数 (即，「线性」激活: `a(x) = x`)。
- **use_bias**: 布尔值，该层是否使用偏置向量。
- **kernel_initializer**: `kernel` 权值矩阵的初始化器 (详见 [initializers](https://keras.io/zh/initializers/))。
- **bias_initializer**: 偏置向量的初始化器 (see [initializers](https://keras.io/zh/initializers/)).
- **kernel_regularizer**: 运用到 `kernel` 权值矩阵的正则化函数 (详见 [regularizer](https://keras.io/zh/regularizers/))。
- **bias_regularizer**: 运用到偏置向的的正则化函数 (详见 [regularizer](https://keras.io/zh/regularizers/))。
- **activity_regularizer**: 运用到层的输出的正则化函数 (它的 "activation")。 (详见 [regularizer](https://keras.io/zh/regularizers/))。
- **kernel_constraint**: 运用到 `kernel` 权值矩阵的约束函数 (详见 [constraints](https://keras.io/zh/constraints/))。
- **bias_constraint**: 运用到偏置向量的约束函数 (详见 [constraints](https://keras.io/zh/constraints/))。

**输入尺寸**

nD 张量，尺寸: `(batch_size, ..., input_dim)`。 最常见的情况是一个尺寸为 `(batch_size, input_dim)`的 2D 输入。

## 快速搭建多层CNN

### Sequential模型

add(self, layer)

向模型中添加一个层

compile()

编译模型，模型在使用前必须编译，否则后面调用fit、fit_generator或者evaluate会抛出异常

fit()

将模型训练nb_epoch轮，参数：

batch_size：keras中的参数更像是按批次进行的，就是小批梯度下降算法，把数据分为若干组，称为batch，按批更像参数。这样，一个批中的一组数据共同决定了本次梯度的下降方向，一批数据中包含的样本数量称为batch_size。

epochs：整数，训练的轮数。训练过程中，当一个完整的数据集通过神经网络一次并且返回了一次，这个过程称为epoch，网络会在每个epoch结束时报告关于模型学习进度的调试信息，如损失，准确率。











