# 命令行
按下 Win + r，输入 wt，回车或者在 vscode 按下 CTRL + \`，弹出了一个黑底白字的窗口，现在你可以简单地认为它就是我指的**命令行**。在这里用户通过输入命令来与计算机交互。
```
PS C:\Users\86135>
```
`C:\Users\86135` 是当前的**工作目录**，我们进行的所有操作都以这个目录为起点进行。`>` 提示你输入命令。这是一些常用命令：
| 命令      | 描述                                    |
| --------- | --------------------------------------- |
| type/cat  | 输出文件内容                            |
| cd        | change working directoty，切换工作目录  |
| md/mkdir  | make directory，新建文件夹              |
| rd/rmdir  | remove directory，删除文件夹            |
| rm/del    | remove/delete，删除文件                 |
| man/help  | 教你做事                                |
| cls/clear | clear，清除屏幕                         |
| rename/mv | move，重命名                            |
| move/mv   | 移动文件 / 文件夹                       |
| tree      | 以树状图显示文件结构                    |

在命令行中，表示一个文件的位置有两种方式，**绝对路径**和**相对路径**。假设 `C:\Usere\Admin` 下有 `hello.txt` 和 `hello world.txt`，那 `hello.txt` 用绝对路径表示就是 `C:\Users\Admin\hello.txt`。
相对路径是相对于工作目录而言的。假设当前工作目录是 `C:\Users`，那么 `hello.txt` 用相对路径表示是 `.\Admin\hello.txt`。在每个目录中，有两个 “隐藏文件夹”，`..` 和 `.`。`..` 是上一层文件夹，`.`
是当前文件夹。还是假设当前工作目录是 `C:\Users`，那么 `..` 是 `C:\`，`.` 是 `C:\Users`。如果你的工作目录是 `C:\Users\Admin\Desktop`，那么用相对路径表示 `hello.txt` 是 `..\hello.txt`。
当文件路径包含空格的时候，要给路径加上引号。如 `hello world.txt` 的绝对路径是 `'C:\Users\Admin\hello world.txt'`。这也是为什么文件名字不能包含引号。

如果想运行工作目录下的某个脚本 `*.bat` 或可执行文件 `*.exe`，可以这样做（假设它是 `hello.exe`）：
```
PS C:\Users\86135> .\hello
Hello world.
PS C:\Users\86135>
```
在 Windows 中的 Powershell 运行脚本 / 可执行文件不需要加后缀，但需要加上所在目录。
