### 本章目标： 创建一个开发环境来构建内核 

> 需要了解的概念： cross-compiler

如果您使用的是Mac，则需要立即执行此过程。 了解为什么需要[交叉编译](https://wiki.osdev.org/Why_do_I_need_a_Cross_Compiler%3F)。

安装软件包：

```
brew install gmp mpfr libmpc gcc
```

好的，记住gcc的路径：

```
/opt/homebrew/Cellar/gcc/11.3.0/bin/ 
```

```
export CC=/opt/homebrew/Cellar/gcc/11.3.0/bin/gcc-11
export LD=/opt/homebrew/Cellar/gcc/11.3.0/bin/gcc-11
```

安装i386-elf-gcc

```
brew install i386-elf-binutils i386-elf-gcc
```

> x86_64-elf-gcc

安装binutils

```
brew install binutils
```

```
echo 'export PATH="/opt/homebrew/opt/binutils/bin:$PATH"' >> ~/.zshrc
export LDFLAGS="-L/opt/homebrew/opt/binutils/lib"
export CPPFLAGS="-I/opt/homebrew/opt/binutils/include"
```


