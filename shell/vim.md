## 一、vim使用介绍

**vim介绍**

​		在linux系统中，大部分配置文件都是ASCII的纯文本形式存放的，所以我们在修改系统设置的时候使用简单的文本编辑软件就可以实现了。

​		vim是一个程序开发工具而不是文字处理软件，因为它包含了很多额外的功能，如：多文件编辑，区块复制等，这些功能让我们在进行配置文件修改的时候会更方便。



## 二、基本使用

由于vi/vim是一个全屏幕的文本编辑器，它工作在三种模式下：分别是**命令模式、输入模式和末行模式**。可以分别从命令模式切换到输入模式和末行模式，也可以从末行模式或输入模式切换到命令模式，但是输入模式与末行模式之间不能互相切换。

![image-20210730230119449](C:\Users\qq\AppData\Roaming\Typora\typora-user-images\image-20210730230119449.png)

**第一种：命令模式**，当我使用“vim myfile”命令打开myfile这个文件时就处于命令模式,屏幕左下角为文件名（myfile），1L 表示本文件有1 行，26C 表示此文件有26 个字符。1,25表示光标当前位置，在此模式下用户可以输入命令来进行文件存盘、移动光标、删除字符、撤消命令和重复命令等操作，还可以设置编辑环境。

```
this is the command mode.
~                                                                                  
~   表示没有内容                                                                                          
"myfile" 1L, 26C                                         1,25         全部
```

**第二种：编辑模式**，又叫输入模式。在输入模式下，屏幕的左下方会出现INSERT (插入)字样。在输入状态下，用户可以输入文本的内容。

```
this is the command mode.
~                                                                                  
~                                                                                  
~                                                                                  
~                                                                                  
-- 插入 --                                                       1,25         全部
```

**第三种：末行模式**。只要在命令模式下输入命令“：”即可进入末行模式。在末行模式下，可以进行保存文件、退出vi、进行查找和替换等操作。

```
this is the command mode.
~                                                                                  
~                                                                                  
~                                                                                  
~                                                                                  
:q!                                                    
```

三种模式介绍完了，我们看下vim的使用，这里面我们还是按照三种模式来对vim的使用进行说明

**命令模式可以使用的按键说明**

 **光标控制按键**

| h 或 向左箭头键(←) | 光标向左移动一个字符                                         |
| ------------------ | ------------------------------------------------------------ |
| j 或 向下箭头键(↓) | 光标向下移动一个字符                                         |
| k 或 向上箭头键(↑) | 光标向上移动一个字符                                         |
| l 或 向右箭头键(→) | 光标向右移动一个字符                                         |
| 15j/15↓            | 向下移动15行                                                 |
| [Ctrl] + [f]       | 屏幕『向下』移动一页，相当于 [Page Down]按键 (常用)          |
| [Ctrl] + [b]       | 屏幕『向上』移动一页，相当于 [Page Up] 按键 (常用)           |
| [Ctrl] + [d]       | 屏幕『向下』移动半页                                         |
| [Ctrl] + [u]       | 屏幕『向上』移动半页                                         |
| n                  | 那个 n 表示『数字』，例如 3 。按下数字后再按空格键，光标会向右移动3 个字符。 |
| 0 或功能键[Home]   | 这是数字『 0 』：移动到这一行的最前面字符处 (常用)           |
| $ 或功能键[End]    | 移动到这一行的最后面字符处(常用)                             |
| H                  | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
| M                  | 光标移动到这个屏幕的中央那一行的第一个字符                   |
| L                  | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
| G                  | 移动到这个文件的最后一行(常用)                               |
| nG                 | n 为数字。移动到这个文件的第 n 行。可配合 :set nu            |
| gg                 | 移动到这个档案的第一行，相当于 1G (常用)                     |
| n                  | n 为数字。光标向下移动 n 行(常用)                            |

 **搜索与替换**

| /abc                    | 向光标之下查找一个名称为 abc 的字符串。 (常用)               |
| ----------------------- | ------------------------------------------------------------ |
| ?abc                    | 向光标之上查找一个字符串名称为 abc 的字符串。                |
| n                       | 这个 n 是英文按键。代表『重复前一个查找的动作』。            |
| N                       | 这个 N 是英文按键。与 n 刚好相反                             |
| **:n1,n2s/abc1/abc2/g** | **n1 与 n2 为数字。在第 n1 与 n2 行之间查找 abc1 替换为 abc2** |
| :1,$s/abc1/abc2/g       | 从第一行到最后一行查找 abc1 字符串，并将该字符串替换为 abc2 (常用) |
| :1,$s/abc1/abc2/gc      | 从第一行到最后一行查找 abc1 字符串，并将该字符串替换为 abc2 ,且在替换前显示提示字符给用户确认 |

 **删除与复制粘贴**

| x, X     | x 相当于 [del] ， X 相当于 [backspace] (常用)                |
| -------- | ------------------------------------------------------------ |
| nx       | n 为数字，连续向后删除 n 个字符。                            |
| **dd**   | **删除光标所在的那一整行(常用)**                             |
| ndd      | n 为数字。删除光标所在的向下 n 行(常用)                      |
| d1G      | 删除光标所在行到第一行的所有数据                             |
| dG       | 删除光标所在行到最后一行的所有数据                           |
| d$       | 删除光标所在处，到该行的最后一个字符                         |
| d0       | 那个是数字的 0 ，删除光标所在处，到该行的最前面一个字符      |
| yy       | 复制光标所在的那一行(常用)                                   |
| nyy      | n 为数字。(常用)                                             |
| y1G      | 复制光标所在行到第一行的所有数据                             |
| yG       | 复制光标所在行到最后一行的所有数据                           |
| y0       | 复制光标所在的那个字符到该行行首的所有数据                   |
| y$       | 复制光标所在的那个字符到该行行尾的所有数据                   |
| p, P     | p 为将已复制的数据在光标下一行贴上，P 则为贴在光标上一行 (常用) |
| J        | 将光标所在行与下一行的数据结合成同一行                       |
| c        | 重复删除多个数据，例如向下删除 4 行，[ 4cj ]，配合上下左右的按键使用 |
| u        | 撤销操作。(常用)                                             |
| [Ctrl]+r | 重做上一个动作。(常用)                                       |

