### 本章目标： 使用C实现和汇编相同功能的代码


一个简单的程序function.c：

```
int my_function() {
    return 0xbaba;
}
```

要编译与系统无关的代码，我们需要`-ffreestanding` ， 所以编译`function.c`以这种方式：

```
x86_64-elf-gcc -ffreestanding -c function.c -o function.o
```

让我们检查下机器码：

```
i386-elf-objdump -d function.o
```

#### Link

最后，为了生成二进制文件，我们将使用链接器。例如，我们将偏移量放在`0x0`并使用`binary`格式生成没有任何标签和/或元数据的机器代码：

```
i386-elf-ld -o function.bin -Ttext 0x0 --oformat binary function.o
```

反编译：

```
ndisasm -b 32 function.bin
```


localvars.c

```
int my_function() {
    int my_var = 0xbaba;
    return my_var;
}
```

functioncalls.c

```
void caller() {
    my_func(0xdede);
}

int my_func(int arg) {
    return arg;
}
```

pointers.c

```
void func() {
    char* string = "Hello";
}
```
