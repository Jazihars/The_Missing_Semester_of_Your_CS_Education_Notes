# 如何高效地使用你的电脑（1）
在这个笔记中，我将记录麻省理工学院著名的 **《计算机教育中缺失的一课》** 的第1课的学习笔记。这门课的主要目的在于，教你如何高效地使用计算机中你常用的各种工具。众所周知，计算机已经深入了我们生活的方方面面。但是，你不一定真的会**高效地**使用它。这门课的目的就在于，教你如何高效地使用工具，帮助你高效地使用计算机。第一节课的内容是**shell**

我是通过vscode远程连接Linux服务器的，因此，我会通过vscode来打开我的Linux shell。当我通过vscode的Remote-SSH远程连接Linux服务器之后，打开vscode终端（使用``Ctrl + ` ``快捷键），就可以键入Linux shell命令了。

## 一些Linux命令
### date命令
首先，我们在Linux终端里键入如下的命令：
``` bash
date
```
得到了如下的输出：
```
Wed May 11 18:14:47 CST 2022
```
这个`date`命令的含义是：输出当前的日期和时间。

### echo命令
接下来，我们学习另一个`echo`命令。在Linux终端里键入如下的命令：
``` bash
echo hello
```
得到了输出：
```
hello
```
echo命令其实就是实现输出一个字符串的功能。
如果我们要输出带有空格的字符串，可以用如下的方式。运行下述命令：
``` bash
echo "Hello World"
echo Hello\ World
```
这两个命令都得到了下面的输出：
```
Hello World
```
这就是输出一个带有空格的字符串的方法。

### 环境变量
什么是环境变量？我们参考中文维基百科上对环境变量的定义（当然，你也可以自行查阅英文维基百科或更多的资料。不过在这里，我们只需要知道一个最基本的概念就好）：
>环境变量是一个动态命名的值，可以影响计算机上进程的行为方式。例如一个正在运行的进程可以查询TEMP环境变量的值，以发现一个合适的位置来存储临时文件，或者查询HOME或USERPROFILE变量，以找到运行该进程的用户所拥有的目录结构。
>
>在所有 UNIX 和 类Unix系统中, 每个进程都有其各自的环境变量设置。 缺省情况下, 当一个进程被创建时， 除了创建过程中的明确更改外，它继承了其父进程的绝大部分环境设置。 在API层级上, 使用fork和exec函数进行变量设置。或利用bash shell文件, 使用特殊的命令调用来改变环境变量：通过Env 间接替代或者使用ENVIRONMENT_VARIABLE=VALUE <command> 标识. 所有的UNIX操作系统 以及DOS 和Microsoft Windows 都是用环境变量，但是它们使用不同的环境变量名称。我们可以通过运行程序来访问环境变量的值。环境变量的例子包括：
>PATH 罗列出 shell 搜索 用户 输入的执行命令所在的目录。
>HOME (类Unix系统) 和 userprofile (Microsoft Windows) 表示用户的家目录在文件系统中的位置。
>TERM (类Unix系统) 指定使用终端或虚拟终端 的类型 (如, vt100 或 终端)。
>CVS_RSH (类Unix系统) 该选项可用于 ext 方式中指明 cvs 客户端寻找远端 shell 的路径，用作连接 cvs 服务器和以更高的优先权覆盖 $CVS_RSH 环境变量中指定的路径。
>MAIL (类Unix系统) 当前用户的邮件存放目录。
>Shell 脚本 和 批处理文件 使用环境变量来存储临时值，用于以后在脚本中引用，也用于传递数据和参数给子进程。 在Unix系统中，一个在脚本或程序中更改的环境变量值只会影响该进程，亦可能影响其子进程。其父进程和无关进程将不受影响。在DOS中，更改或删除一个批处理文件中的环境变量值将改变变量的期限命令的存在。
>
>在Unix系统通过初始化脚本启动时，环境变量通常会在此时被初始化，因此会被系统中的其它进程所继承。用户可以而且经常添加环境变量到他们使用的shell脚本中。 在Windows系统中，环境变量存储在 Windows 注册表中。

或许，仅仅看了这段文字还不是特别理解。没关系，之后会慢慢理解的。我们来看一下`PATH`这个具体的环境变量是什么内容。在Linux终端里运行下述命令：
``` bash
echo $PATH
```
得到了如下的输出：
```
/home/users/XXX/.vscode-server/bin/57fd6d0195bb9b9d1b49f6da5db789060795de47/bin/remote-cli:/usr/local/gcc-5.4.0/bin:/root/tools/arcanist/bin:/usr/local/openmpi/bin:/usr/local/bin:/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/cuda-11.1/bin:/usr/local/hadoop-2.7.2/bin:/home/users/XXX/miniconda3/condabin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/users/XXX/.local/bin:/home/users/XXX/bin
```
这些路径就是在我的Linux机器上，shell程序在执行一条命令时会去搜寻的所有路径。
那如果我们想要知道，当我们执行`echo`命令时，究竟运行了哪个程序，我们可以使用`which`命令来查看。运行下述命令：
``` bash
which echo
```
得到了如下的输出：
```
/bin/echo
```
因此，当我们执行`echo`命令时，实际运行的就是`/bin/echo`这个程序。

### 关于路径的命令
运行下述命令：
``` bash
pwd
```
得到了如下的输出：
```
/home/users/XXX
```
`pwd`命令用于查看我们的当前路径（它的英文全程为`print working directory`）。注意：`pwd`命令给出的是当前路径的绝对路径。

接下来我们可以尝试改变路径。使用`cd`命令来改变路径。运行下述命令：
``` bash
cd /home
```
我们进入了/home路径。如果此时我们来运行一下`pwd`命令，可以看到如下的输出：
```
/home
```
这说明，我们当前正在`/home`路径下。

在Linux系统中，`.`代表当前路径，`..`代表父路径。比如，我们来运行下述命令：
``` bash
cd ..
```
我们可以看到，我们已经从`/home`路径进入了根路径`/`。此时此刻，我们在根路径`/`下。我们来运行下述命令：
``` bash
cd ./home
```
可以看到，我们回到了`/home`路径下。如果这时我们再次运行`cd ./home`命令，会看到如下的输出：
```
bash: cd: ./home: No such file or directory
```
说明当前路径`/home`之下，没有名为`home`的路径了。即：不存在`/home/home`路径。

我们可以使用`../../../`命令一直向上，回到根目录。试运行下述命令：
``` bash
cd ../../../../../../../../../../../
```
结果是，我们回到了根目录。

我们可以使用`ls`命令查看当前目录下都有什么东西。测试下述命令：
``` bash
ls
```
结果就是：输出了当前目录下的所有内容。
或者我们可以使用下述命令：
``` bash
ls ..
```
这个命令输出了当前路径的父路径里的所有内容。`ls ..`输出的内容和`cd .. && ls`输出的内容是完全一样的。

还有一个固定的回到home路径的命令：
``` bash
cd ~
```
这个命令是回到我的home路径的命令。

如果我想回到我刚刚访问过的路径，可以使用下述命令：
``` bash
cd -
```
可以看到，`cd -`命令实现了回到我刚刚访问过的路径。如果连续运行`cd -`命令，则可以在我最近访问的两个路径之间来回跳转。

### 查看帮助
如果我们想查看某个命令的帮助，可以使用`--help`参数。例如，运行下述命令：
``` bash
ls --help
```
输出了如下内容：
```
Usage: ls [OPTION]... [FILE]...
List information about the FILEs (the current directory by default).
Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.

