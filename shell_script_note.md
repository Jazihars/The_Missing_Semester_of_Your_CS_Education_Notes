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

从33:22左右开始看视频
