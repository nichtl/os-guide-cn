### 目标：让引导扇区从磁盘加载数据以引导内核

我们的操作系统不适合放在引导区的512字节内，所以需要从磁盘上读取数据，以便运行内核。

为此，我们设置`al`到`0x02`，并且`int 0x13`

```
mov ax, 0xFFFF
add ax, 1 ; ax = 0x0000 进位1
```

BIOS也将`al`设置为读取的扇区数，所以一定要将其与预期的值进行比较。

boot_sect_disk.asm

```
; 将驱动器dl中的dh扇区加载到 ES:BX
disk_load:
    pusha
    ; 从磁盘读取需要在所有寄存器中设置特定值
    ; 从dx开始覆盖
    ; to the stack for later use.
    push dx

    mov ah, 0x02 ; ah <- int 0x13 function. 0x02 = 'read'
    mov al, dh   ; al <- number of sectors to read (0x01 .. 0x80)
    mov cl, 0x02 ; cl <- sector (0x01 .. 0x11)
                 ; 0x01 is our boot sector, 0x02 is the first 'available' sector
    mov ch, 0x00 ; ch <- cylinder (0x0 .. 0x3FF, upper 2 bits in 'cl')
    ; dl <- drive number. Our caller sets it as a parameter and gets it from BIOS
    ; (0 = floppy, 1 = floppy2, 0x80 = hdd, 0x81 = hdd2)
    mov dh, 0x00 ; dh <- head number (0x0 .. 0xF)

    ; [es:bx] <- 指向将存储数据的缓冲区指针
    ; caller为我们设置好了，其实是int 13h的标准位置
    int 0x13      ; BIOS 中断
    jc disk_error ; 如果错误（存储在进位位中）

    pop dx
    cmp al, dh    ; BIOS 还将 al 设置为读取的扇区数
    jne sectors_error
    popa
    ret


disk_error:
    mov bx, DISK_ERROR
    call print
    call print_nl
    mov dh, ah ; ah = 错误代码，dl = 丢弃错误的磁盘驱动器
    call print_hex ; 阅读 http://stanislavs.org/helppc/int_13-1.html
    jmp disk_loop

sectors_error:
    mov bx, SECTORS_ERROR
    call print

disk_loop:
    jmp $

DISK_ERROR: db "Disk read error", 0
SECTORS_ERROR: db "Incorrect number of sectors read", 0
```

boot_sect_main.asm

```
[org 0x7c00]
    mov bp, 0x8000 ; set the stack safely away from us
    mov sp, bp

    mov bx, 0x9000 ; es:bx = 0x0000:0x9000 = 0x09000
    mov dh, 2 ; read 2 sectors
    ; BIOS为的启动盘号设置了dl
    call disk_load

    mov dx, [0x9000] ; 检索第一个加载的单词  0xdada
    call print_hex

    call print_nl

    mov dx, [0x9000 + 512] ; 第二个加载扇区的第一个字  0xface
    call print_hex

    jmp $

%include "../05-bootsector-functions-strings/boot_sect_print.asm"
%include "../05-bootsector-functions-strings/boot_sect_print_hex.asm"
%include "boot_sect_disk.asm"

; Magic number
times 510 - ($-$$) db 0
dw 0xaa55

; boot sector = sector 1 of cyl 0 of head 0 of hdd 0
; from now on = sector 2 ...
times 256 dw 0xdada ; sector 2 = 512 bytes
times 256 dw 0xface ; sector 3 = 512 bytes
```
