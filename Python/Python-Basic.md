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

浅拷贝会创建新的对象，其内容并不是原对象本身的引用，而是原对象内第一层对象的引用。

浅拷贝有三种形式：切片操作，工厂函数、copy模块中的copy函数。

```python
a = [1, 2, "hello", ['python', 'java']]
# 切片操作
b = a[:]
b = [x for x in a]
# 工厂函数
b = list(a)
# copy 函数
import copy
b = copy.copy(a)
```

浅拷贝产生的列表b不再是列表a了，使用`is`判断可以知道它们并不是同一个对象，使用`id()`查看，它们也不指向同一片内存空间。但是当使用`id(x) for x in a` 和 `id(x) for x in b`来查看a和b中元素的地址时，可以看到二者包含的元素的地址是相同的。

在这种情况下，列表a和b是不同的对象，修改列表a理论上不会影响列表b。但是要注意的是，浅拷贝仅仅只是拷贝了一层。在列表a中有一个嵌套的list，如果修改了这个元素，则列表b也会随之收到影响。

```python
a = [1, 2, "hello", ['python', 'java']]

# 进行浅拷贝
b = a[:]
print(a)  # [1, 2, "hello", ['python', 'java']]
print(b)  # [1, 2, "hello", ['python', 'java']]

# 修改不可变类型
a[0] = 'world'
print(a)  # ['world', 2, "hello", ['python', 'java']]
print(b)  # [1, 2, "hello", ['python', 'java']]

# 修改可变类型中的元素
a[3].append('go')
print(a)  # ['world', 2, "hello", ['python', 'java', 'go']]
print(b)  # [1, 2, "hello", ['python', 'java', 'go']]
```

> 如上述代码那样，修改外层元素则会修改它的引用，让它们指向别的位置。而修改嵌套的列表中元素，列表的地址并未发生变化，指向的都是同一个位置。但如果是使用`a[3]=[1, 2, 3]`直接修改第一层的引用，则列表b不会受到影响。

#### 5.2.3、深拷贝

深拷贝只有一种形式：copy.deepcopy()

深拷贝和浅拷贝对应，深拷贝是拷贝了对象的所有元素，包括多层嵌套的元素。因此，深拷贝的时间和空间开销更高。

```python
import copy
a = [1, 2, "hello", ['python', 'java']]
# 进行深拷贝
b = copy.deepcopy()
print(a)  # [1, 2, "hello", ['python', 'java']]
print(b)  # [1, 2, "hello", ['python', 'java']]
# 修改可变对象的元素
a[3].append('go')
print(a)  # [1, 2, "hello", ['python', 'java', 'go']]
print(b)  # [1, 2, "hello", ['python', 'java']]
```

> 即使嵌套的列表具有更深的层次，也不会产生任何影响，因为深拷贝拷贝出来的对象是一个全新的的对象，与原来的对象没有任何的关联。

#### 5.2.4、拷贝的注意点

对于非容器类型，例如数字、字符以及其它的`原子`类型，没有拷贝一说，产生的都是对原对象的引用。

如果元组变量包含原子类型对象，即使采用可深拷贝，也只能得到浅拷贝。

### 5.3、`__init__` 和 `__new__` 的区别?

`__init__`调用在对象创建后，是对对象进行初始化操作。

`__new__`是在对象创建之前创建一个对象，并将创建的对象返回给`__init__`。

### 5.4、Python中如何生成随机数?

> 在Python中用于生成随机数的模块是`random`

```python
import random

random.random()  # 生成一个0-1之间的随机浮点数
random.uniform(a, b)  # 生成[a, b]之间的浮点数
random.randint(a, b)  # 生成[a, b]之间的整数
random.randrange(a, b, step)  # 在指定的集合[a, b)中，以step为基数随机取一个数
random.choice(sequence)  # 从特定序列中随机取一个元素，这里的序列可以是字符串、列表、元组等
```

### 5.5、输入某年某月某日，判断这一天是这一年的第几天?

```python
import datetime

def day_of_year():
    year = input('请输入年份:')
    month = input('请输入月份:')
    day = input('请输入天:')
    date1 = datetime.date(year=int(year), month=int(month), day=int(day))
    date2 = datetime.date(year=int(year), month=1, day=1)
    return (date1-date2).days + 1
```

### 5.6、如何打乱一个排好序的list对象?

```python
import random
a = [1, 2, 3, 4, 5]
random.shuffle(a)
print(a)
```

### 5.7、分别说明os.path和sys.path的作用?

os.path 主要用于对系统路径文件的操作。

sys.path 主要用于对Python解释器的系统环境参数的操作（即动态改变Python解释器搜索路径）。

### 5.8、Python中os模块常见方法?

