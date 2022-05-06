### 本章目标：进入32位保护模式并测试我们之前课程中的代码 

要想进入32位模式：

* 禁用中断 

* 加载GDT 

* 在CPU控制寄存器上设置一个位`cr0`

* 通过发出精心设计的far jump来刷新CPU管道 

* 更新所有段寄存器 

* 更新堆栈 

* 调用包含第一个32位有用代码的知名label

32bit-switch.asm：

```
[bits 16]
switch_to_pm:
    cli ; 1.禁用中断
    lgdt [gdt_descriptor] ; 2.加载GDT描述符
    mov eax, cr0
    or eax, 0x1 ; 3.在cr0中设置32位模式位
    mov cr0, eax
    jmp CODE_SEG:init_pm ; 4. 通过使用不同的段far jump
[bits 32]
init_pm: ; 我们现在使用32位指令
    mov ax, DATA_SEG ; 5.更新段寄存器
    mov ds, ax
    mov ss, ax
    mov es, ax
    mov fs, ax
    mov gs, ax

    mov ebp, 0x90000 ; 6. 在空闲空间的顶部更新堆栈
    mov esp, ebp

    call BEGIN_PM ; 7. 调用label
```


32bit-main.asm

```
[org 0x7c00] ; bootloader 偏移
    mov bp, 0x9000 ; 设置堆栈
    mov sp, bp

    mov bx, MSG_REAL_MODE
    call print ; 这将在BIOS消息之后写入

    call switch_to_pm
    jmp $ ; 这实际上永远不会被执行

%include "boot_sect_print.asm"
%include "32bit-gdt.asm"
%include "32bit-print.asm"
%include "32bit-switch.asm"

[bits 32]
BEGIN_PM: 
    mov ebx, MSG_PROT_MODE
    call print_string_pm ; 请注意，这将写在左上角
    jmp $

MSG_REAL_MODE db "Started in 16-bit real mode", 0
MSG_PROT_MODE db "Loaded 32-bit protected mode", 0

; bootsector
times 510-($-$$) db 0
dw 0xaa55
```




