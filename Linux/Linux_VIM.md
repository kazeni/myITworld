# VIM 

Vim是从vi发展出来的一个文本编辑器。代码补全、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。和Emacs并列成为类Unix系统用户最喜欢的编辑器。

文档参见：http://vimdoc.sourceforge.net/htmldoc/

中文文档：http://vimcdoc.sourceforge.net/doc/



![](/Users/boycezhang/Documents/GitHub/myITworld/Linux/image/vim-key.png)



查看帮助：`:help`，`]` 进入标签，`O` 返回。

## 常用快捷操作

Y： 复制

P： 黏贴

D： 删除行

gg：到文件头部。

G：到文件尾部。

o： 插入新行

A: 移光标至行末

:nohl 取消高亮

同时打开多个窗口

```shell
vim -p main.cpp my-oj-toolkit.h /private/etc/hosts
```

可以设置更通用的切换标签页快捷键，`~/.vimrc`是这样设置的:

```shell
noremap <C-L> <Esc>:tabnext<CR>
noremap <C-H> <Esc>:tabprevious<CR>
```

需要切换目录时，把VIM置于后台，`Ctrl+Z`，需要切换回来时输入`jobs`，然后再输入`fg 序号`，即可切换回来



## 初始化过程

正如多数 linux 软件，vim 的配置文件分为系统配置文件 `/etc/vimrc`，`/usr/share/vim/` 和用户配置文件 `~/.vimrc`，`~/.vim/`。

vim 的配置文件载入过程为：

1. `/etc/vimrc`
2. `$HOME/.vim/`，`$HOME/.vimrc`
3. `$VIMRUNTIME/.vim`，`$VIMRUNTIME/.vimrc`
4. `$HOME/.vim/after/`

> 通过运行 `vim -V` 可查看整个初始化过程。



如果不知道$HOME或者$VIM具体是哪个目录，可以在vim中用下面的命令查看：

```shell
:echo $VIM
:echo $HOME 
```









## 示例配置文件

一个示例配置文件如下：

```shell
" .vimrc
" See: http://vimdoc.sourceforge.net/htmldoc/options.html for details

" For multi-byte character support (CJK support, for example):
" set fileencodings=ucs-bom,utf-8,cp936,big5,euc-jp,euc-kr,gb18030,latin1
       
set tabstop=4       " Number of spaces that a <Tab> in the file counts for.
 
set shiftwidth=4    " Number of spaces to use for each step of (auto)indent.
 
set expandtab       " Use the appropriate number of spaces to insert a <Tab>.
                    " Spaces are used in indents with the '>' and '<' commands
                    " and when 'autoindent' is on. To insert a real tab when
                    " 'expandtab' is on, use CTRL-V <Tab>.
 
set smarttab        " When on, a <Tab> in front of a line inserts blanks
                    " according to 'shiftwidth'. 'tabstop' is used in other
                    " places. A <BS> will delete a 'shiftwidth' worth of space
                    " at the start of the line.
 
set showcmd         " Show (partial) command in status line.

set number          " Show line numbers.

set showmatch       " When a bracket is inserted, briefly jump to the matching
                    " one. The jump is only done if the match can be seen on the
                    " screen. The time to show the match can be set with
                    " 'matchtime'.
 
set hlsearch        " When there is a previous search pattern, highlight all
                    " its matches.
 
set incsearch       " While typing a search command, show immediately where the
                    " so far typed pattern matches.
 
set ignorecase      " Ignore case in search patterns.
 
set smartcase       " Override the 'ignorecase' option if the search pattern
                    " contains upper case characters.
 
set backspace=2     " Influences the working of <BS>, <Del>, CTRL-W
                    " and CTRL-U in Insert mode. This is a list of items,
                    " separated by commas. Each item allows a way to backspace
                    " over something.
 
set autoindent      " Copy indent from current line when starting a new line
                    " (typing <CR> in Insert mode or when using the "o" or "O"
                    " command).
 
set textwidth=79    " Maximum width of text that is being inserted. A longer
                    " line will be broken after white space to get this width.
 
set formatoptions=c,q,r,t " This is a sequence of letters which describes how
                    " automatic formatting is to be done.
                    "
                    " letter    meaning when present in 'formatoptions'
                    " ------    ---------------------------------------
                    " c         Auto-wrap comments using textwidth, inserting
                    "           the current comment leader automatically.
                    " q         Allow formatting of comments with "gq".
                    " r         Automatically insert the current comment leader
                    "           after hitting <Enter> in Insert mode. 
                    " t         Auto-wrap text using textwidth (does not apply
                    "           to comments)
 
set ruler           " Show the line and column number of the cursor position,
                    " separated by a comma.
 
set background=dark " When set to "dark", Vim will try to use colors that look
                    " good on a dark background. When set to "light", Vim will
                    " try to use colors that look good on a light background.
                    " Any other value is illegal.
 
set mouse=a         " Enable the use of the mouse.
 
filetype plugin indent on
syntax on
```

