# 流控制语句

## 1. 条件语句
### 1.1 if语句
```py
if condition1:
    do something
elif condition2:
    do something
elif condition3:
    do something
else:
    do something
```
* `if`之后判断条件可以省略括号，如果判断条件较长则可以使用括号后将条件换行
* 判断条件之后使用`:`
* 执行内容之前的代码缩进为一个tab键，或者4个空格。Tab和空格不可混用
* 缩进相同的代码视作一个代码块，而不是通过{}来界定
* Python当中不支持`switch`语法

```py
import random

player = int(input('请输入：剪刀(0)  石头(1)  布(2):'))
computer = random.randint(0, 2)

if ((player == 0 and computer == 2)
        or (player == 1 and computer == 0)
        or (player == 2 and computer == 1)):
    print('获胜，哈哈，你太厉害了')
elif player == computer:
    print('平局，要不再来一局')
else:
    print('输了，不要走，决战到天亮')
```

## 2. 循环语句
### 2.1 while

```py
while condition:
    do something
```

```py
# 求0-100之间偶数和
i = 0
sum = 0
while i <= 100:
    if i % 2 == 0:
        sum += i
    i = i + 1

print(sum)
```
::: warning
* Python不支持 `i++` / `i--` 等自增自减等语法
* Python不支持 `do while`语法
* Python支持`break`和`continue`语法
:::

```py
# 输出99乘法表
i = 1
while i <= 9:
    j = 1
    while j <= i:
        print("%d*%d=%d" % (j, i, i * j), end=" ")
        j += 1
    i += 1;
    print("")
```

### 2.2 for in
Python的`for in`循环与JavaScript类似,相当与C#的foreach
```py
for x in "name":
    print(x)

# 输出结果
n
a
m
e
```