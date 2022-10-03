# C++ Hello world. - 2022.8.30
关于环境搭建等吧唧吧唧的，顺便分析一下代码。

## 环境搭建
这里用的是 VSCode 和 MingW。MingW 好像已经过时了，截至 2022.8.23，g++ 最新版本是 12.*，但 MingW 的 g++ 好像还是 8.*，这意味着一些新的特性用不了，但这不影响运行 Hello world。等以后你可以自行研究 VS、MSVC 这些先进的东西（？），~~强烈建议加入 Arch 邪教~~。

### MingW
MingW 安装方式参考 [知乎的一篇文章](https://zhuanlan.zhihu.com/p/355510947)，点击 [这里](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/#mingw-w64-online-installer) 跳转至下载页面。这里有很多个版本，如 MinGW-W64 GCC-8.1.0 表示 GCC 编译器的版本是 8.1.0，若无特别要求选择最新版。每个安装压缩包名字含义如下：
| 项目            | 解释                                                                          |
| --------------- | ----------------------------------------------------------------------------- |
| x86_64 or i686  | 电脑系统是 64 位的，选择 x86_64；如果是 32 位系统，则选择 i686。              |
| win32 or posix  | 若是 Windows，选 win32，若是 Linux、Unix、Mac OS 等其他操作系统要选择 posix。 |
| seh or sjlj     | seh 是新发明的，而 sjlj 则是古老的。seh 性能比较好，但不支持 32位。sjlj 稳定  |
|                 | 性好，支持 32位。建议64位操作系统选择 seh。                                   |
现在主流电脑都是 64 位系统。如果没猜错的话，你会选择 [x86_64-win32-seh](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-win32/seh/x86_64-8.1.0-release-win32-seh-rt_v6-rev0.7z)。下载完后解压到一个你记得住的位置，复制里面 bin 目录的 **绝对路径**，然后配置 PATH 环境变量。大概怎么配置，上面那篇文章也有说。

然后打开命令行，输入 `gcc --version`，如果输出了版本信息那编译器就装完了。然后，在随便找个位置，新建文本文档，把下面的代码粘贴进去，保存为 `main.cpp`（注意不是 `main.cpp.txt`）。
```cpp
#include <iostream>
int main() {
std::cout << "Hello world.\n";
}
```
命令行切换到 `main.cpp` 所在位置，输入下面命令。不出意外的话，最后它会输出一行字符串 `Hello world.`。
```
g++ main.cpp -o hello
.\hello
```
但是用记事本写代码是很反人类的，所以我们需要一个更好的编辑器。

### VSCode
VSCode（Visual Studio Code）是微软开发的一款轻量级开发环境（IDE）。它的优点是安装、卸载都很方便。点击[这里](https://code.visualstudio.com/Download)前往下载页面，选择对应版本（Windows）即可。这是几个常用快捷键：
- CTRL+O 打开文件。
- CTRL+SHIFT+O 打开文件夹。
- CTRL+SHIFT+X 打开扩展页面。
- CTRL+SHIFT+E 显示已打开的文件夹。
- CTRL+SHIFT+P 打开操作命令。
- CTRL+\` 打开命令行（默认 PowerShell）。
    VSCode 有很多好用的扩展，在扩展页面能搜索名字能找到它们：
- Chinese (Simplified) (简体中文) Language Pack for Visual Studio Code
- C/C++ 一些关于 C/C++ 的支持，对我来说有用的是代码提示（IntelliSense）、代码高亮（browsing）。

## 运行
新建文本文档，把下面的代码粘贴进去，保存为 `hello.cpp`。
```cpp
#include <iostream>
int main() {
  std::cout << "Hello world.\n";
  return 0;
}
```
在命令行里使用 `cd` 命令移动到 `hello.cpp` 所在目录，输入下面的命令：
```
g++ hello.cpp -o hello.exe
.\hello
```
不出意外的话，它会输出一个字符串。第一条命令是编译命令，它能把代码变成能直接执行的文件。第二行是运行刚刚编译出来的 `hello.exe`。当可执行文件后缀是 `exe` 时，后缀能省略。

## 代码讲解
自己学去 <https://www.runoob.com/cplusplus/cpp-tutorial.html>
