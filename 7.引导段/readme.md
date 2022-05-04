### 本章目的：   使用16位实模式段编址内存

如果你已经很熟悉实模式的段机制，可以直接跳过这个课程。

其实我们在课程3中已经使用过段寻址符号`[org]`。段意味着你可以通过使用偏移地址访问一个固定段中的数据。

 `cs` `ds` `ss` `es` 分别表示代码段、数据段、栈和其他（用户自定义）。
 
 要计算真正的地址，我们不能简单将两个地址相加：
 
 > segment << 4 + address
 
 例如，假设`ds`等于`0x4d`那么`[0x20]`实际上对应`0x4d0 + 0x20 = 0x4f0`
 
 
 ```
 mov ah, 0x0e ; tty

mov al, [the_secret]
int 0x10 ; 我们已经看到这不起作用，对吧？

mov bx, 0x7c0 ; 请记住，该段会自动 <<4
mov ds, bx
; 警告：从现在开始，所有内存引用都将被 'ds' 隐式偏移
mov al, [the_secret]
int 0x10

mov al, [es:the_secret]
int 0x10 ; 看起来不对……'es'当前不是 0x000 吗？

mov bx, 0x7c0
mov es, bx
mov al, [es:the_secret]
int 0x10


jmp $

the_secret:
    db "X"

times 510 - ($-$$) db 0
dw 0xaa55
 ```
 
 