Mandatory arguments to long options are mandatory for short options too.
  -a, --all                  do not ignore entries starting with .
  -A, --almost-all           do not list implied . and ..
      --author               with -l, print the author of each file
  -b, --escape               print C-style escapes for nongraphic characters
      --block-size=SIZE      scale sizes by SIZE before printing them; e.g.,
                               '--block-size=M' prints sizes in units of
                               1,048,576 bytes; see SIZE format below
  -B, --ignore-backups       do not list implied entries ending with ~
  -c                         with -lt: sort by, and show, ctime (time of last
                               modification of file status information);
                               with -l: show ctime and sort by name;
                               otherwise: sort by ctime, newest first
  -C                         list entries by columns
      --color[=WHEN]         colorize the output; WHEN can be 'never', 'auto',
                               or 'always' (the default); more info below
  -d, --directory            list directories themselves, not their contents
  -D, --dired                generate output designed for Emacs' dired mode
  -f                         do not sort, enable -aU, disable -ls --color
  -F, --classify             append indicator (one of */=>@|) to entries
      --file-type            likewise, except do not append '*'
      --format=WORD          across -x, commas -m, horizontal -x, long -l,
                               single-column -1, verbose -l, vertical -C
      --full-time            like -l --time-style=full-iso
  -g                         like -l, but do not list owner
      --group-directories-first
                             group directories before files;
                               can be augmented with a --sort option, but any
                               use of --sort=none (-U) disables grouping
  -G, --no-group             in a long listing, don't print group names
  -h, --human-readable       with -l, print sizes in human readable format
                               (e.g., 1K 234M 2G)
      --si                   likewise, but use powers of 1000 not 1024
  -H, --dereference-command-line
                             follow symbolic links listed on the command line
      --dereference-command-line-symlink-to-dir
                             follow each command line symbolic link
                               that points to a directory
      --hide=PATTERN         do not list implied entries matching shell PATTERN
                               (overridden by -a or -A)
      --indicator-style=WORD  append indicator with style WORD to entry names:
                               none (default), slash (-p),
                               file-type (--file-type), classify (-F)
  -i, --inode                print the index number of each file
  -I, --ignore=PATTERN       do not list implied entries matching shell PATTERN
  -k, --kibibytes            default to 1024-byte blocks for disk usage
  -l                         use a long listing format
  -L, --dereference          when showing file information for a symbolic
                               link, show information for the file the link
                               references rather than for the link itself
  -m                         fill width with a comma separated list of entries
  -n, --numeric-uid-gid      like -l, but list numeric user and group IDs
  -N, --literal              print raw entry names (don't treat e.g. control
                               characters specially)
  -o                         like -l, but do not list group information
  -p, --indicator-style=slash
                             append / indicator to directories
  -q, --hide-control-chars   print ? instead of nongraphic characters
      --show-control-chars   show nongraphic characters as-is (the default,
                               unless program is 'ls' and output is a terminal)
  -Q, --quote-name           enclose entry names in double quotes
      --quoting-style=WORD   use quoting style WORD for entry names:
                               literal, locale, shell, shell-always, c, escape
  -r, --reverse              reverse order while sorting
  -R, --recursive            list subdirectories recursively
  -s, --size                 print the allocated size of each file, in blocks
  -S                         sort by file size
      --sort=WORD            sort by WORD instead of name: none (-U), size (-S),
                               time (-t), version (-v), extension (-X)
      --time=WORD            with -l, show time as WORD instead of default
                               modification time: atime or access or use (-u)
                               ctime or status (-c); also use specified time
                               as sort key if --sort=time
      --time-style=STYLE     with -l, show times using style STYLE:
                               full-iso, long-iso, iso, locale, or +FORMAT;
                               FORMAT is interpreted like in 'date'; if FORMAT
                               is FORMAT1<newline>FORMAT2, then FORMAT1 applies
                               to non-recent files and FORMAT2 to recent files;
                               if STYLE is prefixed with 'posix-', STYLE
                               takes effect only outside the POSIX locale
  -t                         sort by modification time, newest first
  -T, --tabsize=COLS         assume tab stops at each COLS instead of 8
  -u                         with -lt: sort by, and show, access time;
                               with -l: show access time and sort by name;
                               otherwise: sort by access time
  -U                         do not sort; list entries in directory order
  -v                         natural sort of (version) numbers within text
  -w, --width=COLS           assume screen width instead of current value
  -x                         list entries by lines instead of by columns
  -X                         sort alphabetically by entry extension
  -1                         list one file per line

