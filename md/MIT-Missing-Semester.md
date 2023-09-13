

MIT-Missing-Semester  

https://missing-semester-cn.github.io/

## 1.shell  

`cd -` 返回之前的目录  
`>>` 输出重定向为追加而不是覆盖  
目录权限：`r` 查看目录文件列表，`w` 在目录中重命名、删除、创建文件，`x` 搜索权限，是否允许进入该目录  
`tee FILENAME` 将输入内容同时输出至标准输出和文件  
`tail -n x` 输出输入的最后 x 行  

## 2.shell 工具、脚本  

空格分割参数，因此 `foo = bar` 无法赋值  

`"Value is $foo"` 将展开 `foo` 变量的值，`'Value is $foo'` 则不  
`$_` 上一个命令的最后一个参数  
`$#` 为参数数量  
`$$` 为进程 PID  
`$@` 将把所有参数展开，展开结果类似集合，可以：`for filename in "$@"; do`  
`!!` 被替换为上一个命令  
`;` 将两个语句连接在同一行  

`$(pwd)` 将展开为 `pwd` 命令的输出  
`2>` 用于重定向标准错误流  
process substitution：`A <(B1) <(B2) ...` 依次执行 `B1,B2...` 并将输出放入一个类似临时文件的东西中（每个命令都将产生一个不同的临时文件），将这些文件的标识符提供给 `A`。这意味着 `A` 的参数应为文件，如 `cat`，`ls`：  
```plaint  
$ ls <(ls) <(ls ..)
/dev/fd/62  /dev/fd/63
```  

`source xxx.sh` 在 shell 中加载并执行 `xxx.sh`  
多个参数 `xxxA xxxB xxxC` 可缩写为 `xxx{A,B,C}`。`xxx{A,B,C}yyy{D,E}` 将展开为 6 个参数。`{a..z}` 等可用    
shebang：在脚本的第一行给出解释方法，例如 `#!/bin/sh`，可用 `#!/usr/bin/env python` 等检测解释器路径  
`cd xxx || exit` 进入目录失败即退出  
`/dev/null` 将抛弃所有输入内容  

`tldr COMMAND` 给出命令使用例子  
`touch FILENAME` 修改文件访问时间为当前  
`find -exec COMMAND {} \;` 用于对每个 find 的结果**分别执行**命令，`find -exec COMMAND {} +` 用于将每个 find 的结果依次作为命令的多个参数**执行一次**命令。其中花括号引用 find 的结果  
`locate xxx` 查找并构建索引  
`grep xxx FILENAME` 在文件中查找字符，`-R` 选项递归。类似有 `rg` 命令，ripgrep  
`grep -v` 查找不匹配的项  
`fzf` 交互、模糊查找  
`tree` 树形打印目录结构  
`cut` 可以输入按照自定义的分隔符分成若干段，并输出其中某些段  
`tr` 替换、缩减、删除标准输入中的字符，例如 `tr -s ' '` 将所有连续空格缩减为一个  
`xargs` 从标准输入中获得参数，并送给指定命令  


## 3.vim  

`tabnew` 生成新标签页，每个标签页有若干窗口，每个窗口对应一个缓冲区。一个缓冲区可对应多个窗口，例如当同时查看一个文件的多个部分时  
`c` change 操作，行为与 `d` 相同但删除后进入插入模式  
`shift-v` 可视行，每次选择一整行  
`~` 反转大小写  
`.` 重复前一个编辑操作  
`数字+操作` 执行指定次数操作  
`%` 在可匹配的字符间跳转  
`a` 和 `i` 修饰符，分别为 around inside，操作+a/i+可匹配字符 可删除括号中所有内容，a 包括括号本身，i 不包括。例如 `di(`  



## 4.data wrangling  

`less` 在终端中阅读  
`sed` 流文本编辑器，`sed -E` 使用更现代的正则语法  
`wc` word count，`-l` 可计算行数  
`sort` 将输入的行排序，`uniq` 将排好序的输入去重  
`awk` 基于列的流处理器，参数需使用单引号 
`bc` 命令行计算器  
`paste` 将多个文件中的相同行号的行合并为同一行，或将一个文件中的不同行合并为同一行  

正则：  
`*` 0 或多次，`+` 1 或多次，`?` 0 或 1 次  
默认每行只匹配一次，但贪心匹配，如 `xxxAxxxA` 将被正则语句 `.*A` 完全匹配  
在 `*` 或 `+` 后加 `?` 可使匹配变为非贪心，在上面的例子中匹配将停在第一个 `A` 处  
capture groups：每个小括号之间的内容将成为一个捕获组，可以在替换中用 \\+数字引用，例如 `\2` 引用第二个捕获组  
https://regex101.com/  




## 5.command line environment  


### jobs control  

signal：终端可通过信号与程序通信，如 SIGINT、SIGHUP 等，大部分可被程序捕获并绑定行为，但 SIGKILL 等无法捕获  
`jobs` 显示当前任务的状态，`bg` 将任务移动至后台，可依此继续运行停止的程序，如 `bg %1`  
`kill` 可发送任何类型的信号，如 `kill -STOP %1`。另外，`kill -0` 将不会发送任何信号，若进程不存在则返回状态码非零，可用于检测进程是否结束  
`nohup COMMAND` 将忽略挂起信号 SIGHUP，可避免登出时终止  
`pgrep pkill` 可分别用来根据进程名查找、结束进程，后者也同 `kill` 一样可以发送任何类型的信号  


