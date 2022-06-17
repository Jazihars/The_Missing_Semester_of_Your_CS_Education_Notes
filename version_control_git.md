# 如何高效地使用你的电脑？｜麻省理工学院《计算机教育中缺失的一课》（5）——版本控制（Git）
在这个笔记中，我将记录麻省理工学院著名的 **《计算机教育中缺失的一课》** 的第5课的学习笔记。**这门课的主要目的在于，教你如何高效地使用计算机中你常用的各种工具**。众所周知，计算机已经深入了我们生活的方方面面。但是，你不一定真的会**高效地**使用它。这门课的目的就在于，教你如何高效地使用工具，帮助你高效地使用计算机。第5节课的内容是**版本控制（Git）**。

版本控制系统（VCSs）是一类用于追踪源代码（或其他文件、文件夹）改动的工具。顾名思义，这些工具可以帮助我们管理代码的修改历史；不仅如此，它还可以让协作编码变得更方便。版本控制系统通过一系列的快照将某个文件夹及其内容保存了起来，每个快照都包含了文件或文件夹的完整状态。同时它还维护了快照创建者的信息以及每个快照的相关信息等等。

为什么说版本控制系统非常有用？即使您只是一个人进行编程工作，它也可以帮您创建项目的快照，记录每个改动的目的、基于多分支并行开发等等。和别人协作开发时，它更是一个无价之宝，您可以看到别人对代码进行的修改，同时解决由于并行开发引起的冲突。

现代的版本控制系统可以帮助您轻松地（甚至自动地）回答以下问题：
- 当前模块是谁编写的？
- 这个文件的这一行是什么时候被编辑的？是谁作出的修改？修改原因是什么呢？
- 最近的1000个版本中，何时/为什么导致了单元测试失败？

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

## 撤销修改
我们在`readme.txt`文件的最后随便添加一些修改，比如，增加几个字符。保存之后，此时在Linux终端里键入下述命令：
``` bash
git checkout -- readme.txt
```
再打开`readme.txt`文件查看，就可以发现我们刚刚做的修改已经被撤销了。这行命令可以撤销下面两种情况的修改：
- 一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
- 一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

如果我们已经把修改提交到了暂存区，然后想要撤销修改了，这时该怎么办？我们可以先用下述命令把暂存区的修改回退到工作区：
``` bash
git reset HEAD readme.txt
```
然后再运行下述命令丢弃掉工作区的修改：
``` bash
git checkout -- readme.txt
```
此时在Linux终端运行`git status`命令，可以看到，已经没有要提交的修改了。

## 删除文件
如果我们想要删除文件`/learngit/hello.txt`该怎么办？我们当然可以直接在Linux终端运行下述命令：
``` bash
rm hello.txt
```
只不过，此时我们在Linux终端运行`git status`命令，可以看到如下的内容：
```
# On branch master
# Changes not staged for commit:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#       deleted:    hello.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```
这说明，git已经知道我们删除了`hello.txt`文件，并且此时我们的删除还没有提交。如果我们想提交我们的删除，则需要在Linux终端运行下述命令：
``` bash
git rm hello.txt
git commit -m "remove hello.txt"
```
现在，`hello.txt`文件就从我们的`/learngit`仓库中被删除了。

另一种情况是，当我们`rm hello.txt`之后，发现是删错了，我们想要恢复文件。只需在Linux终端运行下述命令：
``` bash
git checkout -- hello.txt
```

## 在Github上同步远程仓库
我们在Github上创建一个和本地完全一样名称的仓库`/Jazihars/learngit`。然后我们在本地的Linux终端`/learngit`路径下运行下述命令（我们已经在Github上添加了我们的Linux机器的SSH公钥）：
``` bash
echo "# learngit" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:Jazihars/learngit.git
git push -u origin main
```
这样就可以把本地的仓库同步到Github上的远程仓库。

虽然可以用上述这种方式来将文件推送到远程仓库。不过，强烈不建议实际操作的时候用这种方式来操作。强烈推荐的方式是：**先在Github上创建远程仓库，然后克隆到本地，再从本地推送文件到远程仓库。**

## 分支管理——创建分支的目的
接下来我们要进入git最犀利的部分了。首先明确一下，我们为什么要创建不同的分支？

假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

这就是创建分支的目的

