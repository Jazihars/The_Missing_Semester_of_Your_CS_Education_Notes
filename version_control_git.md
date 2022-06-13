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