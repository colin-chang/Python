# 算法

## 1. 算法
算法是独立存在的一种解决问题的方法和思想。算法是计算机处理信息的本质，因为计算机程序本质上是一个算法来告诉计算机确切的步骤来执行一个指定的任务。算法是一个庞杂的话题，我们这里只做一点最简单的探讨。

算法一般有以下五个特性：
* 输入。算法具有0个或多个输入
* 输出。算法至少有1个或多个输出
* 有穷性。 算法在有限的步骤之后会自动结束而不会无限循环，并且每一个步骤可以在可接受的时间内完成
* 确定性。算法中的每一步都有确定的含义，不会出现二义性
* 可行性。算法的每一步都是可行的，也就是说每一步都能够执行有限的次数完成

算法的效率主要由以下两个复杂度来评估：
* **时间复杂度**。评估执行程序所需的时间。可以估算出程序对处理器的使用程度。
* **空间复杂度**。 评估执行程序所需的存储空间。可以估算出程序对计算机内存的使用程度。

设计算法时，一般是要先考虑系统环境，然后权衡时间复杂度和空间复杂度，选取一个平衡点。不过，时间复杂度要比空间复杂度更容易产生问题，因此算法研究的主要也是时间复杂度，不特别说明的情况下，复杂度就是指时间复杂度。

## 2. 时间复杂度
假定计算机执行算法每一个基本操作的时间是固定的一个时间单位，那么有多少个基本操作就代表会花费多少时间单位。算法执行时间可以在一个层面上反应出算法的效率。

### 2.1 时间频度
一个算法执行所耗费的时间，从理论上是不能算出来的，必须上机运行测试才能知道。但我们不可能也没有必要对每个算法都上机测试，一个算法花费的时间与算法中语句的执行次数成正比例，所以我们可以简单的使用算法中语句执行次数来衡量其执行时间。一个算法中的语句执行次数称为语句频度或时间频度，记为`T(n)`。

```py
for i in range(2*n):
    for j in range(n):
        print(i,j)

for k in range(3*n):
    print(k)

for m in range(4):
    print(m)
```
如上面这段代码，<code>T(n) = 2n<sup>2</sup> + 3n + 4</code>
### 2.2 时间复杂度
前面提到的时间频度`T(n)`中，n称为问题的规模，当n不断变化时，时间频度`T(n)`也会不断变化。一般情况下，算法中基本操作重复执行的次数是问题规模n的某个函数，用`T(n)`表示，若有某个辅助函数`f(n)`，使得当n趋近于无穷大时，`T(n)`/`f(n)`的极限值为不等于零的常数，则称`f(n)`是`T(n)`的同数量级函数，记作`T(n)=O(f(n))`，它称为算法的渐进时间复杂度，简称时间复杂度。

算法的时间复杂度有以下三种情况：

* 算法完成工作最少需要多少基本操作，即最优时间复杂度
* 算法完成工作最多需要多少基本操作，即最坏时间复杂度
* 算法完成工作平均需要多少基本操作，即平均时间复杂度

对于最优时间复杂度，其价值不大，因为它没有提供什么有用信息，其反映的只是最乐观最理想的情况，没有参考价值。对于平均时间复杂度，是对算法的一个全面评价，因此它完整全面的反映了这个算法的性质。但另一方面，这种衡量并没有保证，不是每个计算都能在这个基本操作内完成。而且，对于平均情况的计算，也会因为应用算法的实例分布可能并不均匀而难以计算。最坏时间复杂度，则提供了一种保证，表明算法在此种程度的基本操作中一定能完成工作。因此，我们主要关注算法的最坏情况，我们提到的**时间复杂度一般情况下指的就是最坏时间复杂度**。

### 2.3 大O表示法
对于算法进行特别具体的细致分析虽然很好，但在实践中的实际价值有限。对于算法的时间性质和空间性质，最重要的是其数量级和趋势，这些是分析算法效率的主要部分。而计量算法基本操作数量的规模函数中那些常量因子可以忽略不计。例如，两个算法处理同样规模实例的代价分别为 3n<sup>2</sup>和 100n<sup>2</sup>，可以认为它们属于同一个量级，效率“差不多”，均为n <sup>2</sup>级。判断一个算法的效率时，往往只需要关注操作数量的最高次幂项，其它次要项和常数项可以忽略。

`O(f(n))`记录算法复杂度的方式成为大O表示法

时间复杂度的有以下几条基本计算规则：
* 基本操作。即只有常数项，认为其时间复杂度为O(1)
* 顺序结构。时间复杂度按加法进行计算
* 循环结构。时间复杂度按乘法进行计算
* 分支结构。时间复杂度取分支最大值

### 2.4 常见时间复杂度

执行次数函数举例|量级|非正式术语
:-|:-|:-
12|O(1)|常数阶
2n+3|O(n)|线性阶
3n<sup>2</sup>+2n+1|O(n<sup>2</sup>)|平方阶
6n<sup>3</sup>+2n2+3n+4|O(n<sup>3</sup>)|立方阶
2<sup>n</sup>|O(2<sup>n</sup>)|指数阶
5log<sub>2</sub>n+20|O(logn)|对数阶
2n+3nlog<sub>2</sub>n+19|O(nlogn)|nlogn阶
2n!+3|O(n!)|阶乘阶

* <small>常将log<sub>2</sub>n(以2为底n的对数)简写成logn</small>

![算法时间复杂度对比图](../img/datastructure/complexity.jpg)

::: tip 常见时间复杂度效率排序如下
O(1) > O(logn) > O(n) > O(nlogn) > O(n<sup>2</sup>) > O(n<sup>3</sup>) > O(2<sup>n</sup>) > O(n!) > O(n<sup>n</sup>)
:::

## 3. timeit
`timeit`模块的`Timer`类封装了用来测试代码的执行时间的方法。

`Timer(stmt='pass', setup='pass', timer=<timer function>)`
* `stmt`是要测试的代码语句(statment)。
* `setup`是运行代码时需要的配置，如导入相应模块等
* `timer`是一个定时器函数，与平台有关

`Timer.timeit([number=1000000])`方法测算并返回代码执行时间，number参数指定执行次数，默认为100万次。
```py
from timeit import Timer


def fun():
    lst = list(range(100))


timer = Timer("fun()", "from __main__ import fun")
print(timer.timeit(10000))  # 计算fun函数执行1W次的耗时 
```