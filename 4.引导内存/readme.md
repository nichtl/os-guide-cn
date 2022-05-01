### 本章目的： 理解计算机内存是如何组织的

> 在开始之前，你需要了解这些概念：memory offsets, pointers


内存分布图：

<img width="718" alt="image" src="https://user-images.githubusercontent.com/92664048/166137281-8aade621-e695-43ea-8e54-e36a6251e166.png">

这个章节的主要目的是要明白，**启动引导扇区的程序被储存在内存的哪个位置**。

BIOS 会把引导程序放置在内存地址`0x7C00`处，然后把系统控制权交给这里的程序。


对引导程序来说，偏移地址`0x7c00`是会经常用到的，但总使用硬编码地址非常不方便，所以现代汇编器都提供了一个定义全局偏移地址的命令`org`：

```
[org 0x7c00]
```


推荐阅读：
[Writing a Simple Operating System —from Scratch](https://www.cs.bham.ac.uk/~exr/lectures/opsys/10_11/lectures/os-dev.pdf)