```python
os.remove()  		# 删除文件
os.rename()  		# 重命名文件
os.walk() 	 		# 生成目录树下的所有文件名
os.chdir()   		# 改变目录
os.mkdir()   		# 创建目录
os.makedirs()		# 创建多层目录
os.rmdir()   		# 删除目录
os.removedirs() 	# 删除多层目录
os.listdir()		# 列出指定目录的文件
os.getcwd()			# 获取当前工作目录
os.chmod()			# 改变目录的权限
os.path.basename()	# 去掉目录路径，返回文件名
os.path.dirname()	# 去掉文件名，返回目录路径
os.path.join()		# 将分离的各个部分组合成一个路径名
os.path.split()		# 返回(dirname(), basename())的一个元组
os.path.splitext()	# 返回(filename, extension)的一个元组
os.path.getatime()	# 返回最近访问时间
os.path.getctime()	# 返回最近创建时间
os.path.getmtime()  # 返回最近修改时间
os.path.getsize()	# 返回文件大小
os.path.exists()	# 返回是否存在
os.path.isabs()		# 返回是否为绝对路径
os.path.isdir()		# 返回是否为目录
os.path.isfile()	# 返回是否是文件
```

### 5.9、Python的sys模块的常用方法?

```python
sys.argv			# 以list方式返回命令行参数，第一个元素是程序本身路径
sys.modules.keys()	# 返回所有已经导入的模块列表
sys.exc_info()		# 获取当前正在处理的异常类，返回(exc_type, exc_value, exc_traceback)元组
sys.exit(n)			# 退出程序，正常退出时n=0
sys.hexversion		# 获取Python解释程序的版本值
sys.version			# 获取Python解释程序的版本信息
sys.maxint			# 最大的int值
sys.maxunicode		# 最大的unicode值
sys.modules			# 返回系统导入的模块，key为模块名，value为模块
sys.path			# 返回模块的搜索路径，初始化时使用PYTHONPATH的值
sys.platform		# 返回操作系统平台名称
sys.stdout			# 标准输出
sys.stdin			# 标准输入
sys.stderr			# 错误输出
sys.exc_clear()		# 清除当前线程所出现的当前或最近的错误信息
sys.exec_prefix		# 返回平台独立的Python文件安装的位置
sys.byteorder		# 本地字节规则指示器
sys.copyright		# 返回Python版权相关的东西
sys.api_version		# 解释器C的API版本
sys.version_info	# 获取Python的版本号
```

### 5.10、什么是unittest?

在Python中，unittest是Python的单元测试框架。它有支持共享搭建、自动测试、测试中暂停代码、将不同测试迭代成一组等功能。

### 5.11、模块和包是什么?

在Python中，模块是搭建程序的一种方式。每一个Python代码文件都是一个模块，并且可以引用其他的模块，例如对象和属性。

包则是包含许多Python模块的文件夹。一个包可以包含模块和子包。

****

## 6、Python特性

### 6.1、Python是强语言类型还是弱语言类型？

> Python是强类型的动态脚本语言

强类型：不允许不同类型相加。

动态：不使用显示的数据声明，且确定一个变量的类型是在第一次给它赋值的时候。

脚本语言：一般也是解释型语言，运行代码只需要一个解释器，不需要编译。

### 6.2、谈一下什么是解释性语言，什么是编译性语言？

计算机不能直接理解高级语言，只能直接理解机器语言，所以必须要把高级语言翻译成机器语言，计算机才能执行高级语言编写的程序。

解释性语言在运行程序的时候才会进行翻译。

编译性语言在程序执行之前有一个专门的编译过程，把程序编译成机器语言(可执行文件)。

### 6.3、Python中有日志吗？如何使用？

有日志。

Python中自带了logging模块，可以调用logging.basicConfig()方法来配置需要的日志等级和相应的参数，Python解释器会按照配置的参数生成相应的日志。

### 6.4、Python是如何进行类型转换的？

内建函数封装了各种转换函数，可以使用目标类型关键字强制类型转换，进制之间的转换可以用`int(str, base='n')`将特定进制的字符串转为十进制，再用相应的进制转换函数将十进制转换为目标进制。

可以使用内置函数直接转换的有

* list -> tuple
* tuple -> list 

### 6.5、Python2与Python3的区别

#### 6.5.1、核心类差异

##### 6.5.1.1、Python3对Unicode字符的原生支持

Python2中使用ASCII码作为默认编码方式导致了string有两种类型，str和unicode。Python3只支持unicode的string。

| Python2 | Python3 | 表现 | 转换   | 作用 |
| ------- | ------- | ---- | ------ | ---- |
| str     | bytes   | 字节 | encode | 存储 |
| unicode | str     | 字符 | decode | 显示 |

##### 6.5.1.2、Python3采用绝对路径的方式进行import

Python2中相对路径的import会导致标准库导入变的困难（例如，在同一目录下有file.py，如何同时导入这个文件和标准库file）。Python3中这一点被修改，如果还需要导入同一目录的文件则必须使用绝对路径。

##### 6.5.1.3、老式类和新式类

