[TOC]


# 一、基础语法

## 1、输入与输出

### 1.1、代码中要修改不可变数据会出现什么问题? 抛出什么异常?

> 代码不会正常的运行。会直接抛出TypeError异常

### 1.2、不用中间变量交换两个变量的值?

```python
a = 1
b = 2

# 方法一
a, b = b, a

# 方法二
a = a+b
b = a-b
a = a-b

# 方法三
a = a^b
b = b^a
a = a^b
```

### 1.3、print调用Python中底层的什么方法?

> print方法默认调用sys.stdout.write方法，即往控制台打印字符串

### 1.4、下面这段代码的输出结果将是什么？请解释？

```python
class Parent(object):
    x = 1

class Child1(Parent):
    pass

class Child2(Parent):
    pass

print Parent.x, Child1.x, Child2.x
Child1.x = 2
print Parent.x, Child1.x, Child2.x
Parent.x = 3
print Parent.x, Child1.x, Child2.x

# 运行结果
1 ,1 ,1  # 继承自父类的类属性x，因此都一样，因为指向了同一块内存地址。
1, 2, 1  # 修改了Child1，Child1的x指向了新的内存地址
3, 2, 3  # 修改了Parent，Parent的x指向了新的内存地址，Child1之前已修改过，Child2还是继承父类的类属性
```

### 1.5、简述对input()函数的理解?

> 在Python3中，input()获取用户的输入，无论用户输入的是什么，获取到的都是字符串类型。
>
> 在Python2中有raw_input()和input()，raw_input()和Python3中的input()作用是一样的，而input()则是输入的是什么类型，获取到的就是什么类型。

****

## 2、条件与循环

### 2.1、阅读下面的代码，写出A0, A1至An的最终值。

```python
A0 = dict(zip('a', 'b', 'c', 'd', 'e'), (1, 2, 3, 4, 5))
A1 = range(10)
A2 = [i for i in A1 if i in A0]
A3 = [A0[s] for s in A0]
A4 = [i for i in A1 if i in A3]
A5 = {i:i*i for i in A1}
A6 = [[i, i*i] for i in A1]

# 结果为
A0 = {'a': 1, 'b': 2, 'c': 3, 'd': 4. 'e': 5}
A1 = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
A2 = []
A3 = [1, 2, 3, 4, 5]
A4 = [1, 2, 3, 4, 5]
A5 = {0:0, 1:1, 2:4, 3:9, 4:16, 5:25, 6:36, 7:49, 8:64: 9:81}
A6 = [[0, 0], [1, 1], [2, 4], [3, 9], [4, 16], [5, 25], [6, 36], [7, 49], [8, 64], [9,81]
```

### 2.2、range和xrange的区别?

> range返回的是一个列表，是直接开辟一块内存空间来保存列表
>
> xrange返回一个生成器，是边循环边使用，只用当使用时才会开辟内存空间
>
> 因此当列表长度很大时，使用xrange性能要比range好

### 2.3、考虑一下Python代码，如果运行结束，命令行中的运行结果是什么?

```python
l = []
for i in xrange(10):
    l.append({'num': i})
print l

# 运行结果为
[{'num': 0}, {'num': 1}, {'num': 2}, {'num': 3}, {'num': 4}, {'num': 5}, {'num': 6}, {'num': 7}, {'num': 8}, {'num': 9}]
```

再考虑以下代码，运行结束后的结果是什么?

```python
l = []
a = {'num': 0}
for i in xrange(10):
    a['num'] = i
    l.append(a)
print l

# 运行结果为
[{'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}, {'num': 9}]
```

以上两段代码的运行结果是否相同，如果不相同，原因是什么?

> 原因是：字典是可变对象，在下方的代码l.append(a)的操作是把字典a的引用传到列表l中，当后续操作修改a['num']的值的时候，l的值也会随之改变，相当于浅拷贝。

### 2.4、以下Python程序的输出?

