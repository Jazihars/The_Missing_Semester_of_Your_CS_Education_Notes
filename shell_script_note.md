# 如何高效地使用你的电脑？｜麻省理工学院《计算机教育中缺失的一课》（2）——shell脚本
在这个笔记中，我将记录麻省理工学院著名的 **《计算机教育中缺失的一课》** 的第2课的学习笔记。这门课的主要目的在于，教你如何高效地使用计算机中你常用的各种工具。众所周知，计算机已经深入了我们生活的方方面面。但是，你不一定真的会**高效地**使用它。这门课的目的就在于，教你如何高效地使用工具，帮助你高效地使用计算机。第2节课的内容是**shell脚本**

在上节课的笔记中已经说了，我是使用vscode的Remote-SSH来远程连接Linux机器的。因此，一般大多数情况下，我会直接使用vscode来编辑Linux机器上的脚本。目前我暂时还不太会使用vim。之后学习了vim，再使用vim来编辑脚本。也就是说，目前，我没有使用vim这个文本编辑工具，我直接使用vscode来编辑文本。

## 创建并运行一个bash脚本
首先，在我的当前目录下，使用下述命令，创建一个`test.sh`脚本：
``` bash
touch test.sh
chmod +x ./test.sh
```
注意，我们必须给`test.sh`添加`x`权限，否则，我们无法运行该脚本。而且要注意，运行脚本的命令必须是`./test.sh`，而不能是`test.sh`。直接写`test.sh`，Linux系统会去`PATH`里寻找有没有叫`test.sh`的，而只有 `/bin`、`/sbin`、`/usr/bin`、`/usr/sbin`等在`PATH`里，当前目录通常不在`PATH`里，所以写成`test.sh`是会找不到命令的，要用`./test.sh`告诉系统说，就在当前目录找。参考[runoob上的Shell教程](https://www.runoob.com/linux/linux-shell.html)。

然后我们就可以使用`code test.sh`命令用vscode打开`test.sh`这个脚本并开始编辑这个脚本了。依照惯例，我们的第一个脚本是`Hello World`脚本。在`test.sh`脚本中输入如下的代码：
``` bash
#!/bin/bash

echo "Hello World!"
```
然后在vscode的终端里运行下述命令：
``` bash
./test.sh
```
可以看到，终端里输出了如下的内容：
```
Hello World!
```
至此，我们已经学会了如何创建并运行一个bash脚本。

我们再来在`test.sh`脚本中输入如下的代码：
``` bash
#!/bin/bash

foo=bar
echo $foo
```
在终端中运行命令`./test.sh`，可以看到，终端里输出了下述内容：
```
bar
```
这个例子告诉我们：在bash中为变量赋值的语法是`foo=bar`，访问变量中存储的数值，其语法为`$foo`

如果我们在`test.sh`中键入如下的代码：
``` bash
#!/bin/bash

foo = bar
```
运行这个脚本，结果为：
```
./test.sh: line 3: foo: command not found
```
这说明，在bash脚本中，空格的使用需要格外小心。

## 字符串
首先，我们来看看单引号和双引号的区别。众所周知，单引号和双引号都可以用于创建字符串。但是，它们之间是有区别的。我们在`test.sh`中输入如下的代码：
``` bash
#!/bin/bash

foo=bar
echo "Value is $foo"
```
运行脚本，结果为：
```
Value is bar
```

如果我们把双引号换成单引号，我们在`test.sh`中输入如下的代码：
``` bash
#!/bin/bash

foo=bar
echo 'Value is $foo'
```
运行脚本，结果为：
```
Value is $foo
```
由此可以看出单引号和双引号的区别：**以`'`定义的字符串为原义字符串，其中的变量不会被转义，而以`"`定义的字符串会将变量的值进行替换。**

## 函数
Linux shell脚本当然也可以运行函数。我们现在Linux终端里测试下述命令：
``` bash
mcd aaaaa
```
得到了输出：
```
bash: mcd: command not found
```
可以看到，此时此刻还没有`mcd`这个命令。我们接下来在`test.sh`脚本中输入如下的代码，来定义`mcd`命令：
``` bash
#!/bin/bash

mcd () {
    mkdir -p "$1"
    cd "$1"
}
```
我们把`mcd`定义为一个函数，这个`mcd`函数的功能是：创建一个新目录并进入这个目录（`mkdir`的`-p`参数的含义是确保目录名称存在，如果不存在就创建一个）。
接下来我们在终端里运行下述source命令：
``` bash
source test.sh
```
虽然此时什么也没有发生。但是，如果再来运行下述命令：
``` bash
mcd aaaaa
```
可以看到，我们已经在当前路径下成功创建了一个aaaaa路径并进入了这个路径。

Linux shell可以通过`$`符号来表示一些特殊变量。例如，下面是一些常用的特殊变量：
``` bash
$0 - 脚本名
$1 到 $9 - 脚本的参数。 $1 是第一个参数，依此类推。
$@ - 所有参数
$# - 参数个数
$? - 前一个命令的返回值
$$ - 当前脚本的进程识别码
!! - 完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 sudo !!再尝试一次。
$_ - 上一条命令的最后一个参数。如果你正在使用的是交互式 shell，你可以通过按下 Esc 之后键入 . 来获取这个值。
```

比如，我们在终端里连续运行下述两个命令：
``` bash
mkdir aaaaa
cd $_
```
这两个命令实现了在当前路径下新创建一个`aaaaa`路径，并进入这个`aaaaa`路径的功能。

我们再来试试在终端里依次运行下述这两个命令：
``` bash
echo "Hello"
echo $?
```
依次输出了：
```
Hello
0
```
类似于C语言。正常的命令的返回值是0，表示没有发生错误。再次强调：`$?`表示前一个命令的返回值。

依次在终端里运行下述命令：
``` bash
grep foobar test.sh
echo $?
```
第一行命令什么都没有输出，第二行命令输出了一个`1`。这是因为，`test.sh`脚本里没有我们要找的字符串`foobar`，所以grep命令的返回值是错误码1。

在终端里依次运行下述命令：
``` bash
true
echo $?
```
结果输出了错误码0，表示没有错误。

在终端里依次运行下述命令：
``` bash
false
echo $?
```
结果输出了错误码1，表示有错误。

## 逻辑运算符
我们在终端里运行下述命令：
``` bash
false || echo "Hello world"
```
输出了下述结果：
```
Hello world
```

我们再来在终端里运行下述命令：
``` bash
true || echo "Hello world"
```
结果后面的输出字符串的`echo`命令被短路了，没有运行。这符合逻辑运算符的一般规则，即：如果运行前一个表达式就可以确定最终的结果，则后一个表达式会被短路。

我们在终端里运行下述命令：
``` bash
true && echo "Hello World"
```
结果终端里输出了：
```
Hello World
```

我们再来在终端里运行下述命令：
``` bash
false && echo "Hello World"
```
结果什么也没有输出。

我们再来在终端里分别运行下述命令：
``` bash
true ; echo "This will always run"
false ; echo "This will always run"
```
这两条命令都输出了字符串`This will always run`。用分号`;`连接的命令会被依次运行。

## `$`符号的更多应用
使用`$`符号可以实现更多的功能。比如，我们在`test.sh`脚本中键入如下的代码：
``` bash
#!/bin/bash

foo=$(pwd)
echo $foo
```
在终端里运行`./test.sh`命令来运行`test.sh`脚本，结果得到了如下的输出：
```
/home/users/XXX
```
`foo=$(pwd)`实现的功能是把当前路径存入`foo`这个变量。

我们再来在终端里运行下述命令：
``` bash
echo "We are in $(pwd)"
```
得到了输出：
```
We are in /home/users/XXX
```

在终端里测试下述命令：
``` bash
cat <(ls) <(ls ..)
```
这个命令输出了当前路径和当前路径的父路径下的所有内容。


从12:20左右开始看视频