# 如何高效地使用你的电脑？｜麻省理工学院《计算机教育中缺失的一课》（5）——版本控制（Git）
在这个笔记中，我将记录麻省理工学院著名的 **《计算机教育中缺失的一课》** 的第5课的学习笔记。**这门课的主要目的在于，教你如何高效地使用计算机中你常用的各种工具**。众所周知，计算机已经深入了我们生活的方方面面。但是，你不一定真的会**高效地**使用它。这门课的目的就在于，教你如何高效地使用工具，帮助你高效地使用计算机。第5节课的内容是**版本控制（Git）**。

版本控制系统（VCSs）是一类用于追踪源代码（或其他文件、文件夹）改动的工具。顾名思义，这些工具可以帮助我们管理代码的修改历史；不仅如此，它还可以让协作编码变得更方便。版本控制系统通过一系列的快照将某个文件夹及其内容保存了起来，每个快照都包含了文件或文件夹的完整状态。同时它还维护了快照创建者的信息以及每个快照的相关信息等等。

为什么说版本控制系统非常有用？即使您只是一个人进行编程工作，它也可以帮您创建项目的快照，记录每个改动的目的、基于多分支并行开发等等。和别人协作开发时，它更是一个无价之宝，您可以看到别人对代码进行的修改，同时解决由于并行开发引起的冲突。

现代的版本控制系统可以帮助您轻松地（甚至自动地）回答以下问题：
- 当前模块是谁编写的？
- 这个文件的这一行是什么时候被编辑的？是谁作出的修改？修改原因是什么呢？
- 最近的1000个版本中，何时/为什么导致了单元测试失败？
- 
尽管版本控制系统有很多，其事实上的标准则是`Git`

## Git的数据模型
Git将顶级目录中的文件和文件夹作为集合，并通过一系列快照（snapshots）来管理其历史记录。在Git的术语里，文件被称作`Blob`对象（数据对象），也就是一组数据。目录则被称之为“树”，它将名字与`Blob`对象或树对象进行映射（使得目录中可以包含其他目录）。快照（snapshots）则是被追踪的最顶层的树。例如，一个树看起来可能是这样的：
```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```
这个顶层的树包含了两个元素，一个名为 “foo” 的树（它本身包含了一个blob对象 “bar.txt”），以及一个 blob 对象 “baz.txt”。

## 历史记录建模：关联快照
版本控制系统和快照有什么关系呢？线性历史记录是一种最简单的模型，它包含了一组按照时间顺序线性排列的快照。不过处于种种原因，Git并没有采用这样的模型。

在Git中，历史记录是一个由快照组成的**有向无环图**。有向无环图，听上去似乎是什么高大上的数学名词，不过不要怕，我们只需要知道这代表Git中的每个快照都有一系列的“父辈”，也就是其之前的一系列快照。注意，快照具有多个“父辈”而非一个，因为某个快照可能由多个父辈而来。例如，经过合并后的两条分支。

在 Git 中，这些快照被称为“提交”。通过可视化的方式来表示这些历史提交记录时，看起来差不多是这样的：
```
o <-- o <-- o <-- o
            ^  
             \
              --- o <-- o
```
上面是一个ASCII码构成的简图，其中的`o`表示一次提交（快照）。

箭头指向了当前提交的父辈（这是一种“在...之前”，而不是“在...之后”的关系）。在第三次提交之后，历史记录分岔成了两条独立的分支。这可能因为此时需要同时开发两个不同的特性，它们之间是相互独立的。开发完成后，这些分支可能会被合并并创建一个新的提交，这个新的提交会同时包含这些特性。新的提交会创建一个新的历史记录，看上去像这样（最新的合并提交用粗体标记）：

<pre class="highlight">
<code>
o <-- o <-- o <-- o <---- <strong>o</strong>
            ^            /
             \          v
              --- o <-- o
</code>
</pre>

Git 中的提交是不可改变的。但这并不代表错误不能被修改，只不过这种“修改”实际上是创建了一个全新的提交记录。而引用（参见下文）则被更新为指向这些新的提交。

注意：如果要合并的两个分支发生了冲突，Git会报错，需要修复分支中的冲突才能合并分支。

