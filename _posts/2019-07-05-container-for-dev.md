---
title: 对于容器技术应用于开发的一点思考
layout: single
guid: 4ec6f15ab2424453bd14df4f3ec46d85
date: 2019-07-05 11:58:52
categories:
  - 杂谈
tags:
  - 容器
---

以[Docker][]为代表的容器技术早已成为热门，按说也没什么可以讨论的。但我们一直还没用上容器技术，为什么呢？是不是容器技术真的不适合我们呢？这倒值得探讨一下。

传统上，容器技术更多用于[DevOps][]，利用其实现快速交付的能力，实现快速部署、方便的迁移和故障恢复，虚拟化技术也能顺便节省一点资源。而我们的应用不追求或者说对快速部署的追求没有这么高，也不太需要节省资源，应用都是直接运行在物理机上，[RPO][]为0的高可用目标也不是通过容器集群方式提供。所以我们传统上对容器没有太多想法，也没有用到容器。

但我们是不是真的用不上容器技术呢？我觉得不是的。我觉得对我们来说，容器技术至少能在两方面发挥作用：

1.  统一构建环境。

    我们主要在Linux下进行开发，更具体点，主要支持[红帽RHEL][]。在构建过程中，我们要保证系统用到的库文件、编译器版本都和生产一致。而难免有些库文件或公共组件存在多个版本，我们在不同分支上可能需要使用不同的版本，所以不适合直接安装到系统中。在这时候，一个配置好编译器、各种开发库的容器就是一个很好的选择。无论我们用的是什么环境，甚至可以选择其他更顺手的Linux版本，编译出来的就是我们想要的组合。

2.  统一开发环境。

    现代的开发环境除了大型的IDE外，还有[VSCode][]、[Vim][]之类比较轻量的开源环境可供选择，特别是在越来越重视正版化的现在，又贵又笨重的IDE正在逐渐淡出。特别在微软推出[LSP][]（Language Server Protocol）之后，上下文补全、跳转、重构等功能不再是IDE的专利，各种轻量级环境配合LSP也能满足日常的需要。

    轻量级的开发环境一般都会提供多种多样的插件，配合合适的插件、配合Language Server变成一个好用的环境。在这过程中，免不了要安装各种支持工具（[ctags][]、[gtags][]、[fzf][]），可能还要安装Python、Language Server等。如果每个人都自己研究一番，未免效率太低了。另外，随着大家对安全的看中，不少单位都实行内外网隔离，在开发网无法直接访问互联网，安装第三方工具变得相当麻烦。因此可以把配置好的开发环境作为一个容器，把用到的工具、软件、插件全部封装到容器内，实现一次配置，到处使用，大大提高开发效率。

    可能有童鞋会觉得容器是不是只能封装Vim、Emacs之类基于终端的工具呢？其实不是的，只要在宿主机上装一个XServer就可以运行。[这里][jessfraz]能找到一些例子。

容器能干很多事情。不过对于还在RHEL 6上工作的我们来说，要支持Docker还有那么一点麻烦，还是期待晚一点转移到RHEL 7的时候好好用用Docker吧。

[Docker]: https://www.docker.com/
[DevOps]: https://en.wikipedia.org/wiki/DevOps
[RPO]: https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective
[红帽RHEL]: https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux
[VSCode]: https://code.visualstudio.com/
[Vim]: https://www.vim.org/
[LSP]: https://microsoft.github.io/language-server-protocol/
[ctags]: http://ctags.sourceforge.net/
[gtags]: https://www.gnu.org/software/global/
[fzf]: https://github.com/junegunn/fzf
[jessfraz]: https://blog.jessfraz.com/post/docker-containers-on-the-desktop/