## 创建与合并分支
接下来的实验在Windows上进行。首先，在Powershell终端`/learngit`路径下键入下述命令：
``` bash
git checkout -b dev
```
得到了如下的输出：
```
Switched to a new branch 'dev'
```
此时我们运行下述命令查看当前分支：
``` bash
git branch
```
得到了如下的输出：
```
* dev
  main
```
`git checkout`命令加上`-b`参数表示创建并切换。相当于同时运行下述的两条命令：
``` bash
git branch dev
git checkout dev
```
如果我们给`/learngit/mytest.py`（注意：采用Linux下的目录记法`/`）文件添加一些修改，然后再运行下述命令提交：
``` bash
git add mytest.py
git commit -m "modified mytest.py"
git status
```
可以看到如下的内容：
```
On branch dev
nothing to commit, working tree clean
```
可以看到，我们已经在dev分支上提交了我们的修改。接下来，我们切换到main分支。运行下述命令：
``` bash
git checkout main
```
此时可以看一下，main分支上的`/learngit/mytest.py`文件仍然是空的。接下来，运行下述命令把dev分支上的修改合并到main分支上：
``` bash
git merge dev
```
此时，main分支上的`/learngit/mytest.py`文件就已经变成刚才dev分支上修改的样子了。接下来运行下述命令删除dev分支：
``` bash
git branch -d dev
```
再次运行下述命令查看当前的分支情况：
``` bash
git branch
```
得到了如下的输出：
```
* main
```
可以看到，目前只有main分支了。此时，可以使用`git push`命令将本地的修改推送到Github远程仓库。

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在main分支上工作效果是一样的，但过程更安全。

目前，最新版本的git支持下述命令来创建并切换到新的dev分支：
``` bash
git switch -c dev
```
直接切换到已有的master分支，可以使用：
``` bash
git switch master
```
更多关于分支的命令，参见廖雪峰的教程[创建与合并分支](https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424)

## 解决分支合并中的冲突
首先，我们创建并切换到新的feature1分支。在终端里运行下述命令：
``` bash
git switch -c feature1
```
在当前的feature1分支里，修改`/learngit/mytest.py`文件的内容如下：
``` python
import torch

print("1+1=2")
```
我们在feature1分支上提交我们的修改。运行下述命令：
``` bash
git add mytest.py
git commit -m "modified mytest.py 1+1=2"
```
接下来我们切换到main分支。运行下述命令：
``` bash
git switch main
```
在main分支里，将`/learngit/mytest.py`文件的内容修改成下面这样：
``` python
import torch

print("1+1=3")
```
我们在main分支也提交对`/learngit/mytest.py`的修改。运行下述命令：
``` bash
git add mytest.py
git commit -m "modified mytest.py 1+1=3"
```
接下来我们在main分支下运行下述命令：
``` bash
git merge feature1
```
得到了如下的输出：
```
Auto-merging mytest.py
CONFLICT (content): Merge conflict in mytest.py
Automatic merge failed; fix conflicts and then commit the result.
```
果然，无法快速合并分支。因为发生了分支合并的冲突。此时在main分支之下，我们打开`/learngit/mytest.py`文件看一下，里面的内容如下：
``` python
import torch

<<<<<<< HEAD
print("1+1=3")
=======
print("1+1=2")
>>>>>>> feature1

```
此时我们来解决一下这个分支合并的冲突。在main分支下，将`/learngit/mytest.py`文件的内容修改如下（我们保留feature1分支所做的修改）：
``` python
import torch

print("1+1=2")
```
接下来我们在main分支下运行下述提交修改的命令：
``` bash
git add mytest.py  
git commit -m "modified mytest.py at main"
```
此时，就可以在终端里运行下述命令，把我们的修改提交给远程仓库了：
``` bash
git push
```

接下来我们看一下修改日志。在终端里运行下述命令：
``` bash
git log --graph --pretty=oneline --abbrev-commit
```
得到了如下的输出：
```
*   81c775b (HEAD -> main, origin/main) modified mytest.py at main
|\
| * a10031f (feature1) modified mytest.py 1+1=2
* | 6e21805 modified mytest.py 1+1=3
|/
* c8edd44 modified mytest.py
* a09c1e5 modified README.md
* e514884 my second commit
* 071440c first commit
```
由此，我们的修改过程一目了然了。我们接下来在终端里运行下述命令删除feature1分支：
``` bash
git branch -d feature1
```
大功告成！我们的分支合并操作就完成了。