### terminal multiplexer  

#### tmux  

session 包含 window 包含 pane  

session 命令  
`tmux new` 新建 session，`-t xxx` 指定名称  
`tmux a -t xxx` 回到指定 session  
`tmux ls` 列出所有 session  
`tmux kill-session -t xxx` 杀死指定 session  


window 操作：快捷键+修饰符  
`control-b` 改变窗口，修饰符：`d` 退出（窗口将被保留），`p,n` 分别为上、下一个窗口，`c` 新建，数字 跳转指定窗口，`,` 重命名窗口，`&` 关闭当前窗口  

pane 操作：快捷键+修饰符  
`control-b` 改变面板，修饰符：`"` 水平分裂窗口，`%` 垂直分裂窗口，方向键 改变聚焦的面板，空格 将所有面板等距分布，`z` 使面板占据整个窗口（或恢复），`x` 关闭当前面板，`control-arrow key` 按箭头方向调整大小，`!` 将当前面板拆分为独立窗口  
`{}` 将当前面板与上、下一个交换位置，`;o` 切换到上、下一个面板  

如果在 tmux 中想对终端使用 control-b，则需按两次  

在 `~/.tmux.conf` 中使用如下设置即可改变默认的前缀组合键 control-b  
```plaint  
set -g prefix C-a
unbind C-b
bind C-a send-prefix
```  


### others  

`alias xxx=COMMAND` 为长命令设置别名，只对当前会话生效  
环境变量 `PS1` 指定了提示符，如 `[\u@\h \W]\$`  


## 6.version control  

### git 工作原理  

git 将文件夹、文件分别建模为 tree 和 blob，每次提交的快照被建模为 commit  
所有的版本将被记录为有向无环图上的一个节点，指向的节点为作出此次更改前的节点。若在开发中出现分支，将两个分支合并时，新的版本就会同时指向被合并的两个节点。因此，它们构成了有向无环图  
每个节点对应了一个版本快照，同时包含了作者、时间等元数据  

数据在 git 中均通过哈希后的 id 来索引，可以根据这样的 id 来找到相应的指针：  
```plaint  
type blob = array<byte>
type tree = map<string , tree | blob>
type commit = struct{
    parents: array<commit>  //指向的节点
    author: string
    message: string
    snapshot: tree   //该快照的根目录
}
type object = blob | tree | commit
objects = map<string , object>   //通过 object 的 SHA-1 值映射到它的指针
def store(o):
    id = hash(o)
    objects[id] = o
def load(id):
    return objects[id]
references = map<string , string>   //通过可读的版本描述映射到相应快照的 SHA-1
```  

### 基本指令  

工作目录独立于 git 的所有分支  
git 存在一个 stagin area，决定 git 在下次创建快照的时候会包含哪些更改  
`git add FILENAME` 将文件、文件夹从工作目录加入缓冲区。`git add :/` 将加入仓库中的所有内容  
`git status` 将显示这个缓冲区，以及 commit 的记录等  
`git commit` 将提交缓冲区中内容，显示此快照的哈希值的一部分。`git commit -a` 将提交所有更改  
`git cat-file -p HASH` 将显示该哈希对应的 object 的全部内容  
`git log --all --graph --decorate` 将显示所有的提交记录为图，而不是线性展示。其中 HEAD 指最后一个快照  
`git checkout HASH/BRANCHNAME` 将通过哈希值、分支名，改变工作目录到某个其他的提交。这将确实地更改目录中文件的内容  
`git checkout FILENAME` 将舍弃工作目录中该文件的更改，回到 HEAD 指向的快照的中的状态  
`git diff NAME NAME` 可对比两个 object 的差异，哈希值、描述名、文件名等均可作为 NAME  
`git diff xxx yyy FILENAME` 将比较两个快照中某文件的差异  

### branch  

`git branch NAME` 将创建一个新的分支，可以通过 checkout 命令切换到某个分支，该操作也会更改工作目录中的文件。当创建新的提交时，当前处于的分支将会和 HEAD 一起指向新的快照  
`git branch -vv` 查看详细的分支信息  
`git merge BRANCHNAME` 将相应的分支合并到当前快照，可一次合并多个分支  

- Fast-forward merge
    - 当当前 HEAD 指向的快照是要被合并的快照的祖先时，不需创建新的 commit，只用更改 HEAD 指针
    - 可以使用 `--no-ff` 选项禁用这个特性
    - 常见于被 `git pull` 调用时
- True merg
    - Auto-merge 尝试自动合并
    - Auto-merge failed，将在合并失败的文件中的冲突进行标记，手动修改，并重新 `add` 后使用 `git merge --continue` 继续合并  
    - 合并出现冲突时，`git merge --abort` 可放弃合并并尝试退回合并前的状态，这也意味着会更改工作目录中的文件  

