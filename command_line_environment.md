# 如何高效地使用你的电脑？｜麻省理工学院《计算机教育中缺失的一课》（4）——命令行环境
在这个笔记中，我将记录麻省理工学院著名的 **《计算机教育中缺失的一课》** 的第5课的学习笔记（我们暂且跳过第4课）。**这门课的主要目的在于，教你如何高效地使用计算机中你常用的各种工具**。众所周知，计算机已经深入了我们生活的方方面面。但是，你不一定真的会**高效地**使用它。这门课的目的就在于，教你如何高效地使用工具，帮助你高效地使用计算机。第4节课的内容是**命令行环境**。

这节课的内容分为以下的四个方面：
- 任务控制
- 终端多路复用
- 配置文件（Dotfiles）
- 远端设备（SSH）

接下来我们进入正式的课程内容。

## 任务控制

### 结束进程
我们在Linux终端里键入如下的命令：
``` bash
sleep 20
```
参考[Linux sleep命令教程](https://www.runoob.com/linux/linux-comm-sleep.html)，这个命令的含义是：休眠一定的时间。比如，上述`sleep 20`命令的含义就是休眠20秒。

如果我们不想等待这个进程的执行，我们可以使用`Ctrl + C`来终止当前进程的执行。在上面的例子中，当我们输入`Ctrl + C`时，shell会发送一个`SIGINT`（SIGnal INTerrupt）信号到当前进程。

我们再来测试一个Python脚本，看看如何在Python脚本中使用`SIGINT`信号。在当前路径`mydir`之下，创建空白脚本`mytest.py`，然后在`mytest.py`中键入如下的代码：
``` python
import signal, time


def handler(signum, time):
    print("\nI got a SIGINT, but I am not stopping")


signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(0.1)
    print("\r{}".format(i), end="")
    i += 1
```
然后在Linux终端里运行下述命令（启动一个带有`Python3.9`的conda虚拟环境）：
``` bash
python mytest.py
```
这时候，我们看到计数开始了。此时在Linux终端里键入`Ctrl + C`并不能终止`mytest.py`脚本的运行。在Linux终端里键入`Ctrl + C`后，Linux终端里输出了如下的内容：
```
I got a SIGINT, but I am not stopping
```
我们必须在Linux终端里键入`Ctrl + \`，才能使`mytest.py`终止运行。

### 暂停和后台执行进程
以下实验需要用到不挂起一个进程的命令，参考[Linux nohup 命令教程](https://www.runoob.com/linux/linux-comm-nohup.html)。

首先，在Linux终端里运行下述命令：
``` bash
jobs
```
Linux终端里什么都没有输出，说明此时此刻并没有任何job在运行。

接下来我们在Linux终端里运行下述命令：
``` bash
sleep 1000
```
此时，`sleep 1000`这个job开始运行。我们在Linux终端里键入`Ctrl + Z`暂停这个job。此时，我们再次在Linux终端里键入`jobs`命令，得到了如下的输出：
```
[1]+  Stopped                 sleep 1000
```
此时`sleep 1000`这个job处于暂停状态。

我们再来在Linux终端里键入下述命令：
``` bash
nohup sleep 2000 &
```
这行命令的含义是：在后台运行`sleep 2000`命令，并使该命令不被挂起。最后的`&`表示在后台运行这个命令。运行这个命令之后，再在Linux终端里键入`jobs`命令，得到了如下的输出：
```
[1]+  Stopped                 sleep 1000
[2]-  Running                 nohup sleep 2000 &
```
此时第一个job被暂停了。如果我们想继续运行第一个job，可以在Linux终端里键入下述`bg`命令：
``` bash
bg %1
```
此时Linux终端里输出了下述内容：
```
[1]+ sleep 1000 &
```
此时再在Linux终端里运行`jobs`命令，得到了如下的输出：
```
[1]-  Running                 sleep 1000 &
[2]+  Running                 nohup sleep 2000 &
```
此时，这两个命令都正在后台运行。如果我们想暂停第一个job，我们可以在Linux终端里键入下述命令：
``` bash
kill -STOP %1
```
Linux终端里输出了下述内容：
```
[1]+  Stopped                 sleep 1000
```
此时，再在Linux终端里键入`jobs`命令，得到了如下的输出：
```
[1]+  Stopped                 sleep 1000
[2]-  Running                 nohup sleep 2000 &
```
可以看到，第一个job被暂停了。如果我们再在Linux终端里运行下述命令：
``` bash
kill -HUP %1
```
运行`jobs`命令，可以看到如下的输出：
```
[1]-  Hangup                  sleep 1000
[2]+  Running                 nohup sleep 2000 &
```
可以看到第一个job被挂起了。如果我们在Linux终端里运行下述命令：
``` bash
kill -HUP %2
```
运行`jobs`命令，得到了如下的输出：
```
[2]+  Running                 nohup sleep 2000 &
```
此时，第一个job已经没有了，但是第二个job无法被挂起。这就是`nohup`的作用。

我们如果想kill第二个job，在Linux终端里运行下述命令：
``` bash
kill -KILL %2
```
则杀死了第二个job。我们再来运行`jobs`命令，得到了如下的输出：
```
[2]+  Killed                  nohup sleep 2000
```
过了一会儿之后，再来运行`jobs`命令，第二个job也消失不见了。

## 终端多路复用
当我们在使用命令行时，我们通常会希望同时执行多个任务。举例来说，我们可能会想要同时运行编辑器，并在终端的另外一侧执行程序。尽管再打开一个新的终端窗口也能达到目的，使用终端多路复用器则是一种更好的办法。

像`tmux`这类的终端多路复用器可以允许我们基于面板和标签分割出多个终端窗口，这样我们便可以同时与多个`shell`会话进行交互。

不仅如此，终端多路复用使我们可以分离当前终端会话并在将来重新连接。这让我们操作远端设备（SSH）时的工作流大大改善，避免了`nohup`和其他类似技巧的使用。

现在最流行的终端多路器是`tmux`。`tmux`是一个高度可定制的工具，我们可以使用相关快捷键创建多个标签页并在它们间导航。

tmux具有如下的层次结构：
- sessions
  - windows
    - panes

意即：一个session（会话）里可以有多个windows（窗口），一个window（窗口）里可以有多个panes（面板/窗格）。在使用tmux时，记住这个层次结构是有用的。

接下来进入`tmux`相关的实验。

### tmux会话
首先，我们在Linux终端里键入下述命令，查看一下当前有没有已经打开的tmux终端：
``` bash
tmux ls
```
得到了如下的输出：
```
failed to connect to server
```
由此可知，目前我们没有已经打开的tmux终端。接下来，我们使用下述命令：
``` bash
tmux new -s leaves
```
创建一个新的名为`leaves`的tmux会话。然后就可以在这个名为`leaves`的tmux会话里运行程序了。我们如果想回到原来的shell终端，只需键入`Ctrl + b + d`快捷键即可。

此时，我们再次在Linux终端里键入`tmux ls`命令，得到了如下的输出：
```
leaves: 1 windows (created Tue Jun  7 20:54:52 2022) [94x17]
```
此时只有一个名为`leaves`的tmux会话。这个名为`leaves`的tmux会话只有一个窗口。

如果我们想要重新连接刚才的名为`leaves`的会话，只需在Linux终端里键入下述命令：
``` bash
tmux attach -t leaves
```
就重新进入了`leaves`会话。

如果我们想要彻底退出一个tmux会话该怎么办呢？我们首先使用下述命令：
``` bash
tmux attach -t leaves
```
进入名为`leaves`的tmux会话，然后在`leaves`会话里，键入下述退出命令：
``` bash
exit
```
和回车键，就可以彻底退出名为`leaves`的会话了。此时，再次使用`tmux ls`命令，可以看到已经没有`leaves`会话了。

### tmux窗口
在当前的`leaves`会话里，使用`Ctrl + b + c`，可以创建一个新的tmux窗口。这个窗口是属于会话`leaves`的。此时，使用`Ctrl + b + d`回到Linux shell，然后键入`tmux ls`，得到了如下的输出：
```
leaves: 2 windows (created Tue Jun  7 21:11:13 2022) [94x17]
```
可以看到，`leaves`会话有两个窗口。

我们可以使用`Ctrl + b + c`创建多个窗口。每个窗口都有一个编号（从0开始）。使用`Ctrl + b + 3`可以跳转到编号为`3`的窗口（编号在最下方显示）。

使用`Ctrl + b + w`可以列出当前会话的所有窗口，然后使用上下箭头和回车就可以进入任意一个窗口了。

使用`Ctrl + b + p`进入前一个窗口，使用`Ctrl + b + n`进入下一个窗口。

使用`Ctrl + b + ,`可以重命名当前窗口，不过，重命名之后的窗口的编号依然不改变。

使用`exit`彻底关闭当前窗口。

### tmux面板
关于面板的操作，记住下述快捷键即可：
- `Ctrl + b + "` 水平分割
- `Ctrl + b + %` 垂直分割
- `Ctrl + b + 方向键` 切换到指定方向的面板，方向键指的是键盘上的方向键
- `Ctrl + b + z` 切换当前面板的缩放，使当前面板占满屏幕。再次键入`Ctrl + b + z`可以回到之前的缩放状态。
- `Ctrl + b + [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分
- `Ctrl + b + 空格`在不同的面板排布间切换
- `exit` 彻底关闭当前面板


## 配置文件（Dotfiles）
我们暂且省略关于别名的设置，因为一般来说，我不喜欢设置别名，我喜欢用原始的Linux命令。

这一部分暂时没有什么特别需要记住的，只需要之后用到的时候去查即可。或者，使用Github上别人开源的优秀Dotfiles即可。


## 使用SSH远程连接
这一部分比较基础，如果遇到不会的自行搜索即可。这部分没有要讲的。


---

至此，麻省理工学院《计算机教育中缺失的一课》第4课命令行环境就学习完毕了。