接下来我们删除本地和Github上的learngit仓库，重新在Github上创建一个learngit仓库，然后我们clone这个learngit仓库到本地。在本地的终端里运行下述命令：
``` bash
git clone git@github.com:Jazihars/learngit.git
```
我们首先看一下我们在哪个分支上。在本地的终端里键入下述命令：
``` bash
git branch
```
终端里输出了下述内容：
``` bash
* main
```
这说明，我们目前在main分支上。我们创建一个新的dev分支。在本地的终端里键入下述命令：
``` bash
git switch -c dev
```
在dev分支下，创建`/learngit/mytest.py`文件，在`/learngit/mytest.py`文件中键入下述代码：
``` python
import torch

print("我在dev分支")
```
然后我们在dev分支下提交`/learngit/mytest.py`代码。在终端里键入下述命令：
``` bash
git add mytest.py
git commit -m "commit mytest.py at dev"
git push -u origin dev
```
最后一条命令运行时，终端里输出了下述内容：
```
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 312 bytes | 104.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0        
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
remote: 
remote: Create a pull request for 'dev' on GitHub by visiting:
remote:      https://github.com/Jazihars/learngit/pull/new/dev
remote:
To github.com:Jazihars/learngit.git
 * [new branch]      dev -> dev
branch 'dev' set up to track 'origin/dev'.
```
参考[git push官方文档](https://git-scm.com/docs/git-push)，`git push -u`中的`-u`参数与`--set-upstream`含义相同。
这说明，我们在远程Github仓库中创建了一个dev分支。我们可以在Github仓库`learngit`中把这个提交合并到main分支。这个部分在Github远程仓库中操作即可，用浏览器的图形界面来操作。

我们在本地的dev分支上进行第二次提交。将`/learngit/mytest.py`修改如下：
``` python
import torch

print("我在dev分支")

print("在dev分支上第二次提交")
```
接下来就可以在本地的终端里进行第二次提交了，还是提交到远程的dev分支。在本地的终端里运行下述命令：
``` bash
git add mytest.py
git commit -m "second commit of mytest.py at dev"
git push
```
现在我们可以再次在Github上用图形用户界面来把我们刚刚提交到learngit仓库dev分支上的修改合并到learngit仓库main分支上了。这个合并过程和刚才几乎一样，就是一些图形用户界面的操作，这里就不详述了。合并到main分支之后，我们的两个commit就都是`behind main`的了。合并之前，是`ahead main`的状态。

还有一点需要注意。合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。比如，我们来进行下面的实验。我们在dev分支上把`/learngit/mytest.py`修改为下面的内容：
``` python
import torch

print("我在dev分支")

print("在dev分支上第二次提交")

print("在dev分支上测试--no-ff合并")
```
然后我们提交这个修改到dev分支。在终端里运行下述命令：
``` bash
git add mytest.py
git commit -m "test --no-ff commit"
```
接下来我们在本地切换到main分支。在终端里键入下述命令：
``` bash
git switch main
```
接下来我们在本地把dev分支合并到main分支，用`--no-ff`合并方式。在终端里运行下述命令：
``` bash
git merge --no-ff -m "merge with no-ff" dev
```
终端里输出了下述内容：
```
Merge made by the 'ort' strategy.
 mytest.py | 7 +++++++
 1 file changed, 7 insertions(+)
 create mode 100644 mytest.py
```
最后，在终端里运行下述命令，查看日志：
``` bash
git log --graph --pretty=oneline --abbrev-commit
```
终端里输出了如下的内容：
```
*   c7e6d23 (HEAD -> main) merge with no-ff
|\
| * 7673b5d (dev) test --no-ff commit
| * a17d20e (origin/dev) second commit of mytest.py at dev
| * 3ca9230 commit mytest.py at dev
|/
* 312519c Initial commit
```
这就是我们的提交日志。我们此时可以在终端里把我们的提交推送到远程Github仓库。在终端里键入下述命令：
``` bash
git switch dev
git push
```
终端里输出了如下的内容：
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 343 bytes | 114.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0        
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:Jazihars/learngit.git
   a17d20e..7673b5d  dev -> dev
```
我们已经把我们的修改成功推送到了远程Github仓库的dev分支。之后，我们会学习如何直接在本地把我们的修改合并到远程仓库的main分支。目前我们暂且满足于在浏览器上使用图形用户界面来将dev分支合并到main分支。

至此，关于Github的分支操作，我们已经比较熟悉了。下面来谈谈实际操作时的经验。

## Github分支管理实际经验
在实际开发中，我们应该按照以下几个基本原则进行分支管理：
- 首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
- 那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
- 你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

这些就是我们在实际使用git/Github时的经验。我们应当遵循这些规则来进行实际的开发。

## 修复bug的分支
软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

比如，我们来举个例子。当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支issue-101来修复它，但是，等等，当前正在dev上进行的工作还没有提交。我们来人工构造这个场景。在main分支下创建文件`/learngit/bug.py`，在这个文件里输入如下的代码：
``` python
print("这里有一个待修复的bug")
```
然后把这个文件提交给main分支。

在dev分支下，向`/learngit/mytest.py`中追加代码，最后`/learngit/mytest.py`中的代码如下：
``` python
import torch

print("我在dev分支")

print("在dev分支上第二次提交")

print("在dev分支上测试--no-ff合并")

print("未完成的工作")
```
接下来我们在dev分支下在终端里运行下述命令：
``` bash
git status
```
终端里输出了下述的内容：
```
On branch dev
Your branch is up to date with 'origin/dev'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   mytest.py

no changes added to commit (use "git add" and/or "git commit -a")
```
这个时候，并不是我们不想提交dev分支下`/learngit/mytest.py`的内容，而是工作只进行到一半，还没法提交，预计完成还需1天时间。但是，必须在两个小时内修复main分支上的bug（意即，提交对main分支上的`/learngit/bug.py`的修改），我们该怎么办？

我们首先在dev分支下把当前工作隐藏起来。在dev分支下在终端里运行下述命令：
``` bash
git stash
```
终端里输出了下述内容：
```
Saved working directory and index state WIP on dev: 7673b5d test --no-ff commit
```
然后我们查看一下当前的状态。我们在终端里运行下述代码：
``` bash
git status
```
终端里输出了下述内容：
```
On branch dev
Your branch is up to date with 'origin/dev'.

nothing to commit, working tree clean
```
此时就没有需要提交的内容了。接下来我们可以放心地去修复main分支上的bug了。此时我们在dev分支下，我们在终端里运行下述命令：
``` bash
git switch main
git switch -c fix-01-bug
```
接下来在fix-01-bug分支下，把bug.py中的内容修改如下：
``` python
print("这里有一个待修复的bug，但是我们已经修复好了！")
```
此时我们在分支fix-01-bug下，我们在终端里运行下述命令：
``` bash
git add bug.py
git commit -m "fixed bugs in bug.py"
git switch main
git merge --no-ff -m "merged bugs fix in bug.py" fix-01-bug
git branch -d fix-01-bug
git push（把修改后的bug.py推送到远程仓库）
```
接下来我们回到dev分支上继续工作。目前在main分支下，在终端里运行下述命令：
``` bash
git switch dev
git stash list（查看当前隐藏的工作）
git stash pop
git stash list（再次查看当前隐藏的工作，已经没有东西了）
```
接下来可以在dev分支继续完成我们的工作。在dev分支下修改`mytest.py`的内容如下：
``` python
import torch

print("我在dev分支")

print("在dev分支上第二次提交")

print("在dev分支上测试--no-ff合并")

print("未完成的工作，已经完成！")
```
然后在dev分支下在终端里运行下述命令：
``` bash
git add mytest.py
git commit -m "job has been finished"
git push
```
然后在浏览器里用图形用户界面的方式把dev分支上的1个ahead的commit合并到main分支即可。

此处还有一个任务，是把main分支上修复好的bug复制到dev分支。这个实验先不做了，实际遇到的时候去查即可。用`git cherry-pick`命令。

还有一些提示。开发一个新feature，最好新建一个分支；如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。这些实验都不做了。遇到的时候去查即可。

## 多人协作开发
这部分内容先不做了，参见[廖雪峰多人协作教程](https://www.liaoxuefeng.com/wiki/896043488029600/900375748016320)

## 给commit打标签
这部分也不做了，参见参见[廖雪峰标签管理教程](https://www.liaoxuefeng.com/wiki/896043488029600/900788941487552)

---

至此，关于Git的学习告一段落。之后遇到不会的用法再去查即可。