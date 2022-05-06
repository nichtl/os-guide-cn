### 本章目的： 在屏幕上写字符串

> 需要了解的概念：  VGA character cells, screen offset

最后，我们将能够在屏幕上输出文本。本课包含的代码比平时多一些，让我们一步步来。

打开`drivers/screen.h`，你会看到我们已经为显卡驱动定义了一些常量和三个公共函数，一个用来清除屏幕，另外几个用来写字符串，著名的kprint是 "kernel print"。

现在打开`drivers/screen.c`。它从声明私有辅助函数开始，我们将使用这些函数来帮助kprint内核API。


#### kprint_at

`kprint_at`可以被调用，`col`和`row`的值为`-1`，这表示我们将在当前光标位置打印字符串。

它首先为`col/row`和`offset`设置三个变量。然后遍历`char*`，用当前坐标调用`print_char()`。

注意`print_char`本身返回下一个光标位置的偏移量，我们在下一个循环中重复使用它。

`kprint`基本上是`kprint_at`的一个封装器。

#### print_char

与`kprint_at`一样，`print_char`允许`cols/rows`为`-1`，在这种情况下，它使用`ports.c`例程从硬件中检索光标位置。

`print_char`也处理换行。在这种情况下，我们将把光标偏移定位到下一行的第`0`列。

记住，VGA单元需要两个字节，一个用于字符本身，另一个用于属性。


#### kernel.c

我们的新内核终于能够打印字符串了。

