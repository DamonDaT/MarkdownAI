### 【八股文】

***

> 收集一些常见的 LLM 八股文知识点，比较零碎，尽量归纳总结一下

<img src="./images/LlmBagu/01.gif">

***





### 【一】Transformer 架构

***

> 作为划时代的 LLM 核心架构，[Transformer 个人博客](https://blog.csdn.net/qq_34330456/article/details/102964628)，必须掌握其中的每个模块



#### 【1.1】Self-Attention

***

<img src="./images/LlmBagu/02.jpg">

***



#### 【1.2】除以 根号 d_k

***

* 首先要除以一个数，防止输入 **softmax** 的值过大，导致偏导数趋近于 **0**，造成 **梯度消失**。
* 选择根号 d_k 是因为可以使得 q*k 的结果满足期望为 0，方差为 1 的分布，类似于归一化。

***



#### 【1.3】Multi-Head Attention

***

* **提升模型的表达能力**： 每个注意力头（attention head）可以学习独特的表示，捕捉不同的特征和模式。多头注意力能够在不同子空间中并行关注信息，从而丰富整体的表示能力。
* **捕捉不同的依赖关系**： 在自然语言中，不同词汇之间可能存在复杂的依赖关系。单一的注意力机制可能无法有效捕捉所有的重要关系，而多头注意力可以同时关注句子中不同维度的关系，提供更丰富的上下文信息。

***



#### 【1.4】LLM 推理加速

***

* **KV - Cahce**：从前往后，将前面已经计算过的 Key 和 Value 存下来，新进来的 Token 在计算 Attention 时直接拿来用，推理速度会快个 4 ~ 5 倍。
* **Page Attention**：有效利用显存空间，避免空间碎片化。
* **MQA**（Multi-Query Attention），**GQA**（Group-Query Attention，慢慢成为了标准）。

***



#### 【1.5】Flash Attention

***

* Flash Attention：是一种快速，高效，可扩展的注意力机制
  * 将 attention 的计算分块送进速度比 HBM 更快的 SRAM 中，只需要计算块内元素之间的注意力权重，而不是整个序列。
  * 传统的 attention 复杂度是 O(N^2)，现在在保持原有精度的情况下，能达到近似线性 O(N) 的加速比。
  * 现在很多主流模型也是利用了 Flash Attention 这种分块计算的技术，将上下文扩展至原本的几十倍。
* Flash Attention v2：进一步优化
  * 减少了非矩阵乘法运算（non-matmul）FLOPs 的数量。
  * 在序列长度维度上进行并行化，在输入序列很长而 batch size 很小的情况下能增加 GPU 的利用率。
  * 在一个 attention 计算块内，计算工作分配在一个 thread block 的不同 warp 上，减少通信和共享内存的读写。

***





### 【二】Python 语言

***



#### 【2.1】关键字

***

* **global**：用于在函数或其他局部作用域中声明一个 **全局变量**。通过这种声明，你可以在函数内部修改一个定义在全局作用域中的变量。
* **nonlocal**：用于在嵌套函数中使用。这种声明允许你在 **内层函数** 中修改外层（但非全局）函数中的变量。

***



#### 【2.2】可变引用

***

* 以 列表 为例：
* Python 中的列表是可变的，这意味着列表可以被修改，而不需要创建一个新的对象。对列表元素的修改，添加或删除操作都会影响原始列表。因此，将一个列表赋值给另一个变量时，实际上是在创建一个对原始列表的引用，而不是创建一个新的列表。



#### 【2.3】深浅拷贝

***

* 以 列表 为例：
* **浅拷贝**：浅拷贝会创建一个新的列表对象，但不会创建列表中元素的副本。如果原列表中的元素是不可变的（比如字符串，整数），这通常没有问题，但如果原列表中的元素是可变的（例如列表），那么原列表和新列表中相同位置的元素将引用内存中的同一个对象。因此如果修改了可变元素，将影响到原列表和新列表。

* **深拷贝**：深拷贝会创建一个新的列表对象，还会递归创建原列表中的所有元素的副本。这意味着，无论原列表中的元素是否可变，新列表都完全独立于原列表。

***



#### 【2.4】装饰器

***

* 装饰器允许在不修改原有函数或类的定义的情况下，对函数或类进行包装，通过预先定义的逻辑来增强或修改它们的行为。
* 装饰器本质上是一个 Python 函数，它接受一个函数作为参数并返回一个新的函数。

```python
def my_decorator(func):
    def wrapper():
        print(1)  # 在原有函数执行前的代码
        func()
        print(2)  # 在原有函数执行后的代码
    return wrapper

@my_decorator
def my_function():
    print("这是一个需要被装饰的函数")
    
my_function()

# 1
# 这是一个需要被装饰的函数
# 2
```

* 多装饰器叠加使用（上面的装饰器将下面的装饰器作为入参，相当于 **my_decorator1 ( my_decorator2 ( func ) ) **）

```python
def my_decorator1(func):
    def wrapper():
        print('1:1')  # 在原有函数执行前的代码
        func()
        print('1:2')  # 在原有函数执行后的代码
    return wrapper

def my_decorator2(func):
    def wrapper():
        print('2:1')  # 在原有函数执行前的代码
        func()
        print('2:2')  # 在原有函数执行后的代码
    return wrapper

@my_decorator1
@my_decorator2
def my_function():
    print("这是一个需要被装饰的函数")
    
my_function()

# 1:1
# 2:1
# 这是一个需要被装饰的函数
# 2:2
# 1:2
```

***



#### 【2.5】进程 / 线程 / 协程

***

* **进程**：进程是操作系统分配资源和调度的基本单位。每个进程都有自己独立的内存空间，进程间通信需要使用特定的机制（管道，消息队列，共享内存等）。在 Python 中，可以使用 **multiprocessing** 模块来创建和管理进程。
* **线程**：线程是操作系统能够进行运算调度的最小单位。线程存在于进程中，同一进程下的线程可以共享该进程的资源。线程间的通信和协调相对简单，可以直接读写进程数据。Python 中的 **threading** 模块可以用来创建和管理线程。
* **协程**：协程是一种用户态的轻量级线程，协程的调度完全由应用程序来控制。协程依赖于线程，在一个线程中，协程以协作的方式运行，共享资源和状态而不需要额外的同步机制。相对于多线程，协程在处理 **I/O密集型** 任务时可以提升性能，因为它们可以在等待I/O操作（如网络请求，文件读写）完成前挂起当前任务，转而执行其他任务。Python 中通过 **asyncio** 可以创建和管理协程，使用 **async** 和 **await** 关键字来实现。

***



#### 【2.6】多进程 / 多线程

***

* **多进程**：使用 **multiprocessing** 模块可以创建和管理多个进程。每个进程都有自己独立的内存空间，因此可以并行执行多个任务，适合于 **CPU密集型** 的任务。每个进程都有自己的全局解释锁（GIL），因此可以充分利用多核 CPU。

* **多线程**：使用 **threading** 模块可以创建和管理多个线程。多线程适合 **I/O密集型** 的任务。Python 中由于全局解释锁（GIL）的存在，意味着在任意时刻只能有一个线程在解释锁中执行 Python 代码，从而限制了多线程在多核处理器上的性能。

***



