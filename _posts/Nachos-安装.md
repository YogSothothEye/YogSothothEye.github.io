---
layout: post
title: Nachos 安装

date: 2019-06-18 08:40:18

---
# 何为Nachos
它是一个教学用的操作系统平台，运行在宿主机上，通过编译即可运行。它的源码大部分由C和C++组成，涉及少量的汇编。因为是教学使用，源码中有大量注释，方面理解，同时源码也并不复杂。
Nachos已经搭建好了操作系统框架，包括：线程、系统调用、内存管理等等。但是并不完善，诸如线程只能使用4个，内存默认是没有启用TLB快表。这些不完善的地方便是需要自己去学习，扩展，提升的地方。
# Nachos安装
操作系统环境：Ubuntu 16.04
Nachos包：4.1版本

## C语言编译环境搭建
（1）安装 C与C++编译器

`sudo apt-get install build-essential`

（2）安装 g++ 和 gcc 多平台库

`sudo apt install g++-multilib gcc-multilib`

（3）安装 gcc和 g++ 5的版本，并将其切换。

注：因Ubuntu 16.04自带的7版本环境在编译Nachos时会出现bug，需要将其版本号降下来。

```
sudo apt-get install gcc-5 g++-5
sudo update-alternative --install /usr/bin/gcc gcc /usr/bin/gcc-5 100
sudo update-alternative --install /usr/bin/g++ g++ /usr/bin/g++ -5 100
```
## Nachos 修改和编译
（1）解压Nachos后，修改 code/lib/sysdep.h，在头文件后追加：

```cpp
using namespace std;
```

(2)建立依赖关系

```shell
cd ./code/build.linux
make depend
```

（3）修改Makefile

删除 203 行的 -fwritable-strings 选项

改变后变为：

`CFLAGS = -ftemplate-depth-100 -Wno-deprecated -g -Wall $(INCPATH) $(DEFINES) $(HOSTCFLAGS) -DCHANGED`

此时执行make 会报错，出现 error 'IsInList'

（4）修改 code/lib/list.cc，将编译中包含 numInList, first, last, IsEmpty(), IsInList 的地方或属性修改为 this->numInList, this->first, this->last, this->IsEmpty(), this->IsInList （哪里报错改哪里）

再次编译，仍然会出现错误，出现 error cast from 'VoidFunctionPtr' 。

（5）在Makefile 203 行添加选项 -fpermissive 再次执行make，会出现一些汇编的报错。

    switch.s 7:Error invalid instruction suffix for 'push'
    switch.s 9:Error invalid instruction suffix for 'push'
    .........
    make: *** [switch.o] Error 1
    

给 g++ 增加编译选项 -m32

    echo ‘/usr/bin/g++ -m32 “$@”’ > /̃g++
    chmod +x /̃g++
    export PATH=$HOME:$PATH

修改 Makefile 203行与210行分别加入选项 -m32 和 --32， 让编译器能够编译汇编32位的代码。

此时执行 make ， 便可成功编译出 nachos 二进制文件了。
