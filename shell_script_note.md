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


## 一个简单脚本的例子
接下来我们看一个bash脚本的例子。在`test.sh`中输入如下的代码：
``` bash
#!/bin/bash

echo "Starting program at $(date)" # date会被替换成日期和时间

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 如果模式没有找到，则grep退出状态为 1
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```
对这段代码，我们来逐行讲解。首先，这段代码的第1行代码是：
``` bash
echo "Starting program at $(date)"
```
我们在Linux终端里分别来运行下述两行代码：
``` bash
echo "Starting program at $(date)"
echo 'Starting program at $(date)'
```
结果分别为：
```
Starting program at Sat May 14 12:42:52 CST 2022
Starting program at $(date)
```
再次强调，单引号和双引号是不同的。只有在双引号中的特殊字符才会被替换。
这段代码的第二行代码是：
``` bash
echo "Running program $0 with $# arguments with pid $$"
```
我们尝试一下，直接在Linux终端里运行这行代码会发生什么。直接在Linux终端里运行这行代码，得到了输出：
```
Running program /bin/bash with 0 arguments with pid 221144
```
再次复习，`$0`的含义是脚本名，`$#`的含义是参数个数，`$$`的含义是当前脚本的进程识别码。因此，直接在Linux终端里运行`echo "Running program $0 with $# arguments with pid $$"`这行代码，此时的脚本名是`/bin/bash`，参数个数是0。

如果我们在一个空白脚本`mytest.sh`中键入下述代码：
``` bash
#!/bin/bash

echo "Starting program at $(date)" # date会被替换成日期和时间

echo "Running program $0 with $# arguments with pid $$"
```
我们在终端里运行下述命令：
``` bash
./mytest.sh
```
得到了如下的输出：
```
Starting program at Sat May 14 12:51:45 CST 2022
Running program ./mytest.sh with 0 arguments with pid 225118
```
可以看到，当前的脚本名已经变成了`./mytest.sh`，参数个数仍然是0。

如果我们此时此刻再在终端中运行下述命令（当前目录下存在`a.txt`，`b.txt`，`c.txt`这三个文件）：
``` bash
./mytest.sh a.txt b.txt c.txt
```
得到了如下的输出：
```
Starting program at Sat May 14 12:53:26 CST 2022
Running program ./mytest.sh with 3 arguments with pid 225759
```
可以看到，此时的参数个数变成了3。如此我们应该明白了`$#`的含义。`$#`的含义是参数个数，它表示在所运行的脚本后面跟着的参数个数。

接下来的代码是一个`for`循环：
``` bash
for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 如果模式没有找到，则grep退出状态为 1
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```
再次复习，`$@`的含义是所有参数。因此，我们来做一个实验，如果我们在`mytest.sh`脚本中键入如下的代码：
``` bash
#!/bin/bash

for file in "$@"; do
    echo "$file"
done
```
在Linux终端里运行下述命令：
``` bash
./mytest.sh a.txt b.txt c.txt
```
Linux终端里输出了下面的内容：
```
a.txt
b.txt
c.txt
```
由此我们更好地明白了`$@`的含义。`$@`的含义是所有参数，因此，`for file in "$@"; do`这行代码会遍历Linux终端里`./mytest.sh a.txt b.txt c.txt`命令中的三个参数`a.txt`、`b.txt`和`c.txt`。


`for`循环中的第一行代码是：
``` bash
grep foobar "$file" > /dev/null 2> /dev/null
```
为了更好地理解这行代码究竟做了什么事情，我们再来做一些额外的实验。首先，在当前目录下建立三个文本文件`a.txt`、`b.txt`和`c.txt`。在`a.txt`中输入如下的内容：
```
foobar
foobar
foobar
```
在`b.txt`中什么都不输出，让`b.txt`是一个空白的文本文件。在`c.txt`中输入如下的内容：
```
asdfs
```

首先，我们在Linux终端里依次运行下述的命令：
``` bash
grep foobar a.txt
echo $?
```
分别得到了输出：
```
foobar
foobar
foobar
```
和
```
0
```

然后，我们在Linux终端里依次运行下述的命令：
``` bash
grep foobar b.txt
echo $?
```
第一行命令什么都没有输出，第二行命令输出了`1`。

