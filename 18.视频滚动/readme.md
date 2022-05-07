### 本章目标：当文本到达底部时滚动屏幕

> 需要了解的概念： scroll

关注`drivers/screen.c`并注意在底部`print_char`有一个新的部分，如果当前偏移量超过屏幕大小并滚动文本。 

实际的滚动由一个新函数处理 - `memory_copy`，打开`kernel/util.c`到看它的实现。

为了帮助可视化滚动，我们还将实现一个函数将整数转换为文本 - `int_to_ascii`。