SELinux options:

  --lcontext                 Display security context.   Enable -l. Lines
                             will probably be too wide for most displays.
  -Z, --context              Display security context so it fits on most
                             displays.  Displays only mode, user, group,
                             security context and file name.
  --scontext                 Display only security context and file name.
      --help     display this help and exit
      --version  output version information and exit

SIZE is an integer and optional unit (example: 10M is 10*1024*1024).  Units
are K, M, G, T, P, E, Z, Y (powers of 1024) or KB, MB, ... (powers of 1000).

Using color to distinguish file types is disabled both by default and
with --color=never.  With --color=auto, ls emits color codes only when
standard output is connected to a terminal.  The LS_COLORS environment
variable can change the settings.  Use the dircolors command to set it.

Exit status:
 0  if OK,
 1  if minor problems (e.g., cannot access subdirectory),
 2  if serious trouble (e.g., cannot access command-line argument).

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
For complete documentation, run: info coreutils 'ls invocation'
```
因此，`ls --help`命令显示了`ls`命令的所有帮助。

在上述关于`ls`命令的帮助内容中，有这样的一条帮助：
```
-l                         use a long listing format
```
这说明，如果加上`-l`参数，可以以列表形式显示当前目录下的所有内容。测试下述命令：
``` bash
ls -l
```
可以看到如下的内容：
```
total 52
drwxrwxr-x   2 XXX XXX  4096 Apr 27 17:07 bin
drwxrwxr-x   2 XXX XXX    42 Apr 26 19:11 compiler_compat
drwxrwxr-x   2 XXX XXX    27 Apr 27 17:07 condabin
drwxrwxr-x   2 XXX XXX  4096 Apr 27 17:07 conda-meta
drwxrwxr-x   4 XXX XXX    81 May 10 11:17 envs
drwxrwxr-x   4 XXX XXX    47 Apr 26 19:11 etc
drwxrwxr-x   8 XXX XXX  4096 Apr 27 17:07 include
drwxrwxr-x  15 XXX XXX  4096 Apr 27 17:07 lib
-rw-r--r--   1 XXX XXX 11055 Jan 28 04:05 LICENSE.txt
drwxrwxr-x 164 XXX XXX 16384 May  9 21:02 pkgs
drwxrwxr-x  10 XXX XXX   152 Apr 26 19:11 share
drwxrwxr-x   3 XXX XXX    30 Apr 26 19:11 shell
drwxrwxr-x   3 XXX XXX   178 Apr 27 17:07 ssl
drwxrwxr-x   3 XXX XXX    25 Apr 26 19:11 x86_64-conda_cos6-linux-gnu
drwxrwxr-x   3 XXX XXX    25 Apr 26 19:11 x86_64-conda-linux-gnu
```
可以看到，`ls -l`命令以列表形式显示了当前路径下的所有内容。
如果我们回到根路径，测试下述命令：
``` bash
cd /
ls -l
```
得到了如下的输出：
```
total 2668
lrwxrwxrwx.    1 root root       7 Jan 14 00:02 bin -> usr/bin
dr-xr-xr-x.    5 root root    4096 May  5 10:00 boot
drwxr-xr-x    21 root root    3460 May  5 10:02 dev
drwxr-xr-x.  115 root root    8192 May 11 14:35 etc
drwxr-xr-x     4 root root      47 Jan 18 18:06 home
drwxrwxrwx    14 root root     228 May 10 12:38 bu
lrwxrwxrwx.    1 root root       7 Jan 14 00:02 lib -> usr/lib
lrwxrwxrwx.    1 root root       9 Jan 14 00:02 lib64 -> usr/lib64
drwxr-xr-x.    2 root root       6 Apr 11  2018 media
drwxr-xr-x.    4 root root      48 Mar 15 13:11 mnt
drwxr-xr-x     2 root root    4096 Jan 17 15:13 op_scripts
-rw-r--r--.    1 root root 2609034 Jan 14 17:56 op_scripts.tar.gz
drwxr-xr-x.    3 root root      45 Jan 14 18:09 opt
dr-xr-xr-x  1168 root root       0 May  5 09:59 proc
dr-xr-x---.   11 root root    4096 May  9 15:48 root
drwxr-xr-x    35 root root    1060 May  8 18:44 run
lrwxrwxrwx.    1 root root       8 Jan 14 00:02 sbin -> usr/sbin
drwxr-xr-x.    2 root root       6 Apr 11  2018 srv
dr-xr-xr-x    13 root root       0 May 11 21:01 sys
drwxr-xr-x     3 root root      19 Mar 17 13:19 titan
drwxrwxrwt. 1455 root root   69632 May 11 21:01 tmp
drwxr-xr-x.   14 root root     166 Jan 17 11:15 usr
drwxr-xr-x.   20 root root     285 Mar 17 13:19 var
```
可以看到，所有路径和文件的所有者都是`root`。

我们再来试试下述命令：
``` bash
ls -l /usr/bin/
```
可以看到，这个命令以列表的形式输出了`/usr/bin/`这个路径下的所有内容。而且，我们可以看到：`/usr/bin/`这个路径下的所有内容对所有用户都有`x`权限。这意味着，`/usr/bin/`这个路径下的所有内容都是所有人可以执行的。

如果我要进入`/usr/bin/`这个路径，则意味着，我必须拥有`/`、`/usr`和`/usr/bin/`这三个路径的`x`权限。这就是说，如果我要进入一个路径，我必须拥有这个路径本身和它的所有父路径的`x`权限。

### 改名字和复制文件
使用`mv`命令可以很方便地给一个文件或路径改名字。例如，如果当前路径下有一个名为`apple.md`的文件。运行下述命令：
``` bash
mv apple.md banana.md
```
可以看到，名为`apple.md`的文件已经变成了名为`banana.md`的文件了。

我们也可以使用`cp`命令来复制文件。当前路径下有一个名为`banana.md`的文件。测试下述命令：
``` bash
cp banana.md ../apple.md
ls ..
```
可以看到，当前路径的父路径`..`下已经有了一个名为`apple.md`的文件。这个`apple.md`文件的内容和当前路径下的`banana.md`文件的内容是完全一样的。

### 删除
使用下述命令：
``` bash
rm ../apple.md
```
来删除父路径里的`apple.md`文件。

使用下述命令：
``` bash
rmdir aaaaa/
```
来删除一个空路径`aaaaa/`。注意：`rmdir`命令只能用来删除空路径。

### 创建路径
使用`mkdir`命令来创建一个路径。

### 手工查看一个命令的详细信息
`man`是一个Linux命令行实用程序，用于显示linux命令、系统调用、库函数等的帮助手册。比如，我们测试下述命令：
``` bash
man ls
```
可以看到ls命令的帮助手册。

### 清除屏幕中的内容
使用`clear`命令或`Ctrl + L `快捷键，来清除屏幕中的内容。

### 使用`>`或`<`来重定向内容
测试下述命令：
``` bash
echo hello > hello.txt
```
可以看到，这行命令在Linux终端里什么都没有输出，但是，它却把一个字符串`hello`写入了当前路径下的`hello.txt`文件。我们再来运行下述命令：
``` bash
cat hello.txt
```
产生了如下的输出：
```
hello
```
但是，`cat`命令本身，也可以使用`>`和`<`符号来重定向输入和输出。例如，测试下述命令：
``` bash
cat < hello.txt > hello2.txt
```
这行命令的作用是：把`hello.txt`中的内容复制到当前路径下的`hello2.txt`文件中（如果没有`hello2.txt`文件，就直接在当前路径下创建`hello2.txt`文件）。

使用`>>`符号表示附加一个内容给一个文件。例如，测试下述命令：
``` bash
cat < hello.txt >> hello2.txt
cat hello2.txt
```
结果为：
```
hello
hello
```
这表明，`>>`符号可以把一个内容附加给一个文件，而不是仅仅取代这个文件中的内容。

### pipe symbol
我们接下来学习Linux中的pipe symbol（就是竖线符号`|`）。`|`符号的作用是：**把左边内容的输出作为右边内容的输入。**

首先，我们来运行下述命令，获取根路径下的所有内容：
``` bash
ls -l /
```
得到了如下的输出：
```
total 2668
lrwxrwxrwx.    1 root root       7 Jan 14 00:02 bin -> usr/bin
dr-xr-xr-x.    5 root root    4096 May  5 10:00 boot
drwxr-xr-x    21 root root    3460 May  5 10:02 dev
drwxr-xr-x.  115 root root    8192 May 11 14:35 etc
drwxr-xr-x     4 root root      47 Jan 18 18:06 home
drwxrwxrwx    14 root root     228 May 10 12:38 bu
lrwxrwxrwx.    1 root root       7 Jan 14 00:02 lib -> usr/lib
lrwxrwxrwx.    1 root root       9 Jan 14 00:02 lib64 -> usr/lib64
drwxr-xr-x.    2 root root       6 Apr 11  2018 media
drwxr-xr-x.    4 root root      48 Mar 15 13:11 mnt
drwxr-xr-x     2 root root    4096 Jan 17 15:13 op_scripts
-rw-r--r--.    1 root root 2609034 Jan 14 17:56 op_scripts.tar.gz
drwxr-xr-x.    3 root root      45 Jan 14 18:09 opt
dr-xr-xr-x  1347 root root       0 May  5 09:59 proc
dr-xr-x---.   11 root root    4096 May  9 15:48 root
drwxr-xr-x    35 root root    1060 May  8 18:44 run
lrwxrwxrwx.    1 root root       8 Jan 14 00:02 sbin -> usr/sbin
drwxr-xr-x.    2 root root       6 Apr 11  2018 srv
dr-xr-xr-x    13 root root       0 May 11 21:01 sys
drwxr-xr-x     3 root root      19 Mar 17 13:19 titan
drwxrwxrwt. 1515 root root   69632 May 12 19:13 tmp
drwxr-xr-x.   14 root root     166 Jan 17 11:15 usr
drwxr-xr-x.   20 root root     285 Mar 17 13:19 var
```
我们来思考一下，如果我们想要获得上述输出的最后一行，该怎么办？我们可以运行下述命令：
``` bash
ls -l / | tail -n1
```
得到了如下的输出：
```
drwxr-xr-x.   20 root root     285 Mar 17 13:19 var
```
可以看到，这正是上述根目录下的最后一条内容。

我们还可以连续使用这种重定向。测试下述命令：
``` bash
ls -l / | tail -n1 > ls.txt
cat ls.txt
```
这行命令的功能是把根路径下的内容的最后一行写入当前路径下一个名为`ls.txt`的文件中。我们也得到了下述输出：
```
drwxr-xr-x.   20 root root     285 Mar 17 13:19 var
```

我们再来尝试一些稍微复杂一点的例子。首先，运行下述命令：
``` bash
curl --head --silent google.com
```
得到了如下的输出：
```
HTTP/1.1 301 Moved Permanently
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Date: Thu, 12 May 2022 11:32:11 GMT
Expires: Sat, 11 Jun 2022 11:32:11 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 219
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
```
注意：我需要在一台网络正常的机器上运行上述命令。如果我的Linux机器的网络状况不正常，则输出内容可能会不一样。
上述输出内容是google.com的http头。我们接下来测试下述命令：
``` bash
curl --head --silent google.com | grep -i content-length
```
得到了如下的输出：
```
Content-Length: 219
```
Linux `grep` 命令用于查找文件里符合条件的字符串。`grep`命令的`-i`参数意为忽略字符的大小写。因此，上述命令查找出了包含`content-length`的内容。

我们再来运行下述命令：
``` bash
curl --head --silent google.com | grep -i content-length | cut --delimiter=' ' -f2
```
得到了输出：
```
219
```
`cut`命令的`--delimiter=`参数用于自定义分隔符，`-f`与`-d`一起使用，指定显示哪个区域。

## 最后的一些总结
下面的命令：
``` bash
sudo su
```
用于切换到root用户，获得最大的权限。但是，一般不建议这样做。

下述命令：
``` bash
cd /sys/
```
用于进入系统的内核部分。

运行下述命令：
``` bash
echo $SHELL
```
得到了如下的输出：
```
/bin/bash
```
这说明，我目前使用的是bash这种类型的shell。再次强调：`echo`程序的功能是将该参数打印出来。

## 练习题
1.将以下内容一行一行地写入ls.txt文件：
```
#!/bin/sh
curl --head --silent https://missing.csail.mit.edu
```
答案：依次运行下述命令：
``` bash
echo '#!/bin/sh' > ls.txt
echo curl --head --silent https://missing.csail.mit.edu >> ls.txt
```


2.运行下述命令：
``` bash
curl --head --silent https://missing.csail.mit.edu > ls.txt
```
结果：`ls.txt`文件的内容变成了下述这样：
```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 7991
Server: GitHub.com
Content-Type: text/html; charset=utf-8
Last-Modified: Fri, 04 Mar 2022 17:03:44 GMT
Access-Control-Allow-Origin: *
ETag: "62224670-1f37"
expires: Thu, 12 May 2022 12:27:14 GMT
Cache-Control: max-age=600
x-proxy-cache: MISS
X-GitHub-Request-Id: 108C:6E3E:220FE0:2A1CCC:627CFACA
Accept-Ranges: bytes
Date: Thu, 12 May 2022 12:26:13 GMT
Via: 1.1 varnish
Age: 538
X-Served-By: cache-bur-kbur8200074-BUR
X-Cache: HIT
X-Cache-Hits: 1
X-Timer: S1652358373.306971,VS0,VE1
Vary: Accept-Encoding
X-Fastly-Request-ID: 78dc836ee26d90f30f23a73d82a237e32d99f35a


```


3.将`curl --head --silent https://missing.csail.mit.edu`命令的最后更改日期信息，写入主目录下的`last-modified.txt`的文件中。
答案：运行下述命令：
``` bash
curl --head --silent https://missing.csail.mit.edu | grep -i last-modified > ~/last-modified.txt
cat ~/last-modified.txt
```
终端里输出了如下的内容：
```
Last-Modified: Fri, 04 Mar 2022 17:03:44 GMT
```

至此，麻省理工学院《计算机教育中缺失的一课》的第一课shell就学习完了。