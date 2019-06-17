---
title: 为ConEmu下的命令行版本Vim配置SpaceVim的方法
layout: single
guid: f5dd6f842c3e4ec0abb1b8eaae2e4e8a
date: 2019-06-17 18:37:51
categories:
  - 软件
tags:
  - Vim
  - ConEmu
  - SpaceVim
---

[ConEmu][]是我目前正在使用的Windows下终端封装程序（实际用的是[Cmder][]），配合[mintty][]、[Git Bash][]等，无论是通过ssh远程登录到服务器还是使用本地cmd/bash终端都非常方便。但缺省情况下，Vim在ConEmu中能用，但颜色不是很好看，和Linux/MacOS下的Vim形象相去甚远，所以基本上很少在Windows的命令行下直接使用Vim，更多的是使用Gvim/Neovim。为了改进这点，特地搜索了正确的配置方法，让Vim在ConEmu下也能顺畅地使用，方便在ConEmu命令行下直接用Vim编辑文本而不用切换到独立的程序中。

根据[ConEmu文档][VimXterm]，需要满足以下条件：

1. 只支持部分版本。如Vim官方发布的``gvim##.exe``和``vim##w32.zip``中的版本。Vim 7.x、8.x都可以；
1. Vim的可执行程序必须名为``vim.exe``；
1. ConEmu配置中必须配置几个选项：
   - 选中``Features``配置页面中的``Inject ConEmuHk``和``ANSI X3.64 / xterm 256 colors``（在我用的版本中没找到后者）；
   - 选中``Colors``配置页面中的``TrueMod (24bit color) support``；
1. 使用一个支持``Xterm color``的配色方案，如``zenburn``/``onedark``。
1. 配置Vim

   * 对于普通Vim配置，可以在Vim配置中加上：
 
     ```vim
     if &term=='win32' && !empty($ConEmuANSI)
         set termencoding=utf8
         set term=xterm
         set t_Co=256
         set notermguicolors
         let &t_AB="\e[48;5;%dm"
         let &t_AF="\e[38;5;%dm"
         colorscheme zenburn
     endif
     ```
 
   * 由于我使用的是[SpaceVim][]，因此在自己的``myspacevim##before()``中加入：
 
     ```vim
     if &term=='win32' && !empty($ConEmuANSI)
         set termencoding=utf8
         set term=xterm
         set t_Co=256
         let &t_AB="\e[48;5;%dm"
         let &t_AF="\e[38;5;%dm"
         let g:spacevim_enable_guicolors=0
         let g:spacevim_colorscheme_default="zenburn"
     endif
     ```
   
     由于使用了SpaceVim，因此有两个配置改为修改SpaceVim配置变量而不是直接改动Vim的设置，避免被SpaceVim覆盖。

1. 在运行Vim时，指定终端编码为UTF-8:

   ```sh
   chcp 65001
   vim
   ```

通过上述配置，实测zenburn/onedark/monokai/molokai等配色方案都能正常显示，但[NeoSolarized][]显示不正常。

[Cmder]: https://cmder.net/
[mintty]: https://mintty.github.io/
[VimXterm]: https://conemu.github.io/en/VimXterm.html
[SpaceVim]: https://spacevim.org/
[NeoSolarized]: https://github.com/icymind/NeoSolarized
[ConEmu]: https://conemu.github.io
[Git Bash]: https://gitforwindows.org/
