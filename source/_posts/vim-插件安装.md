---
title: vim 插件安装（未完成挖坑）
date: 2021-10-11 21:12:13
categories: 折腾
tags:
---

因为要写 verilog 所以让我们升级一下 vim 来更好的打代码……说来惭愧这么多年我真的使用 vim 都是很浅薄的水准，我谢罪。

有用包管理器的方法，不过我们先原始一下……如果你要安装一个名为 foo 的插件

```bash
mkdir -p ~/.vim/pack/foo
cd ~/.vim/pack/foo
unzip /tmp/foopack.zip
```

其中 mkdir -p 表示创建 parent 目录如果需要的话，若是存在也不报错

然后 vim 将会在 run 的开始检测 pack/*/start 中的 plugin，然后将它们加入 runtimepath 中，现在的目录看起来会是这样

```bash
pack/foo/README.txt
pack/foo/start/foobar/plugin/foo.vim
pack/foo/start/foobar/syntax/some.vim
pack/foo/opt/foodebug/plugin/debugger.vim
```

若是在 pack/foo/opt 中，则需要输入 :packadd foo 来 enable plugin。



##### 现在来实战一下

听说 YouCompleteMe 这个挺不错的，我们按照它安装一下。这个并不是 follow manual install，so，学习另一个方式也挺好。

先是需要 Vundle 来安装

`sudo git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim`

创建并填写 ~/.vimrc 使用它提供的配置

```bash
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
Plugin 'tpope/vim-fugitive'
" plugin from http://vim-scripts.org/vim/scripts.html
" Plugin 'L9'
" Git plugin not hosted on GitHub
Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Install L9 and avoid a Naming conflict if you've already installed a
" different version somewhere else.
" Plugin 'ascenator/L9', {'name': 'newL9'}

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
```

disable 掉不需要的 plugin，然后在这个位置填写 ycm-core/YouCompleteMe，就是除了 Plugin 'VundleVim/Vundle.vim' 剩下的全部涂掉。出现了权限问题，记得修改整个 .vim 文件夹到 777。然后要重新启动配置文件不然不起作用 :source % 或者 :so %



我放弃了，这个牵扯的实在太多了



plus: 上面修改的是个人的设置，我在 /etc/vim/.vimrc 上进行了一些小修改，看看就知道了。



ref：

https://github.com/vim/vim/blob/03c3bd9fd094c1aede2e8fe3ad8fd25b9f033053/runtime/doc/repeat.txt#L515

https://opensource.com/article/20/2/how-install-vim-plugins

https://github.com/ycm-core/YouCompleteMe#linux-64-bit

https://github.com/VundleVim/Vundle.vim#about

https://stackoverflow.com/questions/37618447/vundle-does-not-install-vim-plugins
