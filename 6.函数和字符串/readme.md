### 本章目的：使用汇编完成基础代码（循环和函数）

#### 定义字符串

定义字符串和定义字节数据一样，但是在这里为字符串添加一个空字节（C 语言也是这样，使用 \0 作为字符串结尾）。 

> 这样做的目的是让我们知道字符串在哪儿结束。

```
mystring:
    db 'Hello, World', 0
```

注意： 最后的`0`会被当成`0x00`处理。



#### 流程控制

> 想象一下C语言的if？

我们已经使用过一个控制流程`jmp $` ，跳转到自己来完成一个无限循环。

汇编语言中跳转条件是基于上一条指令的计算结果，例如：

```
cmp ax, 4      ; 如果ax = 4
je ax_is_four  ; 跳转到指定位置
jmp else       ; 否则，做另一件事
jmp endif      ; 最后，恢复正常流程

ax_is_four:
    .....
    jmp endif

else:
    .....
    jmp endif

endif:
```


#### 函数调用

我们可以通过两种方式来传递参数：

* 知道函数间共享的一个寄存器或者内存地址

* 需要更多的代码来避免函数间的影响

第一种方法：

我们可以预先指定使用`al`(实际上是`ax`) 寄存器来存储参数。

```
mov al, 'X'
jmp print
endprint:

...

print:
    mov ah, 0x0e  ; TTY模式
    int 0x10      ; 假设'al'已经有这个角色
    jmp endprint  ; 预先同意的label
```

这里的`print`函数只能返回到`endprint`。如果其他函数想要调用它怎么办？

解决方案： 存储返回地址以便被多个函数调用并正确返回。

#### 包含外部文件Including external files

在汇编中，这很简单：

```
%include "file.asm"
```

#### 打印十六进制数据

在下一章节的课程中我们会学习如何从磁盘中读取数据。

```
; 接收dx中的数据
; 假设使用 dx=0x1234
print_hex:
    pusha

    mov cx, 0 ; 索引变量

; 获取'dx'的最后一个字符，然后转换为ASCII
; 数字 ASCII 值 '0' (ASCII 0x30) to '9' (0x39) 只需将 0x30 添加到字节 N
; 对于字母字符 A-F： 'A' (ASCII 0x41) to 'F' (0x46) 添加 0x40
; 然后，将 ASCII 字节移动到结果字符串的正确位置
hex_loop:
    cmp cx, 4 ; 循环4次
    je end
    
    ; 1. 将'dx'的最后一个字符转换为ascii
    mov ax, dx ; 我们将使用ax作为工作寄存器
    and ax, 0x000f ; 0x1234 -> 将前三个掩码改为零
    add al, 0x30 ; 将 0x30 添加到 N 以将其转换为 ASCII "N"
    cmp al, 0x39 ; 如果 > 9，则添加额外的 8 来表示 'A' 到 'F'
    jle step2
    add al, 7 ; 'A' 是 ASCII 65 而不是 58，所以 65-58=7

step2:
    ; 2. 获取字符串的正确位置以放置我们的 ASCII 字符
    ; bx <- 基地址 + 字符串长度 - char 的索引
    mov bx, HEX_OUT + 5 ; 基地址+长度
    sub bx, cx  ; 我们的索引变量
    mov [bx], al ; 将 'al' 上的 ASCII 字符复制到 'bx' 指向的位置
    ror dx, 4 ; 0x1234 -> 0x4123 -> 0x3412 -> 0x2341 -> 0x1234

    ; 递增索引和循环
    add cx, 1
    jmp hex_loop

end:
    ; 准备参数并调用函数
    ; print 在 'bx' 中接收参数
    mov bx, HEX_OUT
    call print

    popa
    ret

HEX_OUT:
    db '0x0000',0 ; 为我们的新字符串保留内存
```
