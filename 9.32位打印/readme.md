### 本章目标：实现在32位保护模式下输出一段话 

> 需要了解的知识： 32-bit protected mode, VGA, video memory

32位模式允许使用32位寄存器和内存寻址， 保护内存、虚拟内存等，但我们会失去BIOS中断，所以需要编写GDT代码（在稍后将详细介绍）。

在本课中，我们将编写一个新的打印字符串程序，它适用于32位模式。 由于没有BIOS中断，直接操作VGA视频内存而不是调用`int 0x10`。
VGA内存启动在地址`0xb8000`有一个文本模式，可以避免直接操纵像素。

若想访问`80x25`网格上的特定字符，公式是： 


`0xb8000 + 2 * (row * 80 + col)`

也就是说，每个字符使用2个字节（一个用于ASCII，另一个用于颜色等）。

```
[bits 32] ; 使用32位保护模式

; 这是常量的定义方式
VIDEO_MEMORY equ 0xb8000
WHITE_ON_BLACK equ 0x0f ; 每个字符的颜色字节

print_string_pm:
    pusha
    mov edx, VIDEO_MEMORY

print_string_pm_loop:
    mov al, [ebx] ; address of character
    mov ah, WHITE_ON_BLACK

    cmp al, 0 ; 检查字符串是否结束
    je print_string_pm_done

    mov [edx], ax ; 在显存中存储字符+属性
    add ebx, 1 ; 下一个字符
    add edx, 2 ; 下一个显存位置

    jmp print_string_pm_loop

print_string_pm_done:
    popa
    ret

```

不幸的是，这个程序还不能从引导加载程序中调用，因为我们仍然不知道如何编写GDT并进入保护模式。



