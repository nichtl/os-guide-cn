### 本章目标： 完成中断实现CPU定时器 

> 需要了解的概念：  IRQs, PIC, polling

CPU启动时，PIC 将IRQ 0-7映射到`INT 0x8-0xF`和IRQ 8-15到`INT 0x70-0x77`。这与ISR冲突。 

对`ISR 0-31`进行了编程，将`IRQ`重新映射到`ISR 32-47`是标准做法。 

PIC通过I/O 端口进行通信。 主PIC有命令`0x20`和数据`0x21`，而从机有命令`0xA0`和数据`0xA1`。

想要了解更多，阅读[这篇](https://wiki.osdev.org/PIC)文章。

现在跳到汇编程序，在`interrupt.asm` -  第一个任务是为我们刚刚在C代码中使用的IRQ符号添加全局定义。 看看结尾的`global`。 

然后，添加IRQ处理程序。 

`irq_common_stub`这与ISR非常相似。 它位于顶部`interrupt.asm`，还定义了一个新的`[extern irq_handler]`。

回到C代码，编写`irq_handler()` - `isr.c`。

它向PIC发送EOI并调用适当的处理程序，该处理程序存储在一个数组中命名为`interrupt_handlers`并在文件顶部定义。 

新的结构定义在`isr.h`  -- 我们还将使用一个简单的函数来注册中断处理程序。 

