### 本章目的：学会使用栈

栈的使用非常普遍，所以是否能够使用栈非常重要。

务必牢记 bp 寄存器存储栈的基地址 (i.e. 底部地址) ， sp 寄存器存储栈顶地址。栈从基地址 bp 开始向下生长 （i.e. 也就是说 sp 是递减的）。

```
mov ah, 0x0e ; tty模式

mov bp, 0x8000 ; 这是一个远离 0x7c00 的地址，这样我们就不会被覆盖
mov sp, bp ; 如果堆栈为空，则 sp 指向 bp

push 'A'
push 'B'
push 'C'

; 显示堆栈如何向下增长
mov al, [0x7ffe] ; 0x8000 - 2
int 0x10

; 但是，现在不要尝试访问 [0x8000]，因为它不起作用
; 只能访问堆栈顶部
int 0x10


; 使用标准程序恢复我们的字符：'pop'
; 要能弹出完整的单词，所以我们需要一个辅助寄存器来操作
; 低字节
pop bx
mov al, bl
int 0x10 ; prints C

pop bx
mov al, bl
int 0x10 ; prints B

pop bx
mov al, bl
int 0x10 ; prints A

; 从堆栈中弹出的数据现在是垃圾
mov al, [0x8000]
int 0x10


jmp $
times 510-($-$$) db 0
dw 0xaa55
```