**从命令模式进入输入模式**

| i, I  | i=从当前光标所在处插入， I =在当前所在行的第一个非空处开始插入。 (常用) |
| ----- | ------------------------------------------------------------ |
| a, A  | a =从当前光标所在的下一个字符处开始插入， A =从光标所在行的最后一个字符处开始插入。(常用) |
| o, O  | o =在当前光标所在的下一行处插入新的一行； O =在当前光标所在处的上一行插入新的一行。(常用) |
| r, R  | r 只会取代光标所在的那一个字符一次；R会一直取代光标所在的文字，直到按下 ESC 为止；(常用) |
| [Esc] | 退出输入模式，回到命令模式中(常用)                           |

**从命令模式进入到末行模式**

| :w                  | 保存(常用)                                                   |
| ------------------- | ------------------------------------------------------------ |
| :w!                 | 若文件属性为『只读』时，强制保存，是否能保存与权限有关       |
| :q                  | 不保存退出(常用)                                             |
| :q!                 | 强制退出不保存。                                             |
| :wq                 | 保存退出， :wq! 则为强制保存退出 (常用)                      |
| ZZ                  | 这是大写的 Z ！若档案没有更动，则不储存离开，若档案已经被更动过，则储存后离开！ |
| :w [filename]       | 将编辑的数据储存成另一个档案（类似另存新档）                 |
| :r [filename]       | 在编辑的数据中，从指定的文件读取数据并加到光标所在行后面     |
| :n1,n2 w [filename] | 将 n1 到 n2 的内容保存为 filename 这个档案。                 |
| :! command          | 在系统中执行指定的命令 如 :! ls /home                        |
| vim 环境的变更      |                                                              |
| **:set nu**         | **显示行号**                                                 |
| :set nonu           | 取消行号                                                     |

## 三、额外功能

**区块选择**

| v        | 字符选择，选中光标经过的地方 |
| -------- | ---------------------------- |
| V        | 选中光标经过的行             |
| [Ctrl]+v | 区块选择                     |
| y        | 复制选中的部分               |
| d        | 删除选中的部分               |

**多文件编辑**

| :n     | 编辑下一个文件                    |
| ------ | --------------------------------- |
| :N     | 编辑上一个文件                    |
| :files | 列出目前这个 vim 的开启的所有文件 |

**多窗口编辑**

| :sp/:vsp [filename]    | 开启一个新窗口，如果加 filename， 表示在新窗口编辑指定的文件，否则表示两个窗口为同一个文件(同步显示)。 |
| ---------------------- | ------------------------------------------------------------ |
| [ctrl]+w+ j [ctrl]+w+↓ | 按键的按法是：先按下 [ctrl] 不放， 再按下 w 后放开所有的按键，然后再按下 j (或向下箭头键)，则光标可移动到下方的窗口。 |
| [ctrl]+w+ k [ctrl]+w+↑ | 同上，不过光标移动到上面的窗口。                             |
| [ctrl]+w+ q            | 退出光标所在窗口，也可以 [ctrl]+w+j/k 切换窗口后，按下 :q 即可离开， 也可以按下 [ctrl]+w+q 。 |

**环境变量与记录**

.viminfo:记录用户的行为，之前编辑过的文件光标在什么位置，在这个文件中进行过什么操作等，自动建立

.vimrc：定义vim的默认设置，如是否显示行号等，需要手动生成

| :set nu /:set nonu                | 就是设定与取消行号！                                         |
| --------------------------------- | ------------------------------------------------------------ |
| :set hlsearch /:set nohlsearch    | 搜索时是否高亮显示。默认值是 hlsearch                        |
| :set autoindent :set noautoindent | 是否自动缩排？autoindent 就是自动缩排。                      |
| :set backup/:set nobackup         | 是否自动备份，一般是 nobackup 的， 如果设定 backup 的话，那么当你更动任何一个档案时，则源文件会被另存成一个档名为 filename~ 的档案。 |
| :set ruler/:set noruler           | 是否显示右下角的一些状态栏说明                               |
| :set showmode/:set noshowmode     | 是否显示左下角的状态栏。                                     |
| :set backspace=(012)              | 一般来说， 如果我们按下 i 进入编辑模式后，可以利用backspace来删除任意字符的。 但是，某些版本则不许如此。这时就可以使用这个设置2 可以删除任意；0 或 1 仅可删除刚刚输入内容 |
| :set all                          | 显示目前所有的环境变量设定值。                               |
| :set                              | 显示与系统默认值不同的设置， 用户修改过的                    |
| :syntax on :syntax off            | 是否显示颜色                                                 |
| :set bg=dark :set bg=light        | 可用以显示不同的颜色色调，预设是『 light 』。如果你常常发现批注的字体深蓝色实在很不容易看， 那么这里可以设定为 dark 喔！试看看，会有不同的样式呢！ |