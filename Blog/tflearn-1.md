---
title: Tensorflow 学习笔记
date: 2018-12-22 18:56:12
photo : /tflearn-1/tf.png
tags:
- Tensorflow
- Python
categories:
- AI
- Tensorflow
---
{% note default %}
智能就是有学习能力。
{% endnote %}

<!-- more -->

## 神经网络具体实现

### 步骤

- 准备数据集，提取特征，作为输入喂给神经网络
- 搭建 NN 结构，从输入到输出（先搭建计算图，再用会话执行）
> NN 前向传播算法-->计算输出
- 大量特征数据喂给 NN，迭代优化 NN 参数
> NN 反向传播算法-->优化参数训练模型
- 使用训练好的模型预测和分类

下面是一个具体的例子，这个例子中并没有反向传播优化参数。
这个例子是通过输入物品的两个指标：`质量`和`体积`，来判定物品是否合格

```python
import tensorflow as tf

x = tf.constant([[0.7,0.5]])
w1 = tf.Variable(tf.random_normal([2,3],stddev=1,seed=1))
w2 = tf.Variable(tf.random_normal([3,1],stddev=1,seed=1))


a = tf.matmul(x,w1)
y = tf.matmul(a,w2)


with tf.Session() as sess:
    init_op = tf.global_variables_initializer()
    sess.run(init_op)
    print "y is ",sess.run(y)
```

### 数据来源

数据源是来自于实际过程的，假设我们此处直接喂食一组或者多组数据：

使用 `placeholder` 方法进行预设值，在会话中输入实际值。

- 喂食 1 组

```python
x = tf.placeholder(tf.float32,shape=(1,2)) 
import tensorflow as tf

# 喂食 1 组 2 维张量
x = tf.placeholder(tf.float32,shape=(1,2))
w1 = tf.Variable(tf.random_normal([2,3],stddev=1,seed=1))
w2 = tf.Variable(tf.random_normal([3,1],stddev=1,seed=1))


a = tf.matmul(x,w1)
y = tf.matmul(a,w2)


with tf.Session() as sess:
    init_op = tf.global_variables_initializer()
    sess.run(init_op)
    print "y is ",sess.run(y,feed_dict={x:[[0.7,0.5]]})
```

- 喂食多组

```python
import tensorflow as tf

# 喂食多组 2 维张量
x = tf.placeholder(tf.float32,shape=(None,2))
w1 = tf.Variable(tf.random_normal([2,3],stddev=1,seed=1))
w2 = tf.Variable(tf.random_normal([3,1],stddev=1,seed=1))


a = tf.matmul(x,w1)
y = tf.matmul(a,w2)


with tf.Session() as sess:
    init_op = tf.global_variables_initializer()
    sess.run(init_op)
    print "y is \n",sess.run(y,feed_dict={x:[[0.7,0.5],[0.2,0.3],[0.3,0.4],[0.4,0.5]]})
    print "w1:\n",sess.run(w1)
    print "w2:\n",sess.run(w2)
```

### 定义前向传播过程

在喂食数据的例子中，前向传播过程很简单，就是两条语句：

```python
a = tf.matmul(x,w1)
y = tf.matmul(a,w2)
```


### 定义反向传播并训练

为了使反向传播更容易理解，我们再具体化一些。

```python
import tensorflow as tf
import numpy as np # 科学计算库

BATCH_SIZE = 8# 每次喂食数量
seed = 23455# 随机种子值，可以根据时间戳来设置
rng = np.random.RandomState(seed)
X = rng.rand(32,2)# 随机生成数据集
Y = [[int(x0+x1<1)] for (x0,x1) in X]# 人为定义体积与质量和小于1才合格

print "X:\n",X
print "Y:\n",Y

# 输入、参数、输出、前向传播

x = tf.placeholder(tf.float32,shape=(None,2))
y_= tf.placeholder(tf.float32,shape=(None,1))

w1= tf.Variable(tf.random_normal([2,3],stddev=1,seed=1))
w2= tf.Variable(tf.random_normal([3,1],stddev=1,seed=1))

a = tf.matmul(x,w1)
y = tf.matmul(a,w2)

# 损失函数及反向传播

loss = tf.reduce_mean(tf.square(y-y_))
# 梯度下降方法
train_step = tf.train.GradientDescentOptimizer(0.001).minimize(loss)

with tf.Session() as sess:
    init_op = tf.global_variables_initializer()
    sess.run(init_op)
    print "w1:\n",sess.run(w1)
    print "w2:\n",sess.run(w2)
    print "\n"
    
    STEPS = 3000
    for i in range(STEPS):
        start = (i*BATCH_SIZE)%32
        end = start + BATCH_SIZE
        sess.run(train_step,feed_dict={x:X[start:end],y_:Y[start:end]})
        # 每500次输出一次训练总误差
        if i%500 == 0:
            total_loss = sess.run(loss,feed_dict={x:X,y_:Y})
            print("After %d training step(s),loss on all data is %g"%(i,total_loss))
            
        
    print "\n"
    print "w1:\n",sess.run(w1)
    print "w2:\n",sess.run(w2)
        
```

## 总结

根据神经网络实现的步骤，或许能够理解代码如何编写了。再次总结一下，使用神经网络的目的是为了得到一个优秀的模型来进行预测或者分类。如何训练出一个优秀的模型那就是问题的关键了。首先，一个优秀模型的训练离不开数据和计算参数，数据通常来源于生活，而参数我们可以使用随机数初始化，训练之后就是我们需要的参数了。其次，有了数据和参数，我们评判模型的标准是什么，通过数据和参数搭建出计算图，这就是前向传播所需要做的内容。最后，我们搭建好了计算图，就要开始训练模型了，训练模型是一个迭代的过程，迭代过程需要指定如何优化，因此就需要定义误差函数和反向传播过程。