最后，我们在Linux终端里依次运行下述的命令：
``` bash
grep foobar c.txt
echo $?
```
结果和上面一样。第一行命令什么都没有输出，第二行命令输出了`1`。
根据[Linux grep 命令教程](https://www.runoob.com/linux/linux-comm-grep.html)，Linux的`grep`命令用于查找文件里符合条件的字符串。`grep`指令用于查找内容包含指定的范本样式的文件，如果发现某文件的内容符合所指定的范本样式，预设`grep`指令会把含有范本样式的那一列显示出来。若不指定任何文件名称，或是所给予的文件名为`-`，则`grep`指令会从标准输入设备读取数据。如果成功搜索到了指定字符串，则返回错误码0，表示没有错误。如果没有搜索到指定字符串，则返回错误码1。

我们再来尝试在Linux终端里运行下述命令：
``` bash
grep foobar a.txt > b.txt 2> c.txt
```
结果终端里没有输入任何内容，但是`b.txt`中变成了下述这样：
```
foobar
foobar
foobar

```
（`b.txt`最后确实有一个空行）。`c.txt`仍然是一个空白文本文件。
如果我们把`a.txt`设为一个空白脚本，则再次在终端里运行`grep foobar a.txt > b.txt 2> c.txt`命令，则`b.txt`和`c.txt`的结果都是空白文本文件。
参考问题[What does the 2> mean on the Unix command-line?](https://stackoverflow.com/questions/19108895/what-does-the-2-mean-on-the-unix-command-line)可以明白，`2>`表示对标准错误进行重定向。重定向一个内容到`/dev/null`就意味着丢弃那个内容。
至此，`grep foobar "$file" > /dev/null 2> /dev/null`这行代码的含义已经完全搞清楚了，我们并不关心找到的内容以及是否找到了foobar字符串，因此，我们把这些内容都丢弃。

接下来是一个if条件语句：
``` bash
if [[ $? -ne 0 ]]; then
    echo "File $file does not have any foobar, adding one"
    echo "# foobar" >> "$file"
fi
```
再次复习，`$?`的含义是前一个命令的返回值。因此，我们只是关心前一个命令`grep foobar "$file" > /dev/null 2> /dev/null`究竟有没有找到`foobar`字符串。如果找到了就返回0，没有找到就返回1。

根据教程里的观点，在bash中进行比较时，尽量使用双方括号`[[ ]]`而不是单方括号`[ ]`，这样会降低犯错的几率。也可以参考[这里的教程](http://mywiki.wooledge.org/BashFAQ/031)。

`if [[ $? -ne 0 ]]; then`这行代码中的`-ne`表示的是比较左边和右边，如果不相等，则输出`true`（参考[这里的更多例子](http://mywiki.wooledge.org/BashFAQ/031)）。比如，我们在Linux终端中可以运行下述命令：
``` bash
[[ 6 -ne 20 ]] && echo "6 is not equal to 20"
```
输出了结果：
```
6 is not equal to 20
```

因此，上面这个if语句：
``` bash
if [[ $? -ne 0 ]]; then
    echo "File $file does not have any foobar, adding one"
    echo "# foobar" >> "$file"
fi
```
的含义是：如果在所查找的文件中没有找到`foobar`字符串（意即，前一个命令的返回值`$?`不为0），然后就输出一个字符串`"File $file does not have any foobar, adding one"`，并把`"# foobar"`字符串附加到那个文件里。

最后，我们来在Linux终端里测试一下下述命令（其中`a.txt`中有3个`foobar`，`b.txt`和`c.txt`中没有`foobar`）：
``` bash
./test.sh a.txt b.txt c.txt
```
Linux终端里输出了如下的结果：
```
Starting program at Sat May 14 13:48:21 CST 2022
Running program ./test.sh with 3 arguments with pid 245397
File b.txt does not have any foobar, adding one
File c.txt does not have any foobar, adding one
```
可以看到，`b.txt`和`c.txt`中被添加了字符串`# foobar`。

至此，这个比较复杂的bash脚本就分析完成了。这是我们学习的第一个bash脚本。我们应该会写一些简单的bash脚本了。

## 通配符
我们可以使用通配符来查找特定类型的文件或目录。例如，我们想看看当前目录下的所有`.txt`类型的文件，则可以在Linux终端里运行如下的命令：
``` bash
ls *.txt
```
得到了如下的输出：
```
a.txt  b.txt  c.txt  de.txt
```
我们再来在Linux终端里运行下述命令：
``` bash
ls ?.txt
```
得到了如下的输出：
```
a.txt  b.txt  c.txt
```
这就是一个典型的通配符用法了。通配符`*`代表任意多个字符（0个到多个）。`？`代表一个字符。

当前路径下有`project1`、`project2`和`project32`这三个路径。我们在Linux终端里运行下述命令：
``` bash
ls project*
```
结果Linux终端里输出了下述内容：
```
project1:

project2:

project32:
```
我们再来在Linux终端里测试下述命令：
``` bash
ls project?
```
结果Linux终端里输出了下述内容：
```
project1:

project2:
```
至此，我们已经学会了通配符的最基本用法。

## 对文件进行强制类型转换
在Linux系统中，我们可以通过`convert`命令直接对一个文件进行强制类型转换。比如，当前目录下有一个名为`cat11.png`的图片，我们可以通过下述两条命令之一将这个图片转换成`.jpg`格式的图片：
``` bash
convert cat11.png cat11.jpg
convert cat11.{png,jpg}
```
运行上述两个命令之一，我们得到了一张新的图片`cat11.jpg`。之前的图片`cat11.png`保持不变。

## 同时创建多个文件
我们可以使用`{}`来同时创建多个文件。比如，我们可以在Linux终端里运行下述命令：
``` bash
touch {a,b,c,de}.txt
```
这条命令的功能是：在当前路径下同时创建4个文本文件，分别是`a.txt`、`b.txt`、`c.txt`和`de.txt`。

我们也可以用类似的语法来同时创建多个路径。例如，在Linux终端里运行下述命令：
``` bash
mkdir project{1,2,32}
```
这条命令在当前路径下同时创建了三个空路径，分别是`project1`、`project2`和`project32`。

我们再来试试笛卡儿积。首先，在Linux终端里运行下述命令：
``` bash
mkdir project{1,2}
```
同时创建两个空路径`project1`和`project2`。然后，我们在Linux终端里运行下述命令：
``` bash
touch project{1,2}/{a,b,c}.txt
```
然后，我们在Linux终端里依次运行下述命令：
```
ls project1
ls project2
```
分别看到了终端里如下的输出：
```
a.txt  b.txt  c.txt
a.txt  b.txt  c.txt
```
上述`touch project{1,2}/{a,b,c}.txt`命令就是使用笛卡儿积的方法来批量创建文件。

我们也可以在Linux终端里使用下述命令：
``` bash
mkdir project1 project2
```
来在当前路径下同时创建两个空路径`project1`和`project2`。

然后我们可以在Linux终端里使用下述命令来分别在两个路径中创建文件：
``` bash
touch project1/a.txt project2/b.txt
```
然后我们在Linux终端里依次运行下述两个命令：
``` bash
ls project1
ls project2
```
分别得到了输出：
```
a.txt
b.txt
```
因此，使用`touch project1/a.txt project2/b.txt`命令可以实现同时在两个路径中创建文件。

## 比较两个路径的不同
我们在Linux终端里运行下述命令：
``` bash
diff <(ls project1) <(ls project2)
```
得到了输出：
```
1c1
< a.txt
---
> b.txt
```
`1c1`表示两个路径的第一个文件就是不同的。`<`表示后面的路径比前面的路径少了1个内容，`>`表示后面的路径比前面的路径多了1个内容。

## 使用Python脚本来与bash交互
我们不光可以使用bash脚本来与Linux shell交互，我们也可以使用Python脚本来与bash交互。比如，我们在当前目录下创建空白Python脚本`see.py`，然后在`see.py`中键入如下的代码：
``` python
import sys

for arg in sys.argv[1:]:
    print(arg)
```
然后在Linux终端里运行下述命令（本次使用Python3.9.12解释器）：
``` bash
python see.py aaa bb c
```
在Linux终端里输出了下述内容：
```
aaa
bb
c
```
根据[Python sys.argv官方文档](https://docs.python.org/3/library/sys.html#sys.argv)，`sys.argv`表示传递给Python脚本的命令行参数列表。`argv[0]`表示这个Python脚本的名称。

## 查找
参考[Linux find 命令教程](https://www.runoob.com/linux/linux-comm-find.html)，进行如下的实验，熟悉find命令的用法。

在当前路径下创建`./project1/src`和`./project2/src`这两个路径，然后在Linux终端里运行下述命令：
``` bash
find . -name src -type d
```
Linux终端里输出了如下的内容：
```
./project1/src
./project2/src
```
其中，find命令的`-type d`参数的含义是查找目录。

我们再来测试一个例子。首先，创建好如下的文件：
```
./project1/src/test/test1.py
./project1/src/test/test2.py
./project1/src/test/test3.py
./project2/src/test/test1.py
./project2/src/test/test2.py
./project2/src/test/test3.py
```
然后我们在Linux终端里运行下述命令：
``` bash
find . -path '*/test/*.py' -type f
```
Linux终端里输出了如下的内容：
```
./project1/src/test/test1.py
./project1/src/test/test2.py
./project1/src/test/test3.py
./project2/src/test/test1.py
./project2/src/test/test2.py
./project2/src/test/test3.py
```

我们还可以查找最近一天编辑的文件。在Linux终端里运行下述命令：
``` bash
find . -mtime -1
```
（注意，最后的参数是-1，数字1）
Linux终端里输出了如下的内容：
```
.
./project1
./project1/src
./project1/src/test
./project1/src/test/test1.py
./project1/src/test/test2.py
./project1/src/test/test3.py
./project2
./project2/src
./project2/src/test
./project2/src/test/test1.py
./project2/src/test/test2.py
./project2/src/test/test3.py
```

find最强大的地方在于，它不仅可以查找文件，还可以对文件执行操作。首先，在当前目录`mydir`下，在Linux终端里运行`find`命令，得到了如下的输出：
```
.
./project1
./project1/src
./project1/src/test
./project1/src/test/test1.py
./project1/src/test/test2.py
./project1/src/test/test3.py
./project2
./project2/src
./project2/src/test
./project2/src/test/test1.py
./project2/src/test/test2.py
./project2/src/test/test3.py
```
然后，我们来删除所有的`.py`文件。我们来执行下述命令：
``` bash
find . -name "*.py" -exec rm {} \;
```
这条命令的功能是：查找所有的`.py`文件并删除它们。运行这条命令之后，Linux终端里什么都没有输出，好像什么都没有发生。但是，如果我们运行`echo $?`，会输出`0`，这就是说，前一个命令`find . -name "*.py" -exec rm {} \;`的返回值是0，因此，它正确地执行了。我们再来看看当前目录下的情况。在当前目录`mydir`之下，在Linux终端里运行`find`命令，得到了如下的输出：
```
.
./project1
./project1/src
./project1/src/test
./project2
./project2/src
./project2/src/test
```
可以看到，所有的`.py`文件都被删除了。或者，我们再来查找一下还有没有`.py`文件。在当前目录`mydir`之下，在Linux终端里运行下述命令：
``` bash
find . -name "*.py"
```
结果什么都没有输出。因此，当前目录之下已经没有`.py`文件了。

## grep命令相关的查找
我们还可以使用grep命令来查找相关的字符串。比如，我们首先在一些文件中写入foobar字符串。在Linux终端里执行下述命令：
``` bash
./test.sh ./mydir/project1/src/test/test1.py ./mydir/project1/src/test/test2.py ./mydir/project1/src/test/test3.py ./mydir/project2/src/test/test1.py ./mydir/project2/src/test/test2.py ./mydir/project2/src/test/test3.py
```
我们先在这些文件中写入`foobar`字符串。然后，我们在`mydir`目录下，在Linux终端里运行下述两个命令：
``` bash
grep -r foobar .
grep -r foobar
```
这两个命令的结果完全一样，都是在终端里输出了下面的内容：
```
project1/src/test/test1.py:# foobar
project1/src/test/test2.py:# foobar
project1/src/test/test3.py:# foobar
project2/src/test/test1.py:# foobar
project2/src/test/test2.py:# foobar
project2/src/test/test3.py:# foobar
```
或者，我们可以进入`mydir`目录的父目录，然后在Linux终端里运行下述命令：
``` bash
grep -r foobar ./mydir/
```
得到了如下的输出：
```
./mydir/project1/src/test/test1.py:# foobar
./mydir/project1/src/test/test2.py:# foobar
./mydir/project1/src/test/test3.py:# foobar
./mydir/project2/src/test/test1.py:# foobar
./mydir/project2/src/test/test2.py:# foobar
./mydir/project2/src/test/test3.py:# foobar
```
更多关于`grep`命令的用法，可以查看[Linux grep 命令教程](https://www.runoob.com/linux/linux-comm-grep.html)。

## 查看命令的历史
我们当然可以使用键盘上的向上箭头按钮来查看Linux命令的历史。不过，我们也可以在Linux终端里运行下述命令：
``` bash
history
```
Linux终端里输出了下述内容：
```
  126  clear
  127  conda deactivate
  128  clear
  129  tmux ls
  130  tmux new -s myleaves
  131  clear
  132  tmux ls
  133  clear
  134  echo $PATH
  135  source /home/users/XXX/miniconda3/bin/activate
  136  conda activate leaves
  137  conda deactivate
  138  cd classification/
  139  ls
  140  cd checkpoints/
  141  ls
  142  cd ../../../../../../../../../../../
  143  cd /home/
  144  ls
  145  pwd
  146  ls
  147  cd ~
  148  ls
  149  cd miniconda3/
  150  ls
  151  pwd
  152  cd ~
  153  ls ..
  154  cd ..
  155  ls
  156  cd ~
  157  cd .. & ls
  158  cd .. && ls
  159  clear
  160  cd ~
  161  clear
  162  cd .. && ls
  163  cd ~
  164  ls ..
  165  cd -
  166  clear
  167  cd -
  168  clear
  169  cd ~
  170  clear
  171  ls --help
  172  clear
  173  ls --help
  174  ls -l
  175  clear
  176  cd miniconda3/
  177  clear
  178  ls -l
  179  cd /
  180  ls -l
  181  clear
  182  cd /
  183  ls -l
  184  clear
  185  cd ~
  186  ls
  187  clear
  188  cd /
  189  ls
  190  cd /bin/
  191  ls
  192  cd ..
  193  cd usr/
  194  ls
  195  cd bin/
  196  ls
  197  cd ~
  198  clear
  199  ls -l /usr/bin/
  200  clear
  201  ls -l /usr/bin/
  202  cd ~
  203  cd /usr/bin/echo
  204  cd /
  205  ls -l
  206  clear
  207  cd /
  208  ls -l
  209  cd usr/
  210  clear
  211  ls -l
  212  cd ~
  213  ls
  214  cd aaaaa/
  215  ls
  216  clear
  217  touch apple.md
  218  ls
  219  code apple.md 
  220  clear
  221  ls
  222  mv apple.md banana.md
  223  ls
  224  clear
  225  cp banana.md ../apple.md
  226  ls
  227  cd ..
  228  ls
  229  rm apple.md 
  230  ls
  231  cd aaaaa/
  232  ls
  233  code banana.md 
  234  ls
  235  cleawr
  236  clear
  237  ls
  238  code banana.md 
  239  cp banana.md ../apple.md
  240  ls ..
  241  code ../apple.md 
  242  ls
  243  ls ..
  244  rm ../apple.md 
  245  ls ..
  246  ls
  247  rm banana.md 
  248  ls
  249  cd ..
  250  cd aaaaa/
  251  ls
  252  touch apple.md
  253  ls
  254  cd ..
  255  rmdir aaaaa/
  256  cd aaaaa/
  257  ls
  258  rm apple.md 
  259  cd ..
  260  rmdir aaaaa/
  261  ls
  262  man
  263  clear
  264  man --help
  265  clear
  266  man ls
  267  clear
  268  man ls
  269  sdf
  270  cd leaves_classification/
  271  clear
  272  程度。。
  273  ls
  274  cd ..
  275  ls
  276  clear
  277  ls
  278  clear
  279  ls
  280  mkdir aaaaa
  281  ls
  282  cd aaaaa/
  283  ls
  284  clear
  285  ls
  286  cldear
  287  clear
  288  ls
  289  clear
  290  echo hello > hello.txt
  291  ls
  292  cat hello.txt 
  293  code hello.txt 
  294  ls
  295  clear
  296  cat < hello.txt > hello2.txt
  297  ls
  298  cat hello2.txt 
  299  cat < hello.txt > hello2.txt
  300  cat hello2.txt 
  301  clear
  302  cat < hello.txt >> hello2.txt
  303  cat hello2.txt 
  304  cd ~
  305  tmux ls
  306  tmux attach -t myleaves
  307  ls
  308  clear
  309  conda activate leaves
  310  python --version
  311  python my_test.py 
  312  exit
  313  cd leaves_classification/
  314  ls
  315  tmux ls
  316  tmux attach -t myleaves
  317  ls
  318  cd leaves_classification/
  319  ls
  320  cd checkpoints/
  321  ls
  322  cd ..
  323  ls
  324  code my_test.py 
  325  cd checkpoints/
  326  ls
  327  ls -al
  328  clear
  329  ls -al
  330  ls
  331  clear
  332  ls
  333  cd ..
  334  ls
  335  clear
  336  ls
  337  code test.py 
  338  conda activate leaves
  339  python --version
  340  python test.py 
  341  ls
  342  code temp.txt 
  343  python test.py 
  344  clear
  345  python test.py 
  346  code text.txt
  347  ls
  348  code temp.txt
  349  clear
  350  python test.py 
  351  clear
  352  python test.py 
  353  clear
  354  python test.py 
  355  clear
  356  python test.py 
  357  clear
  358  python test.py 
  359  clear
  360  python test.py 
  361  ls
  362  code my_text.txt 
  363  clear
  364  code my_text.txt 
  365  clear
  366  python test.py 
  367  code my_text.txt 
  368  clear
  369  python test.py 
  370  clear
  371  python test.py 
  372  clear
  373  python test.py 
  374  clear
  375  python --version
  376  clear
  377  ls
  378  rm my_text.txt 
  379  ls
  380  rm temp.txt
  381  ls
  382  rm test1.py 
  383  ls
  384  python my_test.py 
  385  code my_test.py 
  386  ls
  387  code test.py 
  388  clear
  389  python test.py 
  390  ls
  391  touch temp.txt
  392  code temp.txt
  393  clear
  394  python test.py 
  395  clear
  396  python my_test.py 
  397  ls
  398  rm temp.txt 
  399  ls
  400  clear
  401  python --version
  402  python my_test.py 
  403  nvidia-smi 
  404  clear
  405  python my_test.py 
  406  ls
  407  touch temp.txt
  408  code temp.txt 
  409  touch my_script.py
  410  code my_script.py 
  411  clear
  412  python my_script.py 
  413  clear
  414  python my_script.py 
  415  clear
  416  python my_script.py 
  417  clear
  418  python my_script.py 
  419  clear
  420  python my_script.py 
  421  clear
  422  python my_script.py 
  423  clear
  424  python my_script.py 
  425  clear
  426  python my_script.py 
  427  clear
  428  python my_script.py 
  429  clear
  430  python my_script.py 
  431  clear
  432  python my_script.py 
  433  clear
  434  python my_script.py 
  435  clear
  436  python my_script.py 
  437  clear
  438  python my_script.py 
  439  clear
  440  python my_script.py 
  441  clear
  442  python my_script.py 
  443  code my_test.py 
  444  clear
  445  python --version
  446  clear
  447  python my_test.py 
  448  code my_script.py 
  449  code my_test.py 
  450  clear
  451  python my_script.py 
  452  clear
  453  python my_test.py 
  454  clear
  455  ls
  456  clear
  457  ls
  458  code my_script.py 
  459  clear
  460  python my_script.py 
  461  code my_test.py 
  462  clear
  463  python my_test.py 
  464  ls
  465  source /home/users/XXX/miniconda3/bin/activate
  466  conda activate leaves
  467  conda deactivate
  468  clear
  469  ls
  470  nvidia-smi 
  471  clear
  472  nvidia-smi 
  473  clear
  474  nvidia-smi 
  475  cd leaves_classification/
  476  ls
  477  rm submission.csv 
  478  ls
  479  code my_test.py 
  480  conda activate leaves
  481  clear
  482  python --version
  483  clear
  484  python my_test.py 
  485  ls
  486  clear
  487  python my_test.py 
  488  ls -l /
  489  clear
  490  ls -l /
  491  clear
  492  ls -l /
  493  ls -l / | tail -n1
  494  ls -l / | tail -n1 > ls.txt
  495  cat ls.txt 
  496  curl --head --silent google.com
  497  curl --head --silent github.com
  498  cd /sys/
  499  ls
  500  sudo su
  501  clear
  502  sudo su
  503  clear
  504  ls
  505  clear
  506  cd ~
  507  ls
  508  xdg-open ls.txt 
  509  cd /sys/
  510  ls
  511  cd class/
  512  ls
  513  cd backlight/
  514  ls
  515  cd ..
  516  echo $SHELL
  517  man touch
  518  cd ~
  519  ls
  520  clear
  521  echo '#!/bin/sh' > ls.txt 
  522  ls
  523  code ls.txt 
  524  echo curl --head --silent https://missing.csail.mit.edu > ls.txt 
  525  echo '#!/bin/sh' > ls.txt 
  526  echo curl --head --silent https://missing.csail.mit.edu >> ls.txt 
  527  clear
  528  echo '#!/bin/sh' > ls.txt 
  529  echo curl --head --silent https://missing.csail.mit.edu >> ls.txt 
  530  clear
  531  echo < curl --head --silent https://missing.csail.mit.edu > ls.txt 
  532  /sys/class/power_supply/
  533  cd /sys/class/power_supply/
  534  ls
  535  cd ~
  536  ls
  537  rm ls.txt 
  538  rm -rf aaaaa/
  539  ls
  540  touch test.sh
  541  code test.sh 
  542  echo $SHELL
  543  clear
  544  foo=bar
  545  echo $foo
  546  ls
  547  code .bashrc 
  548  ls
  549  ./test.sh
  550  ls
  551  sh test.sh 
  552  code test.sh 
  553  ls -l
  554  chmod +x ./test.sh
  555  ls -l
  556  test.sh
  557  ./test.sh 
  558  clear
  559  ./test.sh 
  560  ls
  561  rm test.sh 
  562  ls
  563  clear
  564  touch test.sh
  565  ls -l
  566  chmod +x ./test.sh
  567  ls -l
  568  ls
  569  code test.sh 
  570  ./test.sh 
  571  clear
  572  ./test.sh 
  573  code test.sh 
  574  clear
  575  ./test.sh 
  576  clear
  577  ./test.sh 
  578  clear
  579  ./test.sh 
  580  clear
  581  ./test.sh 
  582  clear
  583  ./test.sh 
  584  clear
  585  ./test.sh 
  586  clear
  587  ./test.sh 
  588  source test.sh 
  589  mcd aaaaa
  590  ls
  591  cd ..
  592  ls
  593  mcd bbbbb
  594  cd ..
  595  rm test.sh 
  596  ls
  597  mcd ccccc
  598  ls
  599  cd ..
  600  ls
  601  mcd ddddd
  602  clear
  603  ls
  604  rm -rf aaaaa
  605  rm -rf bbbbb
  606  rm -rf ccccc
  607  ls
  608  clear
  609  ls
  610  touch test.sh
  611  code test.sh 
  612  mcd aaaaa
  613  clear
  614  mcd
  615  clear
  616  mcd aaaaa
  617  clear
  618  source test.sh 
  619  mcd aaaaa
  620  cd ..
  621  ls
  622  rm aaaaa/
  623  rm -rf aaaaa/
  624  ls
  625  clear
  626  ls
  627  $?
  628  clear
  629  $_
  630  ls
  631  clear
  632  mkdir aaaaa
  633  cd $_
  634  ls
  635  clear
  636  ls
  637  cd ..
  638  ls
  639  rm -rf aaaaa/
  640  ls
  641  clear
  642  ls
  643  clear
  644  echo "Hello"
  645  echo $?
  646  ls
  647  code test.sh 
  648  clear
  649  grep foobar test.sh 
  650  echo $?
  651  clear
  652  true
  653  echo $?
  654  false
  655  echo $?
  656  clear
  657  false || echo "Hello world"
  658  true || echo "Hello world"
  659  clear
  660  true && echo "Hello World"
  661  false && echo "Hello World"
  662  false ; echo "This will always run"
  663  true ; echo "This will always run"
  664  clear
  665  ls
  666  ./test.sh
  667  ls
  668  chmod +x ./test.sh
  669  ls
  670  clear
  671  ls
  672  clear
  673  code test.sh 
  674  clear
  675  ./test.sh 
  676  ls
  677  clear
  678  code test.sh 
  679  clear
  680  ./test.sh 
  681  clear
  682  echo "We are in $(pwd)"
  683  clear
  684  cat <(ls) <(ls ..)
  685  clear
  686  ls
  687  mkdir aaaaa
  688  ls
  689  cd aaaaa/
  690  ls
  691  mkdir bbbbb
  692  mkdir ccccc
  693  ls
  694  cat <(ls) <(ls ..)
  695  cd bbbbb/
  696  ls
  697  cat <(ls) <(ls ..)
  698  ls
  699  clear
  700  ls
  701  clear
  702  ls
  703  mkdir ccccc
  704  ls
  705  cd ..
  706  ls
  707  rm -rf ccccc/
  708  ls
  709  cd ..
  710  ls
  711  cd aaaaa/
  712  ls
  713  cd bbbbb/
  714  ls
  715  cd ccccc/
  716  ls
  717  cd ..
  718  ls
  719  clear
  720  pwd
  721  clear
  722  cat <(ls) <(ls ..)
  723  ls ..
  724  pwd
  725  ls
  726  ls ..
  727  ls
  728  code test.sh 
  729  ./test.sh 
  730  cd /dev/null
  731  cd /dev/
  732  ls
  733  code null 
  734  cd -
  735  clear
  736  [[ 6 -ne 20 ]] && echo "6 is not equal to 20"
  737  clear
  738  [[ 6 -ne 20 ]] && echo "6 is not equal to 20"
  739  ls
  740  touch a.txt
  741  touch b.txt
  742  touch c.txt
  743  code a.txt 
  744  rm -rf aaaaa/
  745  ls
  746  ./test.sh a.txt b.txt c.txt 
  747  code b.txt 
  748  echo "Starting program at $(date)"
  749  echo 'Starting program at $(date)'
  750  echo "Starting program at $(date)"
  751  clear
  752  echo "Starting program at $(date)"
  753  echo 'Starting program at $(date)'
  754  echo "Running program $0 with $# arguments with pid $$"
  755  clear
  756  echo "Running program $0 with $# arguments with pid $$"
  757  ls
  758  touch mytest.sh
  759  chmod +x ./mytest.sh 
  760  ls
  761  code mytest.sh 
  762  ls
  763  clear
  764  ./mytest.sh 
  765  ls
  766  clear
  767  ls
  768  ./mytest.sh 
  769  ./mytest.sh a.txt b.txt c.txt 
  770  code mytest.sh 
  771  clear
  772  ./mytest.sh 
  773  clear
  774  ./mytest.sh a.txt b.txt c.txt 
  775  code mytest.sh 
  776  clear
  777  ./mytest.sh a.txt b.txt c.txt 
  778  clear
  779  ./mytest.sh a.txt b.txt c.txt 
  780  clear
  781  ./mytest.sh a.txt b.txt c.txt 
  782  ls
  783  grep foobar a.txt 
  784  code a.txt 
  785  clear
  786  grep foobar a.txt 
  787  clear
  788  grep foobar a.txt 
  789  echo $?
  790  grep foobar b.txt 
  791  echo $?
  792  code b.txt 
  793  code c.txt 
  794  clear
  795  ls
  796  clear
  797  grep foobar a.txt 
  798  echo $?
  799  clear
  800  grep foobar b.txt
  801  echo $?
  802  clear
  803  grep foobar c.txt
  804  echo $?
  805  grep foobar a.txt > b.txt 2> c.txt
  806  code b.txt 
  807  code c.txt 
  808  code a.txt 
  809  grep foobar a.txt > b.txt 2> c.txt
  810  code b.txt 
  811  code c.txt 
  812  clear
  813  grep foobar a.txt > b.txt 2> c.txt
  814  clear
  815  grep foobar a.txt > b.txt 2> c.txt
  816  clear
  817  grep foobar a.txt > b.txt 2> c.txt
  818  clear
  819  code test.sh 
  820  clear
  821  ./test.sh 
  822  ./test.sh a.txt b.txt c.txt 
  823  clear
  824  man test
  825  man test.sh 
  826  clear
  827  man test
  828  ls
  829  clear
  830  ls *.txt
  831  ls
  832  touch de.txt
  833  clear
  834  ls *.txt
  835  ls ?.txt
  836  clear
  837  ls
  838  mkdir prohject1
  839  rm -rf prohject1/
  840  mkdir project1
  841  mkdir project2
  842  mkdir project32
  843  ls
  844  ls project?
  845  ls project*
  846  clear
  847  ls project*
  848  ls project?
  849  ls
  850  code cat11.png 
  851  rm -rf project1
  852  rm -rf project2
  853  rm -rf project32/
  854  ls
  855  rm de.txt 
  856  ls
  857  convert cat11.png cat11.jpg
  858  ls
  859  rm cat11.jpg 
  860  ls
  861  convert cat11.{png,jpg}
  862  ls
  863  code cat11.jpg 
  864  ls
  865  clear
  866  rm a.txt 
  867  rm b.txt 
  868  rm c.txt 
  869  ls
  870  clear
  871  touch {a,b,c,de}.txt
  872  ls
  873  rm a.txt 
  874  rm b.txt 
  875  rm c.txt 
  876  rm de.txt 
  877  clear
  878  ls
  879  touch {a,b,c,de}.txt
  880  ls
  881  clear
  882  ls
  883  mkdir project{1,2,32}
  884  ls
  885  rm -rf project1
  886  rm -rf project2
  887  rm -rf project32/
  888  ls
  889  rm a.txt 
  890  rm b.txt 
  891  rm c.txt 
  892  rm de.txt 
  893  ls
  894  touch project{1,2}/{a,b,c}.txt
  895  clear
  896  ls
  897  mkdir project{1,2}
  898  clear
  899  ls
  900  clear
  901  ls
  902  touch project{1,2}/{a,b,c}.txt
  903  ls
  904  cd project
  905  clear
  906  ls project1
  907  ls project2
  908  ls
  909  rm -rf project1
  910  rm -rf project2/
  911  ls
  912  clear
  913  ls
  914  mkdir project1 project2
  915  ls
  916  touch project1/a.txt project2/b.txt
  917  ls
  918  clear
  919  ls project1
  920  ls project2
  921  diff <(ls project1) <(ls project2)
  922  clear
  923  diff <(ls project1) <(ls project2)
  924  clear
  925  ls
  926  conda activate leaves
  927  python --version
  928  clear
  929  ls
  930  touch see.py
  931  code see.py 
  932  clear
  933  python see.py 
  934  python see.py a b c
  935  python see.py aaa bb c
  936  clear
  937  python see.py aaa bb c
  938  clear
  939  conda deactivate
  940  ls
  941  clear
  942  shellcheck test.sh 
  943  shellcheck --version
  944  man mv
  945  man rg
  946  rg --version
  947  tldr
  948  find --version
  949  clear
  950  ls
  951  mkdir project{1,2}/src
  952  ls
  953  ls project1
  954  ls project2
  955  clear
  956  find . -name src -type d
  957  clear
  958  ls
  959  rm -rf project1
  960  rm -rf project2/
  961  ls
  962  rm see.py 
  963  ls
  964  mkdir mydir
  965  ls
  966  cd mydir/
  967  ls
  968  mkdir project{1,2}/src
  969  mkdir project{1,2}
  970  mkdir project{1,2}/src
  971  ls
  972  ls project1
  973  ls project2
  974  clear
  975  ls
  976  pwd
  977  clear
  978  find . -name src -type d
  979  ls
  980  mkdir project{1,2}/src/test
  981  touch project{1,2}/src/test/test{1,2,3}.py
  982  ls
  983  ls project1
  984  ls project1/src/
  985  ls
  986  ls project1/src/test/
  987  ls project2/src/test/
  988  clear
  989  find . -path '**/test/*.py' -type f
  990  find . -path '*/test/*.py' -type f
  991  clear
  992  find . -path '*/test/*.py' -type f
  993  clewar
  994  clear
  995  find . -mtime l
  996  find . -mtime 1
  997  find . -mtime -l
  998  find . -mtime -1
  999  clear
 1000  find . -mtime -1
 1001  ls
 1002  find
 1003  ls
 1004  cd mydir/
 1005  ls
 1006  clear
 1007  find
 1008  clear
 1009  pwd
 1010  clear
 1011  pwd
 1012  clear
 1013  find
 1014  find .
 1015  find
 1016  clear
 1017  find
 1018  find .
 1019  clear
 1020  find . -name "*.py" -exec rm {} \;
 1021  echo $?
 1022  find .
 1023  clear
 1024  find
 1025  find .
 1026  clear
 1027  find . -name "*.py"
 1028  locate
 1029  locate --version
 1030  ls
 1031  find
 1032  touch ./project{1,2}/src/test/test{1,2,3}.py
 1033  find
 1034  clear
 1035  ls
 1036  clear
 1037  find
 1038  fd ".py"
 1039  fd --version
 1040  clear
 1041  locate src
 1042  clear
 1043  ls
 1044  cd ..
 1045  ls
 1046  mv cat11.jpg cat12.jpg
 1047  ls
 1048  convert cat12.{jpg,png}
 1049  ls
 1050  rm cat12.jpg 
 1051  ls
 1052  find . -name ".png"
 1053  find . -name "*.png"
 1054  ls
 1055  mv cat11.png mydir/
 1056  ls
 1057  mv cat12.png mydir/
 1058  ls
 1059  cd mydir/
 1060  find . -name "*.png"
 1061  clear
 1062  find . -name "*.png"
 1063  find . -name '*.png' -exec convert {} {}.jpg \;
 1064  find . -name "*.png"
 1065  find . -name "*.jpg"
 1066  ls
 1067  rm cat11.png.jpg
 1068  rm cat12.png.jpg
 1069  ls
 1070  locate src
 1071  clear
 1072  locate mydir
 1073  clear
 1074  ls
 1075  cd ..
 1076  ls
 1077  grep -R foobar .
 1078  clear
 1079  ls
 1080  cd mydir/
 1081  ls
 1082  grep -r foobar .
 1083  cd project1
 1084  ls
 1085  cd src/
 1086  ls
 1087  cd test/
 1088  ls
 1089  cd ~
 1090  ls
 1091  code mytest.sh 
 1092  code test.sh 
 1093  clear
 1094  find mydir/
 1095  ./test.sh ./mydir/project1/src/test/test1.py ./mydir/project1/src/test/test2.py ./mydir/project1/src/test/test3.py ./mydir/project2/src/test/test1.py ./mydir/project2/src/test/test2.py ./mydir/project2/src/test/test3.py
 1096  clear
 1097  cd mydir/
 1098  ls
 1099  clear
 1100  grep -r foobar .
 1101  grep -r foobar
 1102  clear
 1103  grep -r foobar .
 1104  clear
 1105  grep -r foobar .
 1106  clear
 1107  grep -r foobar
 1108  cd ..
 1109  grep -r foobar ./mydir/
 1110  clear
 1111  grep -r foobar ./mydir/
 1112  rg
 1113  clear
 1114  rg --version
 1115  rg --help
 1116  ripgrep
 1117  ack
 1118  ag
 1119  history
 1120  clear
 1121  history
 1122  clear
 1123  ls
 1124  clear
 1125  history
```
我们还可以指定查找的命令数量。比如，在Linux终端里运行下述命令：
``` bash
history 15
```
Linux终端里得到了如下的输出：
```
 1117  ack
 1118  ag
 1119  history
 1120  clear
 1121  history
 1122  clear
 1123  ls
 1124  clear
 1125  history
 1126  clear
 1127  history 1
 1128  history l
 1129  history 1
 1130  history 10
 1131  history 15
```
上述命令`history 15`查找了在Linux终端里输入的最近15条命令

我们还可以查找含有特定内容的命令。比如，在Linux终端里运行下述命令：
``` bash
history | grep convert
```
终端里得到了如下的输出：
```
  857  convert cat11.png cat11.jpg
  861  convert cat11.{png,jpg}
 1048  convert cat12.{jpg,png}
 1063  find . -name '*.png' -exec convert {} {}.jpg \;
 1133  history | grep convert
```
这些是所有我曾经运行过的含有`convert`的命令。**再次复习，pipe符号`|`的含义是：左边命令的输出作为右边命令的输入。**

## 使用`Ctrl+r`来查找之前的命令。
我们可以在Linux终端里运行`Ctrl+r`快捷键，然后输入`grep`，来查找最近的一个含有`grep`的命令。此时，按住`Ctrl`，反复按下`r`，就可以查找其他更久之前的含有`grep`的命令了。

## `-R`用于递归查找
我们在Linux终端里运行下述命令（当前目录是`mydir`）：
``` bash
ls -R
```
得到了如下的输出：
```
.:
cat11.png  cat12.png  project1  project2

./project1:
src

./project1/src:
test

./project1/src/test:
test1.py  test2.py  test3.py

./project2:
src

./project2/src:
test

./project2/src/test:
test1.py  test2.py  test3.py
```
可以看到，我们可以递归地查看当前目录下的所有内容。

在当前目录`mkdir`之下，在Linux终端里运行下述命令：
``` bash
tree
```
终端里输出了下面的内容：
```
.
├── cat11.png
├── cat12.png
├── project1
│   └── src
│       └── test
│           ├── test1.py
│           ├── test2.py
│           └── test3.py
└── project2
    └── src
        └── test
            ├── test1.py
            ├── test2.py
            └── test3.py

6 directories, 8 files
```
这个`tree`命令用于查看当前路径下的完整的文件路径树。

## 作业题
**1.`ls`命令的更多尝试**
在当前路径`mydir`下，在Linux终端里运行下述命令：
``` bash
ls -al
```
得到了如下的输出：
```
total 648
drwxrwxr-x  4 zitong.yin zitong.yin     92 May 15 08:56 .
drwx------ 14 zitong.yin zitong.yin   4096 May 15 08:55 ..
-rw-rw-r--  1 zitong.yin zitong.yin 351194 May 14 19:04 cat11.png
-rw-rw-r--  1 zitong.yin zitong.yin 306421 May 15 08:53 cat12.png
drwxrwxr-x  3 zitong.yin zitong.yin     25 May 14 20:16 project1
drwxrwxr-x  3 zitong.yin zitong.yin     25 May 14 20:16 project2
```
`ls -al`的功能是，以列表的形式输出所有的内容。如果我们在Linux终端里单独运行`ls -a`，会看到如下的输出：
```
.  ..  cat11.png  cat12.png  project1  project2
```
单独运行`ls -l`，会看到如下的输出：
```
total 644
-rw-rw-r-- 1 zitong.yin zitong.yin 351194 May 14 19:04 cat11.png
-rw-rw-r-- 1 zitong.yin zitong.yin 306421 May 15 08:53 cat12.png
drwxrwxr-x 3 zitong.yin zitong.yin     25 May 14 20:16 project1
drwxrwxr-x 3 zitong.yin zitong.yin     25 May 14 20:16 project2
```
如果我们想把文件大小变得更容易理解，可以加入`-h`。比如，在Linux终端里运行下述命令：
``` bash
ls -ahl
```
得到了如下输出：
```
total 648K
drwxrwxr-x  4 zitong.yin zitong.yin   92 May 15 08:56 .
drwx------ 14 zitong.yin zitong.yin 4.0K May 15 08:55 ..
-rw-rw-r--  1 zitong.yin zitong.yin 343K May 14 19:04 cat11.png
-rw-rw-r--  1 zitong.yin zitong.yin 300K May 15 08:53 cat12.png
drwxrwxr-x  3 zitong.yin zitong.yin   25 May 14 20:16 project1
drwxrwxr-x  3 zitong.yin zitong.yin   25 May 14 20:16 project2
```
此时，文件的大小是人能够读懂的形式了。

如果我们想要把所有文件以最近访问顺序排序，可以加入`-t`参数。例如，在当前路径`mydir`下，在Linux终端里运行下述命令：
``` bash
ls -atlh
```
得到了如下的输出：
```
total 648K
drwxrwxr-x  4 zitong.yin zitong.yin   92 May 15 08:56 .
drwx------ 14 zitong.yin zitong.yin 4.0K May 15 08:55 ..
-rw-rw-r--  1 zitong.yin zitong.yin 300K May 15 08:53 cat12.png
drwxrwxr-x  3 zitong.yin zitong.yin   25 May 14 20:16 project1
drwxrwxr-x  3 zitong.yin zitong.yin   25 May 14 20:16 project2
-rw-rw-r--  1 zitong.yin zitong.yin 343K May 14 19:04 cat11.png
```
这是把所有文件以最近访问顺序排序的结果。

**2.关于函数的练习**
首先，我们来看一下当前的home路径。在Linux终端里运行下述命令：
``` bash
echo $HOME
```
得到了如下的输出：
```
/home/users/XXX
```
我们在home路径下，创建一个`marco_history.log`文件（使用`touch marco_history.log`命令）。然后，我们创建一个新的脚本`marco.sh`，在`marco.sh`中输入如下的代码：
``` bash
#!/bin/bash

marco(){
    echo "$(pwd)" > $HOME/marco_history.log
    echo "save pwd $(pwd)"
}
```
然后我们在Linux终端里运行下述命令：
``` bash
source marco.sh
```
然后在Linux终端里运行下述命令：
``` bash
marco
```
Linux终端里输出了如下的内容：
```
save pwd /home/users/XXX
```
同时在`marco_history.log`文件里多了一行内容`/home/users/XXX`

我们创建一个新的脚本`polo.sh`，在这个`polo.sh`脚本中输入如下的代码：
``` bash
#!/bin/bash

polo(){
    cd "$(cat "$HOME/marco_history.log")"
}
```
然后在Linux终端里依次运行下述命令：
``` bash
source polo.sh
polo
```
此时，`polo`命令就可以实现进入`marco_history.log`文件里所包含的路径的功能了。


**3.假设您有一个命令，它很少出错。因此为了在出错时能够对其进行调试，需要花费大量的时间重现错误并捕获输出。 编写一段bash脚本，运行如下的脚本直到它出错，将它的标准输出和标准错误流记录到文件，并在最后输出所有内容。加分项：报告脚本在失败前共运行了多少次。**
**题目的补充说明：运行如下的脚本，指的是运行这个`buggy.sh`脚本：**
``` bash
#!/bin/bash

n=$(( RANDOM % 100 ))

if [[ n -eq 42 ]]; then
    echo "Something went wrong"
    >&2 echo "The error was using magic numbers"
    exit 1
fi

echo "Everything went according to plan"
```
下面我们来解答和分析这道题。
首先，我们对题目中的`buggy.sh`脚本稍加修改，增加一个输出`n`的代码。修改后的`buggy.sh`脚本如下：
``` bash
#!/bin/bash

n=$(( RANDOM % 100 ))

echo "n=$n"

if [[ n -eq 42 ]]; then
    echo "Something went wrong"
    >&2 echo "The error was using magic numbers"
    exit 1
fi

echo "Everything went according to plan"
```
接下来，我们熟悉一下`-eq`比较相等运算符的用法。在Linux终端里运行下述命令：
``` bash
[[ 5 -eq 05 ]] && echo "5 equals 05"
```
Linux终端里输出了下述内容：
```
5 equals 05
```
因此，`[[ n -eq 42 ]]`实现的是比较`n`和`42`是否相等。如果相等，则返回`true`，如果不相等，则返回`false`。

根据[What does >&2 mean in a shell script?](https://askubuntu.com/questions/1182450/what-does-2-mean-in-a-shell-script)的解释，文件描述符`1`是`stdout`，文件描述符`2`是`stderr`。使用`>`来重定向输出与使用`1>`是一样的。这表示要重定向`stdout`（文件描述符`1`）。通常情况下，我们重定向到一个文件。然而，我们可以使用`>&`来重定向到`stdout`（文件描述符`1`）或`stderr`（文件描述符`2`）。**因此，要将`stdout`（文件描述符`1`）重定向到`stderr`（文件描述符`2`），你可以使用`>&2`**。更多的参考资料可以参见[echo >&2 "some text" what does it mean in shell scripting](https://stackoverflow.com/questions/23489934/echo-2-some-text-what-does-it-mean-in-shell-scripting)（关于这个`>&2`的用法，我还是不太懂，之后再说了）。

我们可以新建一个`mytest.sh`，在这个`mytest.sh`脚本中，输入如下的代码：
``` bash
#!/bin/bash

count=1

while true
do
    ./buggy.sh 2> out.log
    if [[ $? -ne 0 ]]; then
        echo "failed after $count times"
        cat out.log
        break
    fi
    ((count++))

done
```
在Linux终端里运行如下的命令：
``` bash
./mytest.sh 
```
在Linux终端里输出了如下的结果：
```
n=70
Everything went according to plan
n=35
Everything went according to plan
n=40
Everything went according to plan
n=38
Everything went according to plan
n=51
Everything went according to plan
n=67
Everything went according to plan
n=48
Everything went according to plan
n=69
Everything went according to plan
n=69
Everything went according to plan
n=36
Everything went according to plan
n=15
Everything went according to plan
n=79
Everything went according to plan
n=21
Everything went according to plan
n=12
Everything went according to plan
n=89
Everything went according to plan
n=13
Everything went according to plan
n=91
Everything went according to plan
n=35
Everything went according to plan
n=13
Everything went according to plan
n=25
Everything went according to plan
n=5
Everything went according to plan
n=59
Everything went according to plan
n=26
Everything went according to plan
n=40
Everything went according to plan
n=60
Everything went according to plan
n=35
Everything went according to plan
n=30
Everything went according to plan
n=95
Everything went according to plan
n=13
Everything went according to plan
n=35
Everything went according to plan
n=27
Everything went according to plan
n=75
Everything went according to plan
n=67
Everything went according to plan
n=2
Everything went according to plan
n=6
Everything went according to plan
n=39
Everything went according to plan
n=5
Everything went according to plan
n=62
Everything went according to plan
n=72
Everything went according to plan
n=93
Everything went according to plan
n=43
Everything went according to plan
n=3
Everything went according to plan
n=86
Everything went according to plan
n=48
Everything went according to plan
n=7
Everything went according to plan
n=91
Everything went according to plan
n=56
Everything went according to plan
n=57
Everything went according to plan
n=7
Everything went according to plan
n=66
Everything went according to plan
n=5
Everything went according to plan
n=67
Everything went according to plan
n=43
Everything went according to plan
n=86
Everything went according to plan
n=81
Everything went according to plan
n=71
Everything went according to plan
n=13
Everything went according to plan
n=65
Everything went according to plan
n=80
Everything went according to plan
n=43
Everything went according to plan
n=63
Everything went according to plan
n=97
Everything went according to plan
n=87
Everything went according to plan
n=82
Everything went according to plan
n=43
Everything went according to plan
n=76
Everything went according to plan
n=84
Everything went according to plan
n=89
Everything went according to plan
n=64
Everything went according to plan
n=44
Everything went according to plan
n=2
Everything went according to plan
n=21
Everything went according to plan
n=45
Everything went according to plan
n=81
Everything went according to plan
n=37
Everything went according to plan
n=32
Everything went according to plan
n=15
Everything went according to plan
n=1
Everything went according to plan
n=90
Everything went according to plan
n=95
Everything went according to plan
n=71
Everything went according to plan
n=41
Everything went according to plan
n=11
Everything went according to plan
n=3
Everything went according to plan
n=71
Everything went according to plan
n=95
Everything went according to plan
n=27
Everything went according to plan
n=39
Everything went according to plan
n=60
Everything went according to plan
n=71
Everything went according to plan
n=66
Everything went according to plan
n=65
Everything went according to plan
n=83
Everything went according to plan
n=44
Everything went according to plan
n=62
Everything went according to plan
n=30
Everything went according to plan
n=27
Everything went according to plan
n=85
Everything went according to plan
n=61
Everything went according to plan
n=31
Everything went according to plan
n=21
Everything went according to plan
n=10
Everything went according to plan
n=47
Everything went according to plan
n=69
Everything went according to plan
n=50
Everything went according to plan
n=32
Everything went according to plan
n=31
Everything went according to plan
n=47
Everything went according to plan
n=94
Everything went according to plan
n=78
Everything went according to plan
n=26
Everything went according to plan
n=98
Everything went according to plan
n=94
Everything went according to plan
n=14
Everything went according to plan
n=97
Everything went according to plan
n=66
Everything went according to plan
n=94
Everything went according to plan
n=11
Everything went according to plan
n=78
Everything went according to plan
n=78
Everything went according to plan
n=89
Everything went according to plan
n=41
Everything went according to plan
n=20
Everything went according to plan
n=35
Everything went according to plan
n=78
Everything went according to plan
n=83
Everything went according to plan
n=76
Everything went according to plan
n=33
Everything went according to plan
n=31
Everything went according to plan
n=30
Everything went according to plan
n=17
Everything went according to plan
n=3
Everything went according to plan
n=53
Everything went according to plan
n=94
Everything went according to plan
n=89
Everything went according to plan
n=14
Everything went according to plan
n=21
Everything went according to plan
n=14
Everything went according to plan
n=23
Everything went according to plan
n=20
Everything went according to plan
n=74
Everything went according to plan
n=91
Everything went according to plan
n=24
Everything went according to plan
n=10
Everything went according to plan
n=59
Everything went according to plan
n=76
Everything went according to plan
n=38
Everything went according to plan
n=91
Everything went according to plan
n=70
Everything went according to plan
n=34
Everything went according to plan
n=66
Everything went according to plan
n=55
Everything went according to plan
n=38
Everything went according to plan
n=8
Everything went according to plan
n=10
Everything went according to plan
n=60
Everything went according to plan
n=49
Everything went according to plan
n=60
Everything went according to plan
n=79
Everything went according to plan
n=31
Everything went according to plan
n=10
Everything went according to plan
n=13
Everything went according to plan
n=95
Everything went according to plan
n=6
Everything went according to plan
n=34
Everything went according to plan
n=36
Everything went according to plan
n=20
Everything went according to plan
n=50
Everything went according to plan
n=34
Everything went according to plan
n=0
Everything went according to plan
n=51
Everything went according to plan
n=2
Everything went according to plan
n=82
Everything went according to plan
n=43
Everything went according to plan
n=29
Everything went according to plan
n=63
Everything went according to plan
n=7
Everything went according to plan
n=41
Everything went according to plan
n=66
Everything went according to plan
n=7
Everything went according to plan
n=15
Everything went according to plan
n=13
Everything went according to plan
n=31
Everything went according to plan
n=61
Everything went according to plan
n=38
Everything went according to plan
n=39
Everything went according to plan
n=82
Everything went according to plan
n=63
Everything went according to plan
n=92
Everything went according to plan
n=22
Everything went according to plan
n=85
Everything went according to plan
n=76
Everything went according to plan
n=86
Everything went according to plan
n=3
Everything went according to plan
n=51
Everything went according to plan
n=22
Everything went according to plan
n=46
Everything went according to plan
n=77
Everything went according to plan
n=95
Everything went according to plan
n=49
Everything went according to plan
n=48
Everything went according to plan
n=83
Everything went according to plan
n=80
Everything went according to plan
n=69
Everything went according to plan
n=0
Everything went according to plan
n=21
Everything went according to plan
n=56
Everything went according to plan
n=55
Everything went according to plan
n=42
Something went wrong
failed after 209 times
The error was using magic numbers
```
这个`mytest.sh`脚本中的代码就是一个可行的解决方案。

**4.编写一个命令，它可以递归地查找文件夹中所有的HTML文件，并将它们压缩成zip文件。**
下面我们来解答和分析这道题。

首先，参考[Linux xargs 命令教程](https://www.runoob.com/linux/linux-comm-xargs.html)，我们先来熟悉一下Linux的`xargs`命令。

我们在当前目录`mydir`之下，新建`test.txt`文件。在`test.txt`文件中，输入如下的内容：
```
a b c d e f g
h i j k l m n
o p q
r s t
u v w x y z
# （注意：必须有最后的这个空行）
```
然后我们在当前目录`mydir`之下，在Linux终端里运行下述命令：
``` bash
cat test.txt
```
得到了如下的输出：
```
a b c d e f g
h i j k l m n
o p q
r s t
u v w x y z
```
我们再来在Linux终端里运行下述命令：
``` bash
cat test.txt | xargs
```
在Linux终端里输出了如下的结果：
```
a b c d e f g h i j k l m n o p q r s t u v w x y z
```
这是因为`xargs`可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行。我们再来在Linux终端里运行下述命令：
``` bash
cat test.txt | xargs -n3
```
得到了如下的输出：
```
a b c
d e f
g h i
j k l
m n o
p q r
s t u
v w x
y z
```

`xargs`命令的`-d`参数的含义是修改`xargs`的分隔符。默认的`xargs`分隔符是回车，`argument`的分隔符是空格，这里`-d`修改的是`xargs`的分隔符。

然后我们在当前目录`mydir`之下，在Linux终端里依次运行下述命令，创建我们要操作的`.html`文件：
``` bash
mkdir html_root
cd html_root
touch {1..10}.html
mkdir html
cd html
touch xxxx.html
```
在当前路径`tree`之下，在Linux终端里运行命令`tree`，得到了如下的输出：
```
.
└── html_root
    ├── 10.html
    ├── 1.html
    ├── 2.html
    ├── 3.html
    ├── 4.html
    ├── 5.html
    ├── 6.html
    ├── 7.html
    ├── 8.html
    ├── 9.html
    └── html
        └── xxxx.html

2 directories, 11 files
```
然后我们在当前路径`mydir`之下，在Linux终端里运行下述命令：
``` bash
find . -name "*.html" -type f
```
Linux终端里输出了如下的内容：
```
./html_root/1.html
./html_root/2.html
./html_root/3.html
./html_root/4.html
./html_root/5.html
./html_root/6.html
./html_root/7.html
./html_root/8.html
./html_root/9.html
./html_root/10.html
./html_root/html/xxxx.html
```
然后我们再来在当前路径`mydir`之下，在Linux终端里运行下述命令：
``` bash
find . -name "*.html" -type f | xargs -d '\n'
```
Linux终端里输出了下面的内容：
```
./html_root/1.html ./html_root/2.html ./html_root/3.html ./html_root/4.html ./html_root/5.html ./html_root/6.html ./html_root/7.html ./html_root/8.html ./html_root/9.html ./html_root/10.html ./html_root/html/xxxx.html
```
可以看到，我们已经用`xargs -d '\n'`把分隔符由换行修改为了空格。最后，我们运行下述命令：
``` bash
find . -name "*.html" -type f | xargs -d '\n' tar -cvzf html.zip
```
在Linux终端里输出了如下的内容：
```
./html_root/1.html
./html_root/2.html
./html_root/3.html
./html_root/4.html
./html_root/5.html
./html_root/6.html
./html_root/7.html
./html_root/8.html
./html_root/9.html
./html_root/10.html
./html_root/html/xxxx.html
```
此时此刻，我们运行`ls`命令，发现多了一个文件`html.zip`。

至此，麻省理工学院《计算机教育中缺失的一课》第2课Shell脚本就学习完毕了。