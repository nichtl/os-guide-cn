### 本章目标：创建一个简单的内核和一个引导扇区 

> 需要掌握的概念： kernel, ELF format, makefile

#### 内核

> 我们的C内核只会在屏幕的左上角打印一个`X`。

kernel.c

```
/* 这将迫使我们创建一个内核入口函数，而不是跳转到kernel.c:0x00。 */
void dummy_test_entrypoint() {
}

void main() {
    char* video_memory = (char*) 0xb8000;
    *video_memory = 'X';
}
```

编译：

```
x86_64-elf-gcc -m32 -ffreestanding -c kernel.c -o kernel.o
```

```
nasm kernel_entry.asm -f elf -o kernel_entry.o
```


#### 链接器

要将两个目标文件链接到单个二进制内核并解析label引用：


```
i386-elf-ld -o kernel.bin -Ttext 0x1000 kernel_entry.o kernel.o --oformat binary
```

#### 引导扇区

```
nasm bootsect.asm -f bin -o bootsect.bin
```

#### 把它们放在一起

```
cat bootsect.bin kernel.bin > os-image.bin
```

#### 运行

如果您发现磁盘加载错误，您可能需要使用磁盘编号或`qemu`参数  (floppy = 0x0, hdd = 0x80)

```
qemu-system-i386 -fda os-image.bin
```

