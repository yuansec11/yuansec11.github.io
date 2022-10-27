---
title: 学习笔记-makefile
date: 2022-10-17 09:38:00
categories: 学习笔记
tags:
- linux
- makefile
- 嵌入式
---
***
<!--more-->  
![](https://s1.ax1x.com/2022/10/17/xBXngS.png)
![](https://s1.ax1x.com/2022/10/17/xBXe9f.png)
![](https://s1.ax1x.com/2022/10/17/xBXENt.png)
![](https://s1.ax1x.com/2022/10/17/xBXV4P.png)
![](https://s1.ax1x.com/2022/10/17/xBXm38.png)
![](https://s1.ax1x.com/2022/10/17/xBXujg.png)
![](https://s1.ax1x.com/2022/10/17/xBXMuQ.png)
![](https://s1.ax1x.com/2022/10/17/xBXQBj.png)
![](https://s1.ax1x.com/2022/10/17/xBXlHs.png)
![](https://s1.ax1x.com/2022/10/17/xBX3En.png)
# makefile隐式规则
比如
```
foo:foo.o bar.o
    cc –o foo foo.o bar.o $(CFLAGS) $(LDFLAGS)
```

在这个里面Makefile 中并没有写下如何生成 foo.o 和 bar.o 这两目标的规则和命令。因为 make 的“隐含规则”功能会自动去推导这两个目标的依赖目标和生成命令。这里make调用的隐含规则是，把[.o]的目标的依赖文件置成[.c]，并使用 C 的编译命令“cc –c $(CFLAGS) [.c]”来生成[.o]的目标。省略了
```
foo.o:foo.c
    cc –c foo.c $(CFLAGS)
bar.o:bar.c
    cc –c bar.c $(CFLAGS)
```

因为，这已经是“约定”好了的事了，make 已经约定好了用 C 编译器“cc”生成[.o]文件的规则，这就是隐含规则。

# 命令变量
AR ：函数库打包程序。默认命令是“ar”。  
AS ：汇编语言编译程序。默认命令是“as”。  
CC ：C 语言编译程序。默认命令是“cc”。  
CXX： C++语言编译程序。默认命令是“g++”。  
CO：从 RCS 文件中扩展文件程序。默认命令是“co”。  
CPP：C 程序的预处理器（输出是标准输出设备）。默认命令是“$(CC) –E”。  
FC：Fortran 和 Ratfor 的编译器和预处理程序。默认命令是“f77”。  
GET：从 SCCS 文件中扩展文件的程序。默认命令是“get”。  
LEX：Lex 方法分析器程序（针对于 C 或 Ratfor）。默认命令是“lex”。  
PC：Pascal 语言编译程序。默认命令是“pc”。  
YACC：Yacc 文法分析器（针对于 C 程序）。默认命令是“yacc”。  
YACCR：Yacc 文法分析器（针对于 Ratfor 程序）。默认命令是“yacc –r”。  
MAKEINFO：转换 Texinfo 源文件（.texi）到 Info 文件程序。默认命令是“makeinfo”。  
TEX：从 TeX 源文件创建 TeXDVI 文件的程序。默认命令是“tex”。  
TEXI2DVI：从 Texinfo 源文件创建军 TeXDVI 文件的程序。默认命令  是“texi2dvi”。  
WEAVE：转换 Web 到 TeX 的程序。默认命令是“weave”。  
CWEAVE：转换 CWeb 到 TeX 的程序。默认命令是“cweave”。  
TANGLE：转换 Web 到 Pascal 语言的程序。默认命令是“tangle”。  
CTANGLE：转换 CWeb 到 C。默认命令是“ctangle”。  
RM：删除文件命令。默认命令是“rm–f”。  