***

## 基本操作

### 光标

#### 字符移动

在Vim的Normal模式里（如果你在Visual模式或者Insert模式，可以按``回到Normal模式）， 通过`h`, `j`, `k`, `l`, `i`来进行左下上右的光标移动。

在Vim中多数操作都支持数字前缀，比如`10j`可以向下移动10行。

#### 单词移动

多数情况下单词移动比字符移动更加高效。 `w`移动光标到下一个单词的词首，`b`移动光标到上一个单词的词首；`e`移动光标到下一个单词的结尾，`ge`移动光标到上一个单词的结尾。

> 单词移动同样支持数字前缀，比如`4w`可以向后移动4个单词。连续的标点符号算一个单词。
>
> 有趣的是，`W`, `B`, `E`具有同样的功能，只不过它是用空格来分隔单词的，可以跳地更远~

`^`到行首，`$`到行尾。

> 拷贝一行：`^y$`。

#### 相对屏幕移动

通过`c-f`向下翻页，`c-b`向上翻页；`c-e`逐行下滚，`c-y`逐行上滚。这在几乎所有Unix软件中都是好使的，比如`man`和`less`。 `H`可以移动到屏幕的首行，`L`到屏幕尾行，`M`到屏幕中间。

`zt`可以置顶当前行，通常用来查看完整的下文，比如函数、类的定义。 `zz`将当前行移到屏幕中部，`zb`移到底部。

#### 文件中移动

通过`:10`可以直接移动光标到文件第10行。如果你看不到行号，可以`:set number`。 `gg`移到文件首行，`G`移到尾行。

> 拷贝整个文件：`ggyG`。

`/xx`可以查找某个单词`xx`，`n`查找下一个，`N`查找上一个。 在光标跳转之后，可以通过`c-o`返回上一个光标位置，`c-i`跳到下一个光标位置。

> `?xx`可以反向查找，`q/`, `q?`可以列出查找历史。





## 查找

在normal模式下按下`/`即可进入查找模式，输入要查找的字符串并按下回车。 Vim会跳转到第一个匹配。按下`n`查找下一个，按下`N`查找上一个。

Vim查找支持正则表达式，例如`/vim$`匹配行尾的`"vim"`。 需要查找特殊字符需要转义，例如`/vim\$`匹配`"vim$"`。

> 注意查找回车应当用`\n`，而替换为回车应当用`\r`（相当于``）。

### 大小写敏感查找

在查找模式中加入`\c`表示大小写不敏感查找，`\C`表示大小写敏感查找。例如：

```bash
/foo\c
```

将会查找所有的`"foo"`,`"FOO"`,`"Foo"`等字符串。

### 大小写敏感配置

Vim 默认采用大小写敏感的查找，为了方便我们常常将其配置为大小写不敏感：

```shell
" 设置默认进行大小写不敏感查找
set ignorecase
" 如果有一个大写字母，则切换到大小写敏感查找
set smartcase 
```

