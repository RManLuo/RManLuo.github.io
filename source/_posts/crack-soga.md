---
title: How to crack a soga backend
date: 2021-02-07 01:29:24
tags:
	- go
	- 破解
	- 逆向
categories:
	- 笔记
cover:
mathjax: false
---

# How to crack a soga backend

Soga 后端破解思路小分享。

仅以此文记录和分享一些关于go应用破解的过程。由于本人也是第一次进行go程序的逆向，本文的分析会比较简单和基础。

# Soga

[soga 后端是一个同时支持 v2ray、Trojan、Shadowsocks 的后端，社区版最高支持88用户，优化了长时间运行的内存占用。](https://github.com/sprov065/soga)

本文以破解2.0.6为例，在release处可以下载指定版本的soga后端。

# 符号还原

Go二进制文件由于由于其自身恶心的机制导致对其直接在IDA中逆向十分困难。

>Go 语言的编译工具链会全静态链接构建二进制文件，把标准库函数和第三方 package 全部做了静态编译，再加上 Go 二进制文件中还打包进去了 runtime 和 GC(Garbage Collection，垃圾回收) 模块代码，所以即使做了 strip 处理( `go build -ldflags "-s -w"` )，生成的二进制文件体积仍然很大。在反汇编工具中打开 Go 语言二进制文件，可以看到里面包含动辄几千个函数。再加上 Go 语言的独特的函数调用约定、栈结构和多返回值机制，使得对 Go 二进制文件的分析，无论是静态逆向还是动态调式分析，都比分析普通的二进制程序要困难很多。
>
>不过，好消息是安全社区还是找到了针对性的方法，让安全分析人员对 Go 语言二进制文件的逆向变得更加轻松。最开始有人尝试过针对函数库做符号 Signature 来导入反汇编工具中，还原一部分二进制文件中的函数符号。后来有人研究出 Go 语言二进制文件内包含大量的运行时所需的符号和类型信息，以及字符串在 Go 二进制文件中独特的用法，然后开发出了针对性的符号甚至类型信息恢复工具。至此，对 Go 语言二进制文件的逆向分析工作，就变得轻松异常了。
>
>-https://www.anquanke.com/post/id/214940

因此为了方便我们进行分析，我在此处采用[go_parser](https://github.com/0xjiayu/go_parser/blob/master/README_cn.md)来对二进制文件进行信息还原。

```
file-->script file-->go_parser.py
```

此脚本能还原出go可执行文件的函数信息以及一些字符串，由于电脑崩溃此处没有保存还原之后的截图。

# 软件分析

在还原字符串以及函数信息之后，破解将十分容易。

我们首先搜索字符串，寻找验证有关的关键词。一把梭，直接查字符串，找到check。

![img](https://i.loli.net/2021/02/07/Xh8LtPsnioGwK21.png)

右键x-refer-to定位到check-soga函数。

![img](https://i.loli.net/2021/02/07/UWeh4GDrQFI3Y1A.png)

查看上层的startsogatask函数，分析函数，我们可得如下结果。

![QQ图片20210207014513](https://i.loli.net/2021/02/07/2ChfeHI51JZdvDP.png)

# 软件破解

简单的将check soga nop掉，直接jmp到 loc_A770F7，这个函数就patch完成了。

![image-20210207014630516](https://i.loli.net/2021/02/07/KM24owZbDPcrIga.png)

然后外部main_main还要处理一下update问题。同样的nop掉即可。

![image-20210207014716589](https://i.loli.net/2021/02/07/TxCMpztmQwORrEh.png)

![image-20210207014726318](https://i.loli.net/2021/02/07/Gg2JjmZb6eQUvBH.png)

# 总结

至此，一个简单的破解就完成了。很显然对软件的继续分析之后可以看出，此破解方法只是绕过了一开始的key验证，在之后的运行中，仍然会有隔一段时进行验证的行为。本文只是抛砖引玉，分享一些自己逆向分析go的二进制文件的心得，读者可以自行进行尝试，进行更加完善的破解。