[TOC]


# 基础语法

## 输入与输出

### 代码中要修改不可变数据会出现什么问题? 抛出什么异常?

> 代码不会正常的运行。会直接抛出TypeError异常

### 不用中间变量交换两个变量的值?

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

### print调用Python中底层的什么方法?

> print方法默认调用sys.stdout.write方法，即往控制台打印字符串

### 下面这段代码的输出结果将是什么？请解释？

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

### 简述对input()函数的理解?

> 在Python3中，input()获取用户的输入，无论用户输入的是什么，获取到的都是字符串类型。
>
> 在Python2中有raw_input()和input()，raw_input()和Python3中的input()作用是一样的，而input()则是输入的是什么类型，获取到的就是什么类型。

## 条件与循环

### 阅读下面的代码，写出A0, A1至An的最终值。

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

### range和xrange的区别?

> range返回的是一个列表，是直接开辟一块内存空间来保存列表
>
> xrange返回一个生成器，是边循环边使用，只用当使用时才会开辟内存空间
>
> 因此当列表长度很大时，使用xrange性能要比range好

### 考虑一下Python代码，如果运行结束，命令行中的运行结果是什么?

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

### 以下Python程序的输出?

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

## 文件操作

