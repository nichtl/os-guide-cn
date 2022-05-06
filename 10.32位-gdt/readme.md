### 本章目标：对GDT进行编程

在32位模式下，分段的工作方式不同。
现在，偏移量变为GDT中段描述符(SD)的索引。
该描述符定义基地址（32位）、大小（20位）和一些flag，（只读，权限等）。

为了增加混乱，数据结构被拆分， 所以打开os-dev.pdf文件并查看第34页上的图或GDT的维基百科页面。 

对GDT进行编程最简单的方法是定义两个段，一个用于代码，另一个用于数据。
这没有内存保护，但是可以启动（在将来我们使用高级语言解决这个问题）。

第一个GDT条目必须是`0x00`以确保程序员在管理地址时没有犯任何错误。

此外，CPU不能直接加载GDT地址，而是需要一个称为`GDT描述符`的元结构，其大小（16b）和地址（32b）是我们实际的GDT。它是通过`lgdt`操作加载的。

```
gdt_start: 
    ; GDT 以 null 8-byte 开始
    dd 0x0 ; 4 byte
    dd 0x0 ; 4 byte

; 代码段的GDT。 base = 0x00000000, length = 0xfffff
; 有关flag，请参阅 os-dev.pdf 文档，第 36 页
gdt_code: 
    dw 0xffff    ; segment length, bits 0-15
    dw 0x0       ; segment base, bits 0-15
    db 0x0       ; segment base, bits 16-23
    db 10011010b ; flags (8 bits)
    db 11001111b ; flags (4 bits) + segment length, bits 16-19
    db 0x0       ; segment base, bits 24-31

; 数据段的GDT - base and length identical to code segment
; some flags changed, again, refer to os-dev.pdf
gdt_data:
    dw 0xffff
    dw 0x0
    db 0x0
    db 10010010b
    db 11001111b
    db 0x0

gdt_end:

;GDT描述符
gdt_descriptor:
    dw gdt_end - gdt_start - 1 ; 大小（16 位），总是比它的真实大小小1。
    dd gdt_start ; address (32 bit)

; 定义一些常量供以后使用
CODE_SEG equ gdt_code - gdt_start
DATA_SEG equ gdt_data - gdt_start
```