```python
for i in range(5, 0, -1):
    print(i)

# 输出结果为
5
4
3
2
1
```

****

## 3、文件操作

### 3.1、4G内存怎么读取一个5G的数据?

> 方法一：
>
> 可以通过生成器，分多次读取，每次读取数量较少的数据进行处理，处理结束后再读取后面的数据。
>
> 方法二：
>
> 可以通过linux命令`split`切割成小文件，然后再对数据进行处理，此方法效率较高，可以按照行数切割，也可以按照文件的大小进行切割。[spilit使用方法](https://blog.csdn.net/mxgsgtc/article/details/12048919)

### 3.2、如何通过生成器在小内存的情况下读取大文件?需要考虑哪些问题?

```python
# 不使用生成器
def get_lines():
    l = []
    with open('file.txt', 'rb') as f:
        for eachline in f:
            l.append(eachline)
    return l

if __name__ == '__main__':
    for e in get_lines():
        process(e)  # 处理每一行数据
```

```python
# 使用生成器
def get_lines():
    l = []
    with open('file.txt', 'rb') as f:
        data = f.readlines(60000)
    l.append(data)
    yield l
```

> 需要考虑的问题有：如果内存无法一次性读取大容量的文件，则需要分批读入。分批读入要记录每次读入数据的位置。分批每次读入数据太小，则会在读取操作上花费过多的时间。

### 3.3、read、readline和readlines的区别?

> read : 读取整个文件。
>
> readline : 读取下一行，使用生成器方法。
>
> readlines : 读取整个文件到一个迭代器以供我们遍历。

### 3.4、补充缺失的代码

```python
def print_directory_contents(sPath):
    """
    这个函数接收文件夹的名称作为输入参数
    返回该文件夹中的文件路径
    以及其包含文件夹中文件的路径
    """
    # 补充代码，代码如下
    import os
    for sChild in os.listdir(sPath):
        sChildPath = os.path.join(sPath, sChild)
        if os.path.isdir(sChildPath):
            print_directory_contents(sChildPath)
        else:
            print(sChildPath)
```

## 4、异常

### 4.1、在except中return后还会不会执行finally中的代码?怎么抛出自定义异常?

> 会继续执行finally中的代码。
>
> 使用raise可以抛出自定义异常。

### 4.2、介绍一下except的作用和用法

> except: # 捕获所有异常
>
> except <异常名>  # 捕获指定异常
>
> except <异常名1， 异常名2>  # 捕获异常1或异常2
>
> except <异常名> as <数据>  # 捕获指定异常及其附加的数据
>
> except <异常名1， 异常名2> as <数据>  # 捕获异常名1或者异常名2，及附加的数据

****

## 5、模块与包

### 5.1、常用的Python标准库都有哪些?

* 标准库
  * os 操作系统
  * time 时间
  * random 随机
  * threading 线程
  * multiprocessing 进程
  * queue 队列
  * re 正则
* 第三方库
  * requests
  * pipenv
  * scrapy
  * flask
  * celery
  * numpy
  * pandas

### 5.2、赋值、浅拷贝和深拷贝的区别?

#### 5.2.1、赋值

在Python中，对象的赋值就是简单的对象引用，如下所示

```python
a = [1, 2, "hello", ['python', 'java']]
b = a
```

在上述情况下，a和b是一样的，它们指向同一片内存，a和b都是那一片内存的别名。

我们可以使用`b is a`来判断，如果返回True，则表明它们的地址相同，内容相同，也可以使用`id()`函数来查看两个列表的地址是否相同。

赋值操作(包括对象作为参数、返回值)`不会`开辟新的内存空间，它只是复制了对象的引用。也就是说除了b这个名字之外，没有其他的内存开销。修改了a同时也会影响了b，同理修改了b，也会影响了a。

> 赋值就是拷贝了对象的引用，不会开辟内存空间。

#### 5.2.2、浅拷贝

