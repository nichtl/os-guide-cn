### 本章目的：让之前的引导程序输出一些文本

> 在开始之前，你需要了解这些概念： interrupts, CPU registers

我们可以通过【中断】来完成这个功能。

在这个例子中我们将会向寄存器`al`(`ax`的低端部分)写入`hello`单词的每个字符，把`0x0e`写入`ah`(`ax`的高端部分)然后发出中断`0x10`，这是视频服务的通用中断。


`0x0e`写入`ah`告诉视频中断我们想要以`tty`模式显示`al`寄存器中的内容。

代码：

```asm
mov ah, 0x0e ; tty模式
mov al, 'H'
int 0x10
mov al, 'e'
int 0x10
mov al, 'l'
int 0x10
int 0x10 ; 'l' 还在 al 上
mov al, 'o'
int 0x10

jmp $ ; 跳转到当前地址 = 无限循环

; 填充
times 510 - ($-$$) db 0
dw 0xaa55 
```

编译和运行：

```
nasm -fbin 2.asm -o 2.bin
qemu-system-x86_64 2.bin
```

<img width="953" alt="image" src="https://user-images.githubusercontent.com/92664048/166137109-ee0bb1af-9baf-4051-92d6-0e896d421fd1.png">
