---
title: 关于Bash的内置getopts命令
layout: single
guid: 3418c039672e4b26acafdfb1cae00dfa
date: 2019-08-03 16:24:55
categories:
  - 软件
tags:
  - shell
---

一直以来，我都是用``getopt``为shell脚本进行命令行参数解析，一路也都工作得很好。直到我遇上了``MacOS``。``getopt``是一个单独的命令，在``MacOS``上的版本和``Linux``上的不同，用法并不兼容，导致命令行脚本在``MacOS``下工作不正常。

为了解决问题，在网上搜了相关问题，发现有两种主要解决办法：

1.  安装并使用gnu版本的``getopt``

    ```sh
    brew install gnu-getopt
    ```

    在安装后，可以：

    * 用gnu版本代替内置版本
    
      ```sh
      brew link --force gnu-getopt
      ```
    
    * 使用时指定``getopt``的绝对路径，如``$(brew --prefix gnu-getopt)/bin/getopt``。主要问题是这样就要求在脚本中指定``getopt``位置，在回到``Linux``时并不通用，达不到兼容的目的。

    * 通过环境变量``FLAGS_GETOPT_CMD``指定``getopt``位置，可以放到``~/.bashrc``、``~/.zshrc``之类的配置文件中。

      ```sh
      export FLAGS_GETOPT_CMD="$(brew --prefix gnu-getopt)/bin/getopt"
      ```

      这样的好处是在脚本中只要正常使用``getopt``即可，完美兼容``Linux``和``MacOS``。

2.  使用``bash``内置的``getopts``命令

    ``bash``内置了一个``getopts``命令，可以跨系统使用。但这个内置命令不支持长参数（``--long``），不如独立的``getopt``命令好用。然而``MacOS``中的``getopt``也是不支持长参数的，所以问题也不算大，起码达到了兼容的目标。

经过比较，出于提高脚本兼容性、减少对第三方包依赖的考虑，鉴于自己的脚本并不是那么复杂，并不需要很复杂的参数，使用``bash``内置的``getopts``命令也已经足够了，所以就把脚本中的``getopt``换成``getopts``，Perfect！

以下是我自己使用的[ultisnips][] snippet，方便复用。

```snippet
snippet getopts "bash builtin getopts" b
__ScriptVersion="${1:version}"

EchoUsage()
{
    echo "
Usage: $(basename "\$0") [options] [--]

    Options:
        -h|help       Display this message
        -V|version    Display script version
        -v|verbose    Display more verbose log
        -t|test <val> A testing argument
" >&2
}

VERBOSE=
while getopts ":hVvt:" opt; do
    case $opt in
        h|help)
            EchoUsage
            exit 0
            ;;
        V|version)
            echo "$${1:(basename $0)} -- Version $__ScriptVersion"
            exit 0
            ;;
        v|verbose)
            VERBOSE="${VERBOSE}1"
            ;;
        t|test)
            echo "Testing argument: '$OPTARG' at position $OPTIND"
            ;;
        * )
            echo -e "\n  Option does not exist : '$OPTARG' at position $OPTIND\n"
            EchoUsage
            exit 1
            ;;
    esac
done
shift $(($OPTIND-1))

$0
endsnippet
```

[ultisnips]: https://github.com/SirVer/ultisnips
