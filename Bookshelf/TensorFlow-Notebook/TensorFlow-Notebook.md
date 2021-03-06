前言
---
对框架不熟悉，跑再多程序都没有意义。深入理解框架，这是内功，这才是最重要的。脱离了对框架的掌握，一切照猫画虎的项目编程都不过是空中楼阁。
————　个人感悟

---

---

Tensorflow源码目录结构
---

## tensorflow/
- ### tensorflow/

	##### 该目录下存放着tensorflow的核心代码
	+ #### contrib/ 
	
		###### 该目录下存放有其他项目贡献者添加的相关贡献代码。由于tensorflow受关注程度较高，目前该目录正急剧膨胀。
	+ #### core/ 
	
		###### 后台C++实现部分。包含了主要的　C++代码　和　runtimes　。该目录为tensorflow的C++源码的核心。
		- ##### common_runtime/ 
			
			###### tensorflow 普通的 执行逻辑。
		- ##### distributed_runtime/ 
		
			###### tensorflow 与分布式相关的 执行逻辑。
		- ##### framework/ 
			
			###### 包含主要的 抽象图计算 和 其他有用的library 。对tensorflow进行计算过程中的 通用组件 进行了定义和实现。
		- ##### graph/
		
			###### tensorflow 图相关操作 的逻辑。 由于tensorflow中的数据计算本质上是一个图状结构的计算流程，该过程中存在将图进行切分并且并行化执行的可能性。该目录下的代码逻辑即为对图数据进行结构化定义并进行拆分的相关内容。
		- ##### kernels/
		
			###### 对tensorflow中各个 单步op 的具体实现。
		- ##### lib/ 
		
			###### 一些 公用的 调用方法 。同 util /
		- ##### ops/
		
			###### 对 kernel/ 下的op进行注册和对外声明。
		- ##### platform/
		
			###### 包含 抽象出平台 和 其他 导入库（protobuf等） 的代码
		- ##### protobuf/
		
			###### tensorflow下各个 模块间 进行 数据传输 的 数据结构定义，通过proto进行配置实现。
		- ##### public/
		
			###### 定义　Session
		- ##### user_ops/ 
		
			###### 存放 自己编写 的 op
		- ##### util/ 
		
			###### 一些 公用的 调用方法 。同　lib/
		- 其他 文件夹/
		- 其他 文档

	+ #### examples/
	
		###### 一些示例（如ios、android系统的示例）
	+ #### g3doc/
	
		###### 是针对c++、python的版本的代码文档	
	+ #### python/ 
	
		###### 前台Python接口。 该目录下存放了tensorflow使用python编写的相关代码，是和 **core/** 对应的python实现目录。使用python封装了　** 对　core/ 中实现的相关的机器学习算法　的调用　** 。 同时利用了　python方便的编程特性　和　C++高效的执行效率　。
		- ##### framework/
		
			###### 包含　图的python抽象　等，(还没深入验证过的：　“　其中很多被序列化为　proto　或被传递到　swigged session　调用　”　)
		- ##### kernel_tests/
		
			###### 单元测试代码　和　示例代码
		- ##### ops/
		
			###### 核心python接口
		- ##### platform/
	
			###### 和上面C++部分的platform（core/platform/）差不多, 对python I/O、单元测试等做了轻量级的包装。
	+ #### stream_executor/
	
		###### 流处理	
	+ #### tensorboard/ 
	
		###### tensorflow独家模块。用于模型训练中 实时生成 图表，以监控 模型的训练程度	
	+ #### tools/ 
	
		###### 一些 工具杂项（如pip、git）
	+ #### user_ops/
	
		###### 存放 自己编写 的 op
		
- ### third_party/
- ### tools/
- ### util/
- ### configure文档

	###### 该文件用于配置tensorflow的安装环境，运行该文件并完成tensorflow的安装环境配置后，输入相应bazel指令即可完成代码的编译工作（需要先安装bazel）
	
- ### 其他文档

<br>
<br>