Python2中存在老式类和新式类的区别，Python3统一采用新式类。新式类声明要求继承object，必须使用新石磊应用多重继承。

##### 6.5.1.4、缩进

Python3使用更加严格的缩进。Python2中的缩进机制中，1个tab和8个space是等价的，所以在缩进中可以同时允许tab和space共存。这种等价机制会导致部分IDE使用存在问题。Python3中1个tab职能找另一个tab替代，因此tab和space共存会导致报错`TabError: inconsistent use of tabs and spaces in indentation`。

#### 6.5.2、废弃类差异

* print语句被Python3废弃，统一使用print函数
* exec语句被Python3废弃，统一使用exec函数
* execfile语句被Python3废弃，推荐使用exec(open('filename').read())
* 不相等操作符`<>`被Python3废弃，统一使用`!=`
* long整数类型被Python3废弃，统一使用int
* xrange函数被Python3废弃，统一使用range，Python3中的range机制也进行修改并提高了大数据集生成的效率
* Python3中的这些方法不再返回list对象：字典相关的keys(),values(),items(),zip(),map(),filter()，但是可以通过list强行转换
* 迭代器iterator的next函数被Python3废弃，统一使用next(iterator)
* raw_input函数被Python3废弃，统一使用input函数
* 字典变量的has_key函数被Python废弃，统一使用关键词in
* file函数被Python3废弃，统一使用open来处理文件
* apply函数被Python3废弃
* 异常StandardError被Python3废弃，统一使用Exception

#### 6.5.3、修改类差异

##### 6.5.3.1、浮点数除法操作符`/`和`//`的区别

`/`：在Python2中，如果两个整形数进行运算，结果为整形，若两个数中有一个为浮点数，则结果为浮点数。而在Python3中，运算结果不再根据参加运算的数的类型。

`//`:在Python2中，返回小于运算结果的最大整数，从类型上讲，与`/`运算符返回类型逻辑一致。Python3和Python2运算结果一样。

##### 6.5.3.2、异常抛出和捕捉机制区别

```python
# Python2
raise IOError, 'file error'  # 抛出异常
except NameError, err  # 捕捉异常

# Python3
raise IOError('file error') #  抛出异常
except NameError as err  # 捕捉异常
```

##### 6.5.3.3、for循环变量值区别

```python
# Python2中，for循环会修改外部相同名称变量的值
i = 1
print('result: ', [i for i in range(5)])
print('after: i = ', i) # i = 4

# Python3，for循环不会修改外部相同名称变量的值
i = 1
print('result: ', [i for i in range(5)])
print('after: i = i', i) # i = 1
```

##### 6.5.3.4、round函数返回值区别

```python
# Python2, round函数返回float类型
isinstance(round(15.5), float) # True

# Python3，round函数返回int类型
isinstance(round(15.5), int) # False
```

##### 6.5.3.5、比较操作符区别

```python
# Python2 中任意两种数据类型都可以比较
11 < 'test' # True

# Python3 中只有同一数据类型的对象可以比较
11 < 'test' # TypeError: unorderable types: int() < str()
```

### 6.6、有什么手段能提升Python程序运行方面的性能？

* 使用多进程，充分利用机器多核性能
* 对于性能影响较大的部分代码，可以使用C或C++编写
* 对于IO阻塞造成的性能影响，可以使用IO多路复用来解决
* 尽量使用Python的内建函数
* 尽量使用局部变量

### 6.7、Python中的作用域？

在Python中，一个变量的作用域总是由在代码中被赋值的地方所决定。当Python遇到一个变量的话，会按照以下顺序进行搜索：

本地作用域(Loacl) -> 当前作用域被嵌入的本地作用域(Enclosing locals) -> 全局/模块作用域(Global) -> 内置作用域(Built-in)

### 6.8、什么是Python？

* Python 是一种编程语言，它有对象、模块、线程、异常处理和自动内存管理。
* Python是一种解释型语言，Python在代码运行之前不需要解释。
* Python是动态类型语言，在声明变量时，不需要指明变量的类型。
* Python适合面向对象的编程，因为它支持通过组合与继承的方式定义类。
* 在Python语言中，函数是第一类对象。
* Python编写代码块，但是运行速度比编译型语言通常要慢。
* Python用途广泛，常被用做“胶水语言”，可以帮助其他语言和组件改善运行状况。
* 使用Python，程序员可以专注于算法和数据结构的设计，而不用处理底层的细节。

### 6.9、什么是Python自省?

Python自省是Python具有的一种能力，使程序员在编写面向对象语言的时候，能够获得对象的类型，这为程序员提供了极大的灵活性和控制力。

[详情](http://www.woola.net/detail/2016-08-28-python-object-introspection.html) 

### 6.10、什么是Python的命名空间？

在Python中，所有的名字都存在于一个空间中，他们在该空间中存在和被操作。它就好像一个盒子，每一个变量名字都对应装着一个对象。当查询变量的时候，会从该盒子里寻找相应的对象。