### 远程仓库  

`git remote` 将列出所有远程仓库
`git remote add NAME URl` 添加远程仓库，标记名字（例如 origin）等
`git push <remote> <local branch>:<remote branch>` 将本地分支推送到远程，将在远程仓库上创建一个新分支或更新某个分支
`git clone URl PATH` 将远程仓库克隆到本地，可选储存路径。使用该下载的副本初始化本地仓库
在其他机器上进行修改并 push 后，push 到的机器并不能立刻接到修改  

- 使用 `git fetch <remote>` 来与进行了 push 的远程仓库通信，同步更改，这不会更改任何本地文件、分支、HEAD 指针等
- fetch 后可以 merge 到同步了的版本，这就是一种 Fast-forward merge
- `git pull` 将先后这两条命令  

`git clone --shallow` 不会克隆仓库的整个历史记录，只下载最新快照来避免下载量过大  

### others  
 
`git add -p FILENAME` 将交互式地将文件添加进缓冲区，可用于只提交文件的某些更改。这样 add 并 commit 完，就就可以 checkout 这个文件来舍弃不想要的更改  
与上面相应，`git diff` 查看的是目前没有进入缓冲区的修改，而 `git diff --cached` 查看的是进入了缓冲区的修改  
`git blame FILENAME` 将查看文件每行修改的时间、人、提交等信息  
`git stash` 将工作目录还原回上次提交的状态，但不会完全删除这些更改，`git stash pop` 将还原这些更改  
`git bisect` 将二分查找提交历史记录，例如找到第一个不能通过测试脚本的提交  
`.gitignore` 文件中写入文件名模式，可以让 git 忽略这些文件不提示 untracked。该文件应该被跟踪  

`git log -x FILENAME` 查看在最新的 x 次提交某文件的信息  



## 7.debugging and profiling  


`logger` 向系统日志中写入  
`tac` 将反向输出文件内容，与 `cat` 相反  

- real time：程序开始运行到结束的总时间
- user time：CPU 执行用户级别代码所用的时间
- system time：CPU 执行内核级别代码所用的时间  


### profilers  

- tracing profilers：会在代码中插入内容，与原本的代码一起运行，在进入函数的时候记录相关信息得出运行时间
- sampling profilers：每隔一小段时间暂停程序，报告当前执行到的位置  

内存分析器，python memory\_profiler，C valgrind  
`perf` 与 `time` 类似，运行程序并跟踪内存相关的统计数据  

lsof 列出被进程打开的各种文件的信息  
hyperfine 可对两个程序的执行情况（用时等）进行比较  

htop, du, ncdu  
可视化工具：flame graph

## 8.meta programming  

### build system  

make 需要给出构建的目标，目标所需的依赖，从依赖编译出目标的规则  
`make <target>` 将构建指定的目标，如果没有参数来指定，默认会构建第一个  

基本的语法是：  

```plaint  
<target> : <prerequisites>
[tab]  <commands>
```  

规则即为对依赖项执行的命令，命令前默认必须为 tab，可以通过更改内置变量 `.RECIPEPREFIX` 控制这个前缀  
命令可以有多行，但每行在一个独立的 shell 中运行，也就是变量等无法继承共享  
可以使用分号将所有命令写在一行，也可在 target 前一行声明 `.ONESHELL:`  

`%` 可以进行模式匹配，例如 `%.o: %.c` 让所有 o 文件依赖相同名字的 c 代码  
makefile 定义变量与 shell 相似，也存在一些自动变量，`$*` 将被扩展为上面一行说的的匹配的模式，`$@` 扩展为目标文件名，`$<` 扩展为第一个依赖，`@^` 扩展为全部依赖并用空格隔开  
也存在一些[内置变量](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)  
定义函数的方法是  

```makefile  
define function
endef
```  

调用函数是 `$(function arguments)`，存在一些[内置函数](https://www.gnu.org/software/make/manual/html_node/Functions.html)  

phony target 是一个操作而不是文件，作为一种伪目标，例如：  

```makefile 
.PHONY: clean
clean:
      rm *.o
```  

当没有任何叫做 clean 的文件时，第一行可以省略  
当我们最终构建的结果有多个文件时，可以使用 phony target `source: a b c`，这样只需执行 `make source` 一条命令，即可构建出三个文件  
也可定义 test 目标，来执行测试  

### semantic versioning  

`x.y.z` 分别代表主要、次要、补丁版本号  
在补丁更新中应增加补丁版本号，进行了向前兼容的更新时应增加次要版本号，进行了不向前兼容的更新时应增加主要版本号  
因此，在 x 与需要依赖的版本相同时，只要 y 大于等于需要依赖的版本，即可成功依赖  

lockfile 列出了当前每个依赖所对应的具体版本号，避免了构建程序自动将依赖更新到其他版本  
vendoring 指为把依赖直接复制到项目中  

### others  

continues integration 持续集成，一种类似于钩子函数的东西，例如 github pages, codecov 等，`.git/hooks` 也包含一些执行类似功能的脚本  

tests  

- 单元测试，集成测试，回归测试
- mocking  