> 将上述设置粘贴到你的`~/.vimrc`，重新打开Vim即可生效。

### 查找当前单词

在normal模式下按下`*`即可查找光标所在单词（word）， 要求每次出现的前后为空白字符或标点符号。例如当前为`foo`， 可以匹配`foo bar`中的`foo`，但不可匹配`foobar`中的`foo`。 这在查找函数名、变量名时非常有用。

按下`g*`即可查找光标所在单词的字符序列，每次出现前后字符无要求。 即`foo bar`和`foobar`中的`foo`均可被匹配到。

### 查找与替换

`:s`（substitute）命令用来查找和替换字符串。语法如下：

```shell
:{作用范围}s/{目标}/{替换}/{替换标志}
```

例如`:%s/foo/bar/g`会在全局范围(`%`)查找`foo`并替换为`bar`，所有出现都会被替换（`g`）。

### 作用范围

作用范围分为当前行、全文、选区等等。

当前行：

```shell
:s/foo/bar/g
```

全文：

```shell
:%s/foo/bar/g
```

选区，在Visual模式下选择区域后输入`:`，Vim即可自动补全为 `:'<,'>`。

```shell
:'<,'>s/foo/bar/g
```

2-11行：

```shell
:5,12s/foo/bar/g
```

当前行`.`与接下来两行`+2`：

```shell
:.,+2s/foo/bar/g
```

### 替换标志

上文中命令结尾的`g`即是替换标志之一，表示全局`global`替换（即替换目标的所有出现）。 还有很多其他有用的替换标志：

空替换标志表示只替换从光标位置开始，目标的第一次出现：

```shell
:%s/foo/bar
```

`i`表示大小写不敏感查找，`I`表示大小写敏感：

```shell
:%s/foo/bar/i
# 等效于模式中的\c（不敏感）或\C（敏感）
:%s/foo\c/bar
```

`c`表示需要确认，例如全局查找`"foo"`替换为`"bar"`并且需要确认：

```shell
:%s/foo/bar/gc
```

回车后Vim会将光标移动到每一次`"foo"`出现的位置，并提示

```shell
replace with bar (y/n/a/q/l/^E/^Y)?
```

按下`y`表示替换，`n`表示不替换，`a`表示替换所有，`q`表示退出查找模式， `l`表示替换当前位置并退出。`^E`与`^Y`是光标移动快捷键，参考： [Vim中如何快速进行光标移动](http://harttle.com/2015/11/07/vim-cursor.html)。

### 高亮设置

### 高亮颜色设置

如果你像我一样觉得高亮的颜色不太舒服，也可以进行设置：

```shell
highlight Search ctermbg=grey ctermfg=black 
```

> 将上述配置粘贴到`~/.vimrc`，重新打开vim即可生效。

上述配置指定Search结果的前景色（foreground）为黑色，背景色（background）为灰色。 更多的CTERM颜色可以查阅：[http://vim.wikia.com/wiki/Xterm256_color_names_for_console_Vim](http://vim.wikia.com/wiki/Xterm256_color_names_for_console_Vim)

### 禁用/启用高亮

有木有觉得每次查找替换后Vim仍然高亮着搜索结果？ 可以手动让它停止高亮，在normal模式下输入：

```shell
:nohighlight
" 等效于
:nohl
```

其实上述命令禁用了所有高亮，正确的命令是`:set nohlsearch`。 下次搜索时需要`:set hlsearch`再次启动搜索高亮。 怎么能够让Vim查找/替换后自动取消高亮，下次查找时再自动开启呢？

```shell
" 当光标一段时间保持不动了，就禁用高亮
autocmd cursorhold * set nohlsearch
" 当输入查找命令时，再启用高亮
noremap n :set hlsearch<cr>n
noremap N :set hlsearch<cr>N
noremap / :set hlsearch<cr>/
noremap ? :set hlsearch<cr>?
noremap * *:set hlsearch<cr>
```

> 将上述配置粘贴到`~/.vimrc`，重新打开vim即可生效。













