### 本章目的：  实现我们的第一个IRQ处理程序：CPU计时器和键盘 

现在一切准备就绪，可以测试我们的硬件中断。


#### 定时器

定时器很容易配置。 首先我们要声明一个`init_timer()` - `cpu/timer.h`和`cpu/timer.c`。这只是计算时钟频率和将字节发送到适当的端口。

修复`kernel/utils.c int_to_ascii()`以正确的顺序打印数字。为此，我们需要实现`reverse()`和`strlen()`。

最后，回到`kernel/kernel.c`并做两件事。 再次启用中断（非常重要！） ----- 然后初始化定时器中断。

试试 `make run` ？


#### 键盘

键盘更容易，但有一个缺点 - PIC没有向我们发送ASCII码（对于按下的键）。但扫描码用于按键和按键事件。

查看`drivers/keyboard.c`其中有两个函数：回调和配置中断回调的初始化。

`keyboard.c`有一个长表将扫描码转换为`ASCII键`的功能。阅读[更多](https://www.win.tue.nl/~aeb/linux/kbd/scancodes-1.html)关于扫描码的信息。