## Git的数据模型及其伪代码表示
下面的伪代码表示了Git的数据模型的结果：
``` C++
// 文件就是一组数据
type blob = array<byte>

// 一个包含文件和目录的目录
type tree = map<string, tree | blob>

// 每个提交都包含一个父辈，元数据和顶层树
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```
这就是Git各个对象的类型和包含的内容。

## git的实践——创建git仓库并添加文件
接下来我们来实践git的操作。

首先，在Linux终端里键入下述命令：
``` bash
git --version
```
得到了如下的输出：
```
git version 1.8.3.1
```
这就是我的Linux机器上安装的git的版本。

接下来，在home路径下，依次运行下述命令：
``` bash
mkdir learngit
cd learngit/
pwd
```
得到了如下的输出：
```
/home/users/XXX/learngit
```
接下来运行下述命令：
``` bash
git init
```
得到了如下的输出：
```
Initialized empty Git repository in /home/users/XXX/learngit/.git/
```
`git init`命令的含义是：把当前路径`/learngit`变成一个git仓库。当把`/learngit`变成一个仓库后，我们运行`ls -a`命令，得到了如下的输出：
```
.  ..  .git
```
这就表明，当前路径`/learngit`已经变成了一个git仓库了。

接下来，我们用`touch readme.txt`命令在当前路径`/learngit`下创建文本文件`readme.txt`。打开这个文本文件（我直接使用vscode打开了。当然完全可以使用vim来编辑），输入如下的内容：
```
Git is a version control system.
Git is free software.
```
接下来我们把这个文本文件`readme.txt`添加到git仓库。首先，在Linux终端里运行下述命令：
``` bash
git add readme.txt
```
这条命令运行后没有任何显示消息，这就对了。Unix的哲学是**没有消息就是好消息**，说明添加成功。
接下来运行下述命令：
``` bash
git commit -m "wrote a readme file"
```
输出了一些内容，表示已经成功提交。此时，我们再来运行下述命令：
``` bash
git status
```
得到了如下的输出：
```
# On branch master
nothing to commit, working directory clean
```
`git status`命令的含义是：显示当前的仓库状态。

如果我们要再往仓库里提交一个文件，我们可以做如下的实验。运行下述命令：
``` bash
echo "hello world" > hello.txt
git status
```
得到了如下的输出：
```
# On branch master
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       hello.txt
nothing added to commit but untracked files present (use "git add" to track)
```
此时`hello.txt`是一个未追踪的文件。我们再来运行下述命令：
``` bash
git add hello.txt
git status
```
得到了如下的输出：
```
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#       new file:   hello.txt
#
```
此时`hello.txt`变成了准备提交的状态。最后，我们运行下述命令：
``` bash
git commit -m "wrote hello.txt"
```
得到了如下的输出：
```
(省略一些内容)
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
```
此时再次运行下述命令：
``` bash
git status
```
得到了如下的输出：
```
# On branch master
nothing to commit, working directory clean
```
这说明，我们已经成功提交了`hello.txt`文件到当前的`/learngit`仓库。此时，如果我们运行下述命令查看日志：
``` bash
git log
```
得到了如下的输出：
```
commit 0f3d14f865fcb18f04fc8cc00517b7737bc51e4c
Author: XXX
Date:   Tue Jun 14 21:36:11 2022 +0800

    wrote hello.txt

commit 33ee81f330e98cdd2766e2672fb5ac3950068e64
Author: XXX
Date:   Tue Jun 14 21:23:20 2022 +0800

    wrote a readme file
```
这些日志显示了我刚才的提交记录。我们来运行下述命令查看我们的提交：
``` bash
git cat-file -p 0f3d14f865fcb18f04fc8cc00517b7737bc51e4c
```
得到了如下的输出：
```
tree 23f10711fb4ffe30a77d2febd4aa7bbe6c0cedbe
parent 33ee81f330e98cdd2766e2672fb5ac3950068e64
author XXX 1655213771 +0800
committer XXX 1655213771 +0800

wrote hello.txt
```
由此可知：`git cat-file -p <hash>`可以查看对应hash值的提交的详细信息。如果我们再来运行下述命令（键入tree的hash值）：
``` bash
git cat-file -p c9099b9c93f05b733a5dddd6f8b26657758f0331
```
得到了如下的输出：
```
100644 blob d8036c15d7630cc6056052a2ec506483a7ce2d0a    readme.txt
```
这是tree提交的内容。我们再来运行下述命令查看具体的修改内容：
``` bash
git cat-file -p d8036c15d7630cc6056052a2ec506483a7ce2d0a
```
得到了如下输出：
```
Git is a version control system.
Git is free software.
```
这就是对`readme.txt`文件的具体的修改内容。

