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

