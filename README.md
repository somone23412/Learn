# Vim-Learn
Vim introductory learning notes, including the use of plug-ins
# Vim入门教程笔记

## 基本命令

### 1.编辑模式

+ 进入编辑模式：键盘按键 "**i**"
+ 退出编辑模式：键盘按键 "**esc**"

### 2.保存退出

```sh
:wq #保存退出
:x #同
```
":" 表示进入命令模式

## 基本配置

### 1.代码高亮

```vim
:syntax on #代码高亮
```

### 2.显示行数

```vim
:set number #显示行数
```

### 3.保存配置

```sh
vim ~/.vimrc #编辑vim配置文件
```

在配置文件里把需要的命令打进去，不需要添加 ":"，保存后再使用vim时就会应用这些设置

```sh
vim ~/.vimrc #编辑vim配置文件
```

比如文件内容：

```sh
quyan@new1022-2:~$ cat ~/.vimrc
syntax on
set number
```

再次用vim打开文件时就会应用这些设置：

```sh
quyan@new1022-2:~$ vim Documents/VimLx/helloworld.cpp 
```

```cpp
  1 #include <iostream>
  2 
  3 int main(){
  4     std::cout<<"hello world!"<<std::endl;
  5     return 0;
  6 }
```

vimrc可以十分复杂，甚至上千行；GitHub上有很多配置好的 .vimrc
+ 如[vimrc](https://github.com/amix/vimrc/tree/master/vimrcs)

## 光标移动

### 1.单格移动

+ "↑" : 键盘 "**H**" 或 "**↑**"
+ "↓" : 键盘 "**J**" 或 "**↓**"
+ "←" : 键盘 "**K**" 或 "**←**"
+ "→" : 键盘 "**L**" 或 "**→**"

### 2.单词间移动

+ 单词间前跳动 : 键盘 "**w**"
+ 单词间后跳动 : 键盘 "**b**"
+ 或"**shift**+**←**/**→**"

### 3.翻页

+ 上下翻页 : 键盘 "**PageDn**/**Up**" 或 "**Ctrl** + **f**/**b**"
+ 或"**shift**+**↑**/**↓**"

### 4.跳到指定行

+ 在键盘上输入"**行号**+**gg**"
如要跳到88行，则敲"**88gg**"，就会跳到88行

### 5.在指定方向跳指定行

+ 在键盘上输入"**行数**+**方向**"
如要向下跳10行，则敲"**10j**"，这里的方向为上面介绍的hjkl所指定的方向

### 6.查找指定内容

+ 在键盘上输入"**/**+**查找内容**"，通过键盘输入"**n**或**shift**+**n**"来切换上一个和下一个匹配的段落
如我要查找“map”，则敲"**/map**"回车

## 基础文本编辑

### 1.剪切

+ 剪切**整行**并进入编辑模式：键盘输入"**cc**"
+ 剪切**连续n行**并进入编辑模式：键盘输入"**cnc**"或"**ncc**"
比如要剪切连续的两行就敲"**c2c**"
+ 不进入编辑模式：把"c"换成"**d**"

### 2.复制

+ 把“剪切”的所有"c"换成"**y**"

### 3.粘贴

+ 键盘"**p**"

### 4.撤销操作

+ 键盘"**u**"

### 5.拖选模式

+ 键盘"**v**"进入拖选，按"**c**"剪切所选内容，其它操作类推

## 代码补全

### 1.基础补全

+ 键盘敲“**Ctrl**+**n**”可以补全**输入过的**单词

### 2.第三方插件

#### 1.插件管理工具vim-plug
+ [vim-plug](https://github.com/junegunn/vim-plug)

##### 安装：

```sh
quyan@new1022-2:~$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

~目录下出现.vim隐藏文件夹，安装成功

##### 使用方法：

Add a vim-plug section to your `~/.vimrc` (or `~/.config/nvim/init.vim` for Neovim):

1. Begin the section with `call plug#begin()`
1. List the plugins with `Plug` commands
1. `call plug#end()` to update `&runtimepath` and initialize plugin system
    - Automatically executes `filetype plugin indent on` and `syntax enable`.
      You can revert the settings after the call. e.g. `filetype indent off`, `syntax off`, etc.

##### 示例：

```vim
" Specify a directory for plugins
" - For Neovim: ~/.local/share/nvim/plugged
" - Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')

" Make sure you use single quotes

" Shorthand notation; fetches https://github.com/junegunn/vim-easy-align
Plug 'junegunn/vim-easy-align'

" Any valid git URL is allowed
Plug 'https://github.com/junegunn/vim-github-dashboard.git'

" Multiple Plug commands can be written in a single line using | separators
Plug 'SirVer/ultisnips' | Plug 'honza/vim-snippets'

" On-demand loading
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
Plug 'tpope/vim-fireplace', { 'for': 'clojure' }

" Using a non-master branch
Plug 'rdnetto/YCM-Generator', { 'branch': 'stable' }

" Using a tagged release; wildcard allowed (requires git 1.9.2 or above)
Plug 'fatih/vim-go', { 'tag': '*' }

" Plugin options
Plug 'nsf/gocode', { 'tag': 'v.20150303', 'rtp': 'vim' }

" Plugin outside ~/.vim/plugged with post-update hook
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }

" Unmanaged plugin (manually installed and updated)
Plug '~/my-prototype-plugin'

" Initialize plugin system
call plug#end()
```

Reload .vimrc and `:PlugInstall` to install plugins.

#### 2.插件示例

+ 可以在[VimAwesome](https://vimawesome.com)上找到很多好的插件
比如要安装[NERD tree](https://vimawesome.com/plugin/nerdtree-red)，选择“VimPlug”，可以看到如下内容：

*Place this in your .vimrc:*

>*Plug 'scrooloose/nerdtree'*

*… then run the following in Vim:*

>*:source %*
>*:PlugInstall*

##### 安装

在~/.vimrc文件末尾添加如下代码：

```vim
 385 call plug#begin('~/.vim/plugged')
 386     Plug 'scrooloose/nerdtree'
 387 call plug#end()
```

在命令模式下输入：

```vim
:PlugInstall
```

显示“Finishing...Done!”安装成功；安装过程需要用到**git**，如果没有预先安装git的话会安装失败

##### 使用NERDTree

+ 使用的说明一般都在插件对应的GitHub上

查看帮助文档：

```vim
:help NERDTree.txt
```

在文档中看到Global command： **NERDTree**，看一下效果

```vim
:NERDTree
```

![a0e38790b3cf30828735e32f3235f632.png](en-resource://database/5893:0)

可以看到树形结构的文件列表，回车可以进入查看文件
“**Ctrl**+**w**”后按”**左/右**“键可以在两个窗口之间切换

想要NERDTree**自启动**可以在.vimrc文件中添加

```vim
autocmd VimEnter * NERDTree
```

#### 3.卸载插件

```vim
:PlugClean
```

---

具体的自动补全插件的安装方法同上