## 修改仓库中的文件
我们对`/learngit/readme.txt`修改如下：
```
Git is a version control system.
Git is free software.

Tony love git!
```
此时，我们在Linux终端运行下述命令：
``` bash
git status
```
Linux终端里输出了下述内容：
```
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       modified:   readme.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```
这说明，我们仅仅是修改了`readme.txt`文件，还没有提交我们的修改。如果我们过了几周再查看文件，忘记我们的修改了，那我们该怎么办呢？我们可以在Linux终端里键入下述命令：
``` bash
git diff readme.txt
```
得到了如下的输出：
```
diff --git a/readme.txt b/readme.txt
index d8036c1..5ced1d6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,4 @@
 Git is a version control system.
-Git is free software.
\ No newline at end of file
+Git is free software.
+
+Tony love git!
\ No newline at end of file
```
由此，我们对`readme.txt`文件做了哪些修改就一目了然了。此时，我们在Linux终端里运行下述命令：
``` bash
git add readme.txt
git commit -m "add Tony love git"
```
如此就将我们的修改提交到了Git仓库`/learngit`。此时，运行下述命令查看git的状态：
``` bash
git status
```
得到了如下的输出：
```
# On branch master
nothing to commit, working directory clean
```
这说明我们的修改都已经提交到了git仓库`/learngit`

## 版本回退
首先，我们在Linux终端里运行下述命令，查看我们的修改日志：
``` bash
git log
```
Linux终端里输出了下述内容：
```
commit 3d57ae76cd176fbef5345f85c132f1a69ab72f80
Author: XXX
Date:   Wed Jun 15 21:21:18 2022 +0800

    add Tony love git

commit 0f3d14f865fcb18f04fc8cc00517b7737bc51e4c
Author: XXX
Date:   Tue Jun 14 21:36:11 2022 +0800

    wrote hello.txt

commit 33ee81f330e98cdd2766e2672fb5ac3950068e64
Author: XXX
Date:   Tue Jun 14 21:23:20 2022 +0800

    wrote a readme file
```
或者，我们可以在Linux终端里运行下述命令：
``` bash
git log --pretty=oneline
```
得到了如下的输出：
```
3d57ae76cd176fbef5345f85c132f1a69ab72f80 add Tony love git
0f3d14f865fcb18f04fc8cc00517b7737bc51e4c wrote hello.txt
33ee81f330e98cdd2766e2672fb5ac3950068e64 wrote a readme file
```
这是简化版的修改日志。

此时，如果我们想回退到上一个仓库版本，只需运行下述命令：
``` bash
git reset --hard HEAD^
```
得到了如下的输出：
```
HEAD is now at 0f3d14f wrote hello.txt
```
此时，我们用`cat readme.txt`命令看一下`readme.txt`文件里的内容，可以看到，`readme.txt`文件里已经变成这个样子了：
```
Git is a version control system.
Git is free software.
```
这说明我们已经回退到了上一个版本。如果我们想要取消我们的回退，继续恢复回退之前的版本，我们首先在Linux终端里运行下述命令：
``` bash
git reflog
```
得到了如下的输出：
```
0f3d14f HEAD@{0}: reset: moving to HEAD^
3d57ae7 HEAD@{1}: commit: add Tony love git
0f3d14f HEAD@{2}: commit: wrote hello.txt
33ee81f HEAD@{3}: commit (initial): wrote a readme file
```
我们看到了我们的命令列表。我们接下来运行下述命令：
``` bash
git reset --hard 3d57ae7
cat readme.txt
```
可以看到，`readme.txt`中已经恢复了如下的内容：
```
Git is a version control system.
Git is free software.

Tony love git!
```