#### Supplement:
很多博客中提到的 *models/*（位于根目录下，该目录下存放这多个使用python实现的模型实例），我并没有看到。估计是最新版的Tensorflow源码取消了这个文件夹。

#### Reference:
[google讨论小组](https://groups.google.com/a/tensorflow.org/forum/#!topic/discuss/39guS9VV0Yg)、[Tensorflow快速入门3–代码结构及模型例子](http://www.infocool.net/kb/OtherCloud/201701/278480.html)

---

---

基本使用
---

 - 使用图 (graph) 来表示计算任务.

 - 在被称之为 会话 (Session) 的上下文 (context) 中执行图.

 - 使用 tensor 表示数据.

 - 通过 变量 (Variable) 维护状态.

 - 使用 feed 和 fetch 可以为任意的操作(arbitrary operation) 赋值或者从其中获取数据.


TensorFlow 用图来表示计算任务，图中的节点被称之为operation，缩写成op。

一个节点获得 0 个或者多个张量 tensor，执行计算，产生0个或多个张量。

图必须在会话(Session)里被启动，会话(Session)将图的op分发到CPU或GPU之类的设备上，同时提供执行op的方法，这些方法执行后，将产生的张量(tensor)返回。

---

---

个人理解
---

TensorFlow是一个库，用于计算数据流图。

Session 与硬件设备相关联，而 Graph　只是画了一张虚拟的操作图。所以 Graph 只有放在　Session 中才能实际跑起来。Op　是代表　操作　的　节点　，一堆　Op 和它们之间的　关联　构成　Graph ， tensor 们在这张 Graph 上的那些孔（Op）里穿来穿去。

	import tensorflow as tf
	
	#定义‘符号’变量，也称为占位符
	a = tf.placeholder("float")
	b = tf.placeholder("float")

	y = tf.multiply(a, b) #构造一个op节点
	
	#建立会话
	sess = tf.Session()
	#运行会话，输入数据，并计算节点，同时打印结果
	print sess.run(y, feed_dict={a: 3, b: 3})
	# 任务完成, 关闭会话.
	sess.close()

---

---

综述
---

TensorFlow 是一个编程系统, 使用图来表示计算任务. 

图中的节点被称之为 op (operation 的缩写). 

一个 op 获得 0 个或多个 Tensor, 执行计算, 产生 0 个或多个 Tensor. 

每个 Tensor 是一个类型化的多维数组. 

例如, 你可以将一小组图像集表示为一个四维浮点数数组, 这四个维度分别是 [batch, height, width, channels].


一个 TensorFlow 图描述了计算的过程. 

为了进行计算, 图必须在 会话 里被启动. 

会话 将图的 op 分发到诸如 CPU 或 GPU 之类的 设备 上, 同时提供执行 op 的方法. 

这些方法执行后, 将产生的 tensor 返回. 

在 Python 语言中, 返回的 tensor 是 numpy ndarray 对象; 在 C 和 C++ 语言中, 返回的 tensor 是 tensorflow::Tensor 实例.

---

---

Tensorflow基本网站
---

[TensorFlow组织](https://github.com/tensorflow)
- [tensorflow源码](https://github.com/tensorflow/tensorflow)
  + 源码永远是学习的重中之重。
- [TensorFlow Model Zoo](https://github.com/tensorflow/models)
  + 该存储库包含在TensorFlow中实现的机器学习模型。因为这些型号仅仅与TensorFlow 1.0或更高版本兼容
  + 如果TensorFlow 是0.x版本，需要upgdade tensorflow version才能使用这些model

[TensorFlow官网](https://www.tensorflow.org/)
- [python-api](https://www.tensorflow.org/api_docs/python/)
- [TensorFlow中文社区](http://www.tensorfly.cn)
  + 内容老旧，里面的TensorFlow 是0.x版本。
  + 列举的不少api后来都改名了，直接copy下的代码有时候跑起来会报错。初学的时候看看还是可以的。

---

---

Tensorflow版本变动
---

[TensorFlow 1.0 版本 API 变动汇总](http://www.jianshu.com/p/04850ffc1021)

[自动将代码移植到 1.0 版本](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/tools/compatibility)：

	tf_upgrade.py --infile foo.py --outfile foo-upgraded.py

---

---

Tensorflow框架变动
---

[Convert Caffe models to TensorFlow](https://github.com/ethereon/caffe-tensorflow)

	convert.py

---

---

Tensorflow安装 & 遇到的问题及解决方案
---

出处：[Ubuntu + py2.7 + gpu](https://www.tensorflow.org/install/install_linux#installing_with_native_pip)

	pip uninstall tensorflow
	pip install tensorflow-gpu

tensorflow 尽量从 **源码** 安装，这样运行起来会更快，遇到的 Warning 也更少

### Problem & Solution

#### Problem_0

如果 TensorFlow 程序运行缓慢， 可能和 protobuf pip package 有关

#### Solution

    pip install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/protobuf-3.1.0-cp27-none-linux_x86_64.whl
	  
	  
---

---


Session
---

	with tf.Session() as sess:
	  result = sess.run([mul, intermed])
	  print result
  
 等同于：

      tf.Session().run([mul, intermed])

---

---

Tensor
---

在 TF 中，一个张量如下表述：

	my_tensor = tf.constant(0., shape=[6,3,7])
	print(my_tensor) # -> Tensor("Const_1:0", shape=(6, 3, 7), dtype=float32)
一个张量应该包含如下内容：

> 一个名字，它用于**键值对的存储**，用于后续的检索： **Const: 0**
>
> 一个形状描述， 描述数据的每一维度的元素个数：（6，3，7）
>
> 数据类型，比如 float32

从向量空间到实数域的多重线性映射(multilinear maps)

在 TensorFlow 中用 tensor 数据结构来代表所有的数据, 计算图中, 操作间传递的数据都是 tensor。


张量是一个拥有 n 维度的数组，并且其中的值都拥有相同的类型，比如整型，浮点型，布尔型等等。

张量可以用我们所说的形状来描述：我们用列表（或元祖）来描述我们的张量的每个维度的大小

不同维度的Tensor | 俗称　| 表示方法
--- | --- | ---
n 维度的张量 | 多维数组　| （D_0, D_1, D_2, ..., D_n-1）
W x H 大小的张量 | 矩阵 | （W, H）
尺度是 W 的张量 | 向量 | （W, ）
０维度的张量 | 标量 | （）或（1, ）

注意： D_*，W，H 都是整型。


Tensor 种类　| Annotation
--- | ---
常值张量(constant)　| 是不需要初始化的。
变量(Variable)　| 是维护图执行过程中的状态信息的. 需要它来保持和更新参数值，是需要动态调整的。必须先通过 **tf.global_variables_initializer()** 初始化，然后才有值。

---

---

Variable
---

## 变量初始化

### 全部变量一次性初始化

    tf.Session.run(tf.global_variables_initializer())

等同于：

    with tf.Session() as sess:
    
      init = tf.global_variables_initializer()
      sess.run(init)
	
等同于：

    init = tf.global_variables_initializer()
    sess = tf.Session()
    sess.run(init)


**tf.global_variables_initializer()** == **tf.initialize_all_variables()**

但是在 2017年3月2号以后, tf.initialize_all_variables() 该函数将不再使用。取而代之的是　tf.global_variables_initializer() 

来自TensorFlow 文档的重要说明：
> tf.initialize_all_variables(): THIS FUNCTION IS DEPRECATED. It will be removed after 2017-03-02. Instructions for updating: Use tf.global_variables_initializer instead.

### 仅指定部分变量初始化
使用 tf.initialize_variables()

    ＃要初始化v_6, v_7, v_8三个变量：
    init_new_vars_op = tf.initialize_variables([v_6, v_7, v_8])
    sess.run(init_new_vars_op)

PS:　识别　未被初始化的变量　的小技巧：

	uninit_vars = []
	＃用 try & except 语句块捕获：
	for var in tf.all_variables():
	    try:
		sess.run(var)
	    except tf.errors.FailedPreconditionError:
		uninit_vars.append(var)

	init_new_vars_op = tf.initialize_variables(uninit_vars)

---

---

tf.constant
---

constant(
    value,
    dtype=None,
    shape=None,
    name='Const',
    verify_shape=False
)
Defined in [tensorflow/python/framework/constant_op.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/constant_op.py).

Creates a constant tensor.
生成一个常量

**Args:**

value: A constant value (or list) of output type dtype.
dtype: The type of the elements of the resulting tensor.
shape: Optional dimensions of resulting tensor.
name: Optional name for the tensor.
verify_shape: Boolean that enables verification of a shape of values.

**Returns:**

A Constant Tensor.

Exampple:

	i = tf.constant(0.1, shape=[1,3,1,2])
	print(tf.Session().run(i))
	tf.Session().run(i)
result:
<ol>
<li>Coffee</li>
<li>Milk</li>
</ol>
	[[[[ 0.1  0.1]]
	  [[ 0.1  0.1]]
	  [[ 0.1  0.1]]]]
    Out[1]:
	array([[[[ 0.1,  0.1]],
		[[ 0.1,  0.1]],
		[[ 0.1,  0.1]]]], dtype=float32)
	
---

---

tf.truncated_normal
---

truncated_normal(
    shape,
    mean=0.0,
    stddev=1.0,
    dtype=tf.float32,
    seed=None,
    name=None
)
Defined in [tensorflow/python/ops/random_ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/ops/random_ops.py).

Outputs random values from a truncated normal distribution.

The generated values follow a normal distribution with specified mean and standard deviation, except that values whose magnitude is more than 2 standard deviations from the mean are dropped and re-picked.

**Args:**

shape: A 1-D integer Tensor or Python array. The shape of the output tensor.
mean: A 0-D Tensor or Python value of type dtype. The mean of the truncated normal distribution.
stddev: A 0-D Tensor or Python value of type dtype. The standard deviation（标准差） of the truncated normal distribution.
dtype: The type of the output.
seed: A Python integer. Used to create a random seed for the distribution. See tf.set_random_seed for behavior.
name: A name for the operation (optional).

**Returns:**

A tensor of the specified shape filled with random truncated normal values.

---

---

[Tensorflow一些常用基本概念与函数](http://blog.csdn.net/lenbow/article/category/6194008)
---

---

---


tf.nn.conv2d
---

(input, filter, strides, padding, use_cudnn_on_gpu=None, name=None)

除去name参数用以指定该操作的name，与方法有关的一共五个参数：

Args|Annotation
 :---- | ----
 第一个参数input | 指需要做卷积的输入图像，它要求是一个Tensor，具有[batch, in_height, in_width, in_channels]这样的shape，具体含义是[训练时一个batch的图片数量, 图片高度, 图片宽度, 图像通道数]，注意这是一个4维的Tensor，要求类型为float32和float64其中之一
 第二个参数filter | 相当于CNN中的卷积核，它要求是一个Tensor，具有[filter_height, filter_width, in_channels, out_channels]这样的shape，具体含义是[卷积核的高度，卷积核的宽度，图像通道数，卷积核个数]，要求类型与参数input相同，有一个地方需要注意，第三维in_channels，就是参数input的第四维
第三个参数strides | 卷积时在图像每一维的步长，这是一个一维的向量，长度4
第四个参数padding | string类型的量，只能是"SAME","VALID"其中之一，这个值决定了不同的卷积方式
第五个参数 | use_cudnn_on_gpu:bool类型，是否使用cudnn加速，默认为true        

**结果返回：** 一个Tensor，这个输出，就是我们常说的feature map

	def conv2d(x, W):
	  return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')

---

---

tf.nn.max_pool
---

(value, ksize, strides, padding, name=None)

参数是四个，和卷积很类似：


Args|Annotation
 :---- | ----
第一个参数value | 需要池化的输入，一般池化层接在卷积层后面，所以输入通常是feature map，依然是[batch, height, width, channels]这样的shape
第二个参数ksize | 池化窗口的大小，取一个四维向量，一般是[1, height, width, 1]，因为我们不想在batch和channels上做池化，所以这两个维度设为了1
第三个参数strides | 和卷积类似，窗口在每一个维度上滑动的步长，一般也是[1, stride,stride, 1]
第四个参数padding | 和卷积类似，可以取'VALID' 或者'SAME'. 返回一个Tensor，类型不变，shape仍然是[batch, height, width, channels]这种形式.  padding='VALID'时，无自动填充。padding='SAME'时，自动填充，池化后保持shape不变。


	def max_pool_2x2(x):
	  return tf.nn.max_pool(x, ksize=[1, 2, 2, 1],
				strides=[1, 2, 2, 1], padding='SAME')
			
---

---

返回　tensor 的　维数
---

如果tensor是用调用tensorflow框架定义的，那么用 tensor_name.shape 即可返回tensorflow 的维数：

	>>> import tensorflow as tf 
	>>> a=tf.constant([  
	...         [[1.0,2.0,3.0,4.0],  
	...         　[5.0,6.0,7.0,8.0],  
	...         　[8.0,7.0,6.0,5.0],  
	...         　[4.0,3.0,2.0,1.0]],  
	...         [[4.0,3.0,2.0,1.0],  
	...          [8.0,7.0,6.0,5.0],  
	...          [1.0,2.0,3.0,4.0],  
	...          [5.0,6.0,7.0,8.0]]  
	...     ])
	>>> a.shape
	TensorShape([Dimension(2), Dimension(4), Dimension(4)])
也可通过调用 numpy 来返回 tensor 的维数：

	>>> import numpy as np
	>>> np.shape(a)
	TensorShape([Dimension(2), Dimension(4), Dimension(4)])



---

---

查看tensor数值
---

### 不能直接用print的原因：

print只能打印输出shape的信息，而要打印输出tensor的值，需要借助　class tf.Session, class tf.InteractiveSession。

因为我们在建立graph的时候，只建立tensor的结构形状信息，并没有执行数据的操作。

### 法一：

	>>> import tensorflow as tf 
	>>> a=tf.constant([  
	...         [[1.0,2.0,3.0,4.0],  
	...         　[5.0,6.0,7.0,8.0],  
	...         　[8.0,7.0,6.0,5.0],  
	...         　[4.0,3.0,2.0,1.0]],  
	...         [[4.0,3.0,2.0,1.0],  
	...          [8.0,7.0,6.0,5.0],  
	...          [1.0,2.0,3.0,4.0],  
	...          [5.0,6.0,7.0,8.0]]  
	...     ])
	
	# Launch the graph in a session. 
	
	>>> sess = tf.Session() 
	
	# Evaluate the tensor `a`. 
	
	>>> print(sess.run(a)) 
	[[[ 1.  2.  3.  4.]
	  [ 5.  6.  7.  8.]
	  [ 8.  7.  6.  5.]
	  [ 4.  3.  2.  1.]]

	 [[ 4.  3.  2.  1.]
	  [ 8.  7.  6.  5.]
	  [ 1.  2.  3.  4.]
	  [ 5.  6.  7.  8.]]]


### 法二：


	>>> import tensorflow as tf 
	>>> a=tf.constant([  
	...         [[1.0,2.0,3.0,4.0],  
	...         　[5.0,6.0,7.0,8.0],  
	...         　[8.0,7.0,6.0,5.0],  
	...         　[4.0,3.0,2.0,1.0]],  
	...         [[4.0,3.0,2.0,1.0],  
	...          [8.0,7.0,6.0,5.0],  
	...          [1.0,2.0,3.0,4.0],  
	...          [5.0,6.0,7.0,8.0]]  
	...     ])
	
	# Launch the graph in a session. 
	
	>>> with tf.Session():
	...     print(a.eval())
	... 
 
	[[[ 1.  2.  3.  4.]
	  [ 5.  6.  7.  8.]
	  [ 8.  7.  6.  5.]
	  [ 4.  3.  2.  1.]]

	 [[ 4.  3.  2.  1.]
	  [ 8.  7.  6.  5.]
	  [ 1.  2.  3.  4.]
	  [ 5.  6.  7.  8.]]]


---

---

[tf.reshape](https://www.tensorflow.org/api_docs/python/tf/reshape)
---

reshape(
    tensor,
    shape,
    name=None
)

给定一个tensor，这个操作会返回一个有着跟原tensor一样的值且经过shape重塑过的张量。
如果shape中存在值为-1的组成，被计算过的维度总和的大小将保持恒定。尤其是当一个为[-1]的shape被展平为一维，且shape最多只能存在一个-1。
如果shape是一维或者是更高维度，那么这个操作将会返回一个形状为shape，填充满value值的张量。在这种情况下，shape的元素的数量必须与tensor的元素数量一样。

Args:

tensor: A Tensor.
shape: A Tensor. Must be one of the following types: int32, int64. Defines the shape of the output tensor.
name: A name for the operation (optional).

Returns:

A Tensor. Has the same type as tensor.


---

---

Tensorboard
---

Module | Annotation
 :---- | ----
SCALARS | 记录单一变量的，使用 tf.summary.scalar() 收集构建。
IMAGES | 收集的图片数据，当我们使用的数据为图片时（选用）。
AUDIO | 收集的音频数据，当我们使用数据为音频时（选用）。                - class tf.TensorShape
799
                    Defined in [tensorflow/python/framework/tensor_shape.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/tensor_shape.py).
GRAPHS | 构件图，效果图类似流程图一样，我们可以看到数据的流向，使用tf.name_scope()收集构建。
DISTRIBUTIONS | 用于查看变量的分布值，比如 W（Weights）变化的过程中，主要是在 0.5 附近徘徊。
HISTOGRAMS | 用于记录变量的历史值（比如 weights 值，平均值等），并使用折线图的方式展现，使用tf.summary.histogram()进行收集构建。

代码运行完成之后，命令行中跳转到代码生成的文件夹中，输入

	tensorboard --logdir .
等待程序反应之后，浏览器访问
> localhost:6006

也可以自己定义端口

---

---

小技巧
---
[Use WeChat to Monitor Your Network](http://www.jianshu.com/p/b2e050bb7d4f)

---

---        

tf.assign
---
assign(
    ref,
    value,
    validate_shape=None,
    use_locking=None,
    name=None
)

Defined in [tensorflow/python/ops/state_ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/ops/state_ops.py).

将　value 赋值给　ref，并输出 ref　

这使得需要使用复位值的连续操作变简单
                - class tf.TensorShapeduilie
799
                    Defined in [tensorflow/python/framework/tensor_shape.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/tensor_shape.py).
**Args:**

- ref: A mutable Tensor. Should be from a Variable node. May be uninitialized.
- value: A Tensor. Must have the same type as ref. The value to be assigned to the variable.
- validate_shape: An optional bool. Defaults to True. If true, the operation will validate that the shape of 'value' matches the shape of the Tensor being assigned to. If false, 'ref' will take on the shape of 'value'.
- use_locking: An optional bool. Defaults to True. If True, the assignment will be protected by a lock; otherwise the behavior is undefined, but may exhibit less contention.
- name: A name for the operation (optional).

**Returns:**

Same as "ref". Returned as a convenience for operations that want to use the new value after the variable has been reset.

---

---

[Python API Guides](https://www.tensorflow.org/api_docs/python/)  (仅记录日常用到的api)
---

## Python API Guides

- ### Tensor转换:  [Ref](https://www.tensorflow.org/api_guides/python/array_ops)

  + #### 生成tensor
    - tf.string_to_number
    - tf.to_double
    - tf.to_float
    - tf.to_bfloat16
    - tf.to_int32
    - tf.to_int64
    - tf.cast

  + #### tensor形状op
    - tf.shape
    - tf.size
    - tf.rank
    - tf.reshape
    - tf.squeeze
    - tf.expand_dims
　　　
  + #### 切片和插入op
    - tf.slice
    - tf.split
    - tf.pad
    - tf.concat
    - tf.transpose

- ### 断言和布尔检查:   [Ref](https://www.tensorflow.org/api_guides/python/check_ops)
	Defined in [tensorflow/python/ops/check_ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/ops/check_ops.py)

- ### Graphs运作:  [Ref](https://www.tensorflow.org/api_guides/python/client)
	+ #### 会话管理
		- [tf.Session](https://www.tensorflow.org/api_docs/python/tf/Session) 
	    
			Defined in [tensorflow/python/client/session.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/client/session.py)
		- [tf.InteractiveSession](https://www.tensorflow.org/api_docs/python/tf/InteractiveSession)
		
			Defined in [tensorflow/python/client/session.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/client/session.py).
		- [tf.get_default_session](https://www.tensorflow.org/api_docs/python/tf/get_default_session)
		
			Defined in [tensorflow/python/framework/ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/ops.py).

  	+ #### 错误类和方便功能
		- tf.OpError
		
			Defined in [tensorflow/python/framework/errors_impl.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/errors_impl.py).

- ### 常量、序列和随机变量：　　[Ref](https://www.tensorflow.org/api_guides/python/constant_op)

	+ #### 生成常量tensor
		- tf.zeros
		- tf.zeros_like
		- tf.ones
		- tf.ones_like
		- tf.fill
		- tf.constant

 	+ #### 生成序列op
		- tf.linspace
		- tf.range

	+ #### 生成随机tensor
	
		Under *Tensorflow/python/ops* dir or *Tnesorflow/python/framework* dir
		- tf.random_normal
		- tf.truncated_normal
		- tf.random_uniform
		- tf.random_shuffle

- ### 构建网络层:   [Ref](https://www.tensorflow.org/api_guides/python/contrib.layers)
	
 	+ #### 高级构建op

		- tf.contrib.layers.avg_pool2d
		- tf.contrib.layers.batch_norm
		- tf.contrib.layers.convolution2d
		- tf.nn.conv2d_transpose
		- tf.contrib.layers.convolution2d_transpose
		- tf.nn.dropout
		- tf.contrib.layers.fully_connected
		- tf.contrib.layers.max_pool2d
		- tf.nn.relu
		- tf.nn.relu6
		- tf.nn.softmax
		- tf.stack
		- tf.contrib.layers.unit_norm

	+ #### 正则化op
	
		Defined in [tensorflow/contrib/layers/python/layers/regularizers.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/contrib/layers/python/layers/regularizers.py).
		- tf.contrib.layers.l1_regularizer
		- tf.contrib.layers.l2_regularizer

 	+ #### 初始化op
	
		Defined in [tensorflow/contrib/layers/python/layers/initializers.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/contrib/layers/python/layers/initializers.py)

	+ #### （权重）优化op
  	
		Defined in [tensorflow/contrib/layers/python/layers/optimizers.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/contrib/layers/python/layers/optimizers.py)
		- [tf.contrib.layers.optimize_loss](https://www.tensorflow.org/api_docs/python/tf/contrib/layers/optimize_loss)

 	+ #### 总结op
	
		Defined in [tensorflow/contrib/layers/python/layers/summaries.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/contrib/layers/python/layers/summaries.py)
		- tf.contrib.layers.summarize_activation
		- tf.contrib.layers.summarize_tensor
		- tf.contrib.layers.summarize_tensors
		- tf.contrib.layers.summarize_collection
		- tf.contrib.layers.summarize_activations

- ### RNN：　[Ref](https://www.tensorflow.org/api_guides/python/contrib.rnn)

- ### 过程控制：　[Ref](https://www.tensorflow.org/api_guides/python/control_flow_ops)

	+ #### 过程控制op:
		- tf.identity
		- tf.tuple
		
	+ #### 逻辑op:
		- tf.logical_and
		- tf.logical_not
		- tf.logical_or
		- tf.logical_xor

	+ #### 对照op:
		- tf.equal
		- tf.not_equal
		- tf.less
		- tf.less_equal
		- tf.greater
		- tf.greater_equal
		- tf.where

	+ #### 调试op:
		- tf.is_finite
		
			Defined in [tensorflow/python/ops/gen_math_ops.py]()
		- tf.Assert
		
			Defined in [tensorflow/python/ops/control_flow_ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/ops/control_flow_ops.py).
		- tf.Print
		
			Defined in [tensorflow/python/ops/logging_ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/ops/logging_ops.py).

- ### 构建graph：  [Ref](https://www.tensorflow.org/api_guides/python/framework)

	+ #### 核心graph数据结构op
		Defined in [tensorflow/python/framework/ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/ops.py)
		- tf.Graph
		- tf.Operation
		- tf.Tensor
		
	+ #### tensor类型op
		Defined in [tensorflow/python/framework/dtypes.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/dtypes.py)
		- tf.DType
		- tf.as_dtype
	
	+ #### Utility函数op
		Defined in [tensorflow/python/framework/ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/ops.py)
		- tf.device
		- tf.container    
		- tf.name_scope
		- tf.get_default_graph
		- tf.reset_default_graph
	
	+ #### 自定义op
		- class tf.TensorShape
		    	
			Defined in [tensorflow/python/framework/tensor_shape.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/framework/tensor_shape.py).

- ### 图像：  [Ref](https://www.tensorflow.org/api_guides/python/image)
	
	+ #### 图像调整op
		- tf.image.resize_images

- ### 输入和读取：

	+ #### 占位符op
		- tf.placeholder
		- tf.placeholder_with_default
	+ #### 读取op
		- tf.ReaderBase
		- tf.IdentityReader
		- tf.TFRecordReader

	+ #### 转换成tensor
		- tf.decode_csv　
		- tf.decode_raw
	
	+ #### 队列op
		- tf.QueueBase
		- tf.FIFOQueue
	
	+ #### 文件处理op
		- tf.matching_files
		- tf.read_file
		- tf.write_file
	
	+ #### 流水线输入op
		- tf.train.batch
		- tf.train.maybe_batch
		- tf.train.batch_join
		- tf.train.maybe_batch_join
		- tf.train.shuffle_batch

- ### 数学计算：　　[{Detail}](https://github.com/JNingWei/Notebook/blob/master/Bookshelf/TensorFlow-Notebook/TensorFlow-Box/Source_Code/tensorflow/python/ops/math_ops.md)

	Defined in　[tensorflow/python/ops/math_ops.py](https://github.com/tensorflow/tensorflow/blob/r1.1/tensorflow/python/ops/math_ops.py)

	+ #### 算数op
		- tf.add
		- tf.subtract
		- tf.multiply
		- tf.scalar_mul
		- tf.div
		- tf.divide
		- tf.truediv
		- tf.floordiv
		- tf.realdiv
		- tf.truncatediv
		- tf.floor_div
		- tf.truncatemod
		- tf.floormod
		- tf.mod
		- tf.cross

	+ #### 基本数学op
		- tf.add_n
		- tf.abs
		- tf.negative
		- tf.sign
		- tf.square
		- tf.round
		- tf.sqrt
		- tf.exp
		- tf.expm1
		- tf.log
		- tf.log1p
		- tf.ceil
		- tf.floor
		- tf.maximum
		- tf.minimum
		- tf.cos
		- tf.sin
		- tf.tan
		- tf.acos
		- tf.asin
		- tf.atan

	+ #### 矩阵的线代op
		- tf.diag
		- tf.trace
		- tf.transpose
		- tf.eye
		- tf.matrix_diag
		- tf.matrix_transpose
		- tf.matmul
		- tf.norm
		- tf.cholesky

	+ #### 复数op
		- tf.complex
		- tf.conj
		- tf.imag
		- tf.real

	+ #### 降维op
		- tf.reduce_sum
		- tf.reduce_prod
		- tf.reduce_min
		- tf.reduce_max
		- tf.reduce_mean
		- tf.reduce_all
		- tf.reduce_any

	+ #### 切片op
		- tf.segment_sum
		- tf.segment_prod
		- tf.segment_min
		- tf.segment_max
		- tf.segment_mean
		- tf.sparse_segment_sum
		- tf.sparse_segment_mean
		
	+ #### 序列比较和索引提取op
		- tf.argmin
		- tf.argmax
		- tf.where
		- tf.unique
		
- ### 神经网络

	+ #### 激活op
		- tf.nn.relu
		- tf.nn.relu6
		- tf.nn.crelu
		- tf.nn.elu
		- tf.nn.softplus
		- tf.nn.softsign
		- tf.nn.dropout
		- tf.nn.bias_add
		- tf.sigmoid
		- tf.tanh

	+ #### 卷积op
		- tf.nn.convolution
		- tf.nn.conv2d
		- tf.nn.depthwise_conv2d
		- tf.nn.depthwise_conv2d_native
		- tf.nn.separable_conv2d
		- tf.nn.atrous_conv2d
		- tf.nn.atrous_conv2d_transpose
		- tf.nn.conv2d_transpose
		- tf.nn.conv1d
		- tf.nn.conv3d
		- tf.nn.conv3d_transpose
		- tf.nn.conv2d_backprop_filter
		- tf.nn.conv2d_backprop_input
		- tf.nn.conv3d_backprop_filter_v2
		- tf.nn.depthwise_conv2d_native_backprop_filter
		- tf.nn.depthwise_conv2d_native_backprop_input

	+ #### 池化op
		- tf.nn.avg_pool
		- tf.nn.max_pool
		- tf.nn.max_pool_with_argmax
		- tf.nn.avg_pool3d
		- tf.nn.max_pool3d
		- tf.nn.fractional_avg_pool
		- tf.nn.fractional_max_pool
		- tf.nn.pool

	+ #### 归一化op
		- tf.nn.l2_normalize
		- tf.nn.local_response_normalization
		- tf.nn.sufficient_statistics
		- tf.nn.normalize_moments
		- tf.nn.moments
		- tf.nn.weighted_moments
		- tf.nn.fused_batch_norm
		- tf.nn.batch_normalization
		- tf.nn.batch_norm_with_global_normalization

	+ #### 误差损失op
		- tf.nn.l2_loss
		- tf.nn.log_poisson_loss
		
	+ #### 分类op
		- tf.nn.sigmoid_cross_entropy_with_logits
		- tf.nn.softmax
		- tf.nn.log_softmax
		- tf.nn.softmax_cross_entropy_with_logits
		- tf.nn.sparse_softmax_cross_entropy_with_logits
		- tf.nn.weighted_cross_entropy_with_logits

	+ #### 查找嵌入tensor的value

		- tf.nn.embedding_lookup
		- tf.nn.embedding_lookup_sparse

	+ #### 候选抽样op
		- tf.nn.sampled_softmax_loss

- ### value

	+ #### value
		- tf.Variable
		
	+ #### value管理op
		- tf.global_variables
		- tf.local_variables
		- tf.model_variables
		- tf.trainable_variables
		- tf.moving_average_variables
		- tf.global_variables_initializer
		- tf.local_variables_initializer
		- tf.variables_initializer
		- tf.is_variable_initialized
		- tf.report_uninitialized_variables
		- tf.assert_variables_initialized
		- tf.assign
		- tf.assign_add
		- tf.assign_sub
		
	+ #### value保存和恢复op
		- tf.train.Saver
		- tf.train.latest_checkpoint
		- tf.train.get_checkpoint_state
		- tf.train.update_checkpoint_state

	+ #### value共享op
		- tf.get_variable
		- tf.get_local_variable
		- tf.VariableScope
		- tf.variable_scope
		- tf.variable_op_scope
		- tf.get_variable_scope
		- tf.constant_initializer
		- tf.random_normal_initializer
		- tf.truncated_normal_initializer
		- tf.random_uniform_initializer
		- tf.uniform_unit_scaling_initializer
		- tf.zeros_initializer
		- tf.ones_initializer






