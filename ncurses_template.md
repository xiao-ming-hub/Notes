# ncurses.h 简要手册 - SunJul14 2024
一个很好用的 C 语言库, 写终端游戏经常用到也经常忘记。每次翻文档重新学太累了，所以我写了一个简要的提纲.

## [manual](https://man.archlinux.org/man/ncurses.3x) `DESCRIPTION` 的机翻:
> “new courses” 库为程序员提供了一种与终端无关的方式来读取键盘和鼠标输入, 并更新字符单元终端的输出, 以最小化屏幕更新. ncurses 取代了 System V Release 4 Unix (SVr4) 和 44BSD Unix 中的 curses 库, 这些库的开发在 1990 年代停止. 本文档描述了 ncurses 版本 65 (补丁 20240427).
>
> ncurses允许控制终端屏幕的内容; 通过窗口和面板 (pads) 对其进行抽象和划分 (abstraction and subdivision); 读取终端输入; 控制终端输入和输出选项; 环境查询例程; 颜色操作; 定义和使用软标签键; terminfo 访问; termcap兼容接口; 以及对操作终端的系统API的抽象 (例如 `termios(3)`)  . 
>
> ncurses 实现了由 X/Open Curses Issue 7 描述的标准接口. 在许多行为细节上, ncurses 模拟了 SVr4 的 curses 库, 并提供了许多有用的扩展.
>
> ncurses 手册使用几个部分来澄清使用和与其他 curses 实现的互操作性的问题.
> - `NOTES` 描述了任何使用 ncurses API 的用户都应该注意的问题和注意事项, 例如底层整数类型的大小限制或函数定义中独占的预处理宏的可用性 (防止其地址被获取). 该部分还描述了对程序员重要但不标准化的实现细节.
> - `EXTENSIONS` 介绍了超越 X/Open Curses 标准和/或 (and/or) SVr4 curses 实现的 ncurses 创新. 它们被称为扩展, 因为它们不能仅通过使用库 API 来实现, 而需要访问库的内部状态.
> - `PORTABILITY` 讨论了在编写符合 curses 标准或多个实现的代码时应考虑的问题 (超越扩展的练习 (beyond the exercise of extensions)).
> - `HISTORY` 审查了 ncurses 和其他 curses 实现在几十年的发展过程中的细节问题, 特别是在先例或惯性阻碍更好设计的情况下 (以及在少数情况下, 克服了这种惯性的情况). 
> curses应用程序必须与库链接; 请使用 `-lncurses` 选项将其链接到您的编译器或链接器. 库的调试版本可能可用; 如果是这样, 请使用 `-lncurses_g` 链接它 (您的系统集成商可能已经安装了这些库, 以便您可以分别使用选项 `-lcurses` 和 `-lcurses_g`). ncurses_g 库会生成跟踪日志 (保存在当前目录中的名为 trace 的文件中), 描述 ncurses 的操作.

## 初始化相关 `ncurses(3X)`
```c
#include <ncurses.h>
int main() {
  initscr();      // curs_initscr(3X)
  start_color();  // curs_color(3X)
  cbreak();       // curs_inopts(3X)
  noecho();       // curs_inopts(3X)
  curs_set(0);    // curs_kernel(3X)
  refresh();      // curs_refresh(3X)
  endwin();       // curs_initscr(3X)
}
```
- `initscr` 标准开头.
- 如果程序要用颜色的话, 在 `initscr` 后面调用 `start_color` 初始化颜色对.
- `cbreak` 关闭缓冲, `nocbreak` 开启缓冲.
- `noecho` 关闭回显, `echo` 开启回显.
- `curs_set` 用 0, 1, 2 表示不可见, 正常, 或者非常可见 (手册写的是 very visible 但是我实际运行 1 和 2 没区别).
- 每次添加完字符后用 `refresh()` 更新屏幕.
- `endwin()` 相当于 `return 0`, 结束页面回到命令行. 如果程序意外退出光标什么的没有还原, 用 `tput reset` 重置光标.

## 输出 `curs_addch(3X)` `curs_addstr(3X)` `curs_addwstr(3X)`
`add` 相当于词根, 前缀 `w` 带 `*win`, `mv` 带坐标 (y,x) 行列. 下面两个带 `echo` 的表示一添加就调用 `refresh`.
```c
int addch(const chtype ch);
int waddch(WINDOW *win, const chtype ch);
int mvaddch(int y, int x, const chtype ch);
int mvwaddch(WINDOW *win, int y, int x, const chtype ch);
// y和x是相对于窗口左上角的坐标

int echochar(const chtype ch);
int wechochar(WINDOW *win, const chtype ch);
```
```c
int addstr(const char *str);
int mvaddstr(int y, int x, const char *str);
int mvwaddstr(WINDOW *win, int y, int x, const char *str);
int waddstr(WINDOW *win, const char *str);

int addnstr(const char *str, int n);
int mvaddnstr(int y, int x, const char *str, int n);
int mvwaddnstr(WINDOW *win, int y, int x, const char *str, int n);
int waddnstr(WINDOW *win, const char *str, int n);
```
宽字符也是可以的：
```c
int addwstr(const wchar_t *wstr);
int mvaddwstr(int y, int x, const wchar_t *wstr);
int mvwaddwstr(WINDOW *win, int y, int x, const wchar_t *wstr);
int waddwstr(WINDOW *win, const wchar_t *wstr);

int addnwstr(const wchar_t *wstr, int n);
int mvaddnwstr(int y, int x, const wchar_t *wstr, int n);
int mvwaddnwstr(WINDOW *win, int y, int x, const wchar_t *wstr, int n);
int waddnwstr(WINDOW *win, const wchar_t *wstr, int n);
```
 `n` 表示添加 `str` 的前 `n` 位, `n` 比 `str` 长度长时直接输出 `str`. 如:
```c
mvwaddnstr(stdscr, 20, 10, "Hello world.", 5);  // Hello
```

## 键盘交互 `curs_getch(3X)`
```cpp
int getch(void);
int wgetch(WINDOW *win);
int mvgetch(int y, int x);
int mvwgetch(WINDOW *win, int y, int x);

int ungetch(int c);

/* extension */
int has_key(int c);
```

## 光标移动 `curs_move(3X)`
```c
int move(int y, int x);
int wmove(WINDOW *win, int y, int x);
```
`y` 是行, `x` 是列, `move` 目标位置超出窗口边界、`wmove` 的 `win` 是空指针都会返回 `ERR`; 否则正常移动, 返回 `OK`.

## 鼠标交互 `curs_mouse(3X)`
```c
typedef unsigned long mmask_t;

typedef struct {
  short id;         /* ID to distinguish multiple devices */
  int x, y, z;      /* event coordinates */
  mmask_t bstate;   /* button state bits */
} MEVENT;

bool has_mouse(void);

mmask_t mousemask(mmask_t newmask, mmask_t *oldmask);

int getmouse(MEVENT *event);
int ungetmouse(MEVENT *event);

bool wenclose(const WINDOW *win, int y, int x);

bool mouse_trafo(int* pY, int* pX, bool to_screen);
bool wmouse_trafo(const WINDOW* win,
                  int* pY, int* pX, bool to_screen);

int mouseinterval(int erval);
```

## 窗口相关 `curs_window(3X)`
`initscr` 后会初始化一个窗口 `stdscr` 表示整个屏幕.
```c
WINDOW *newwin(
  int nlines, int ncols,
  int begin_y, int begin_x);
int delwin(WINDOW *win);
int mvwin(WINDOW *win, int y, int x);
WINDOW *subwin(WINDOW *orig,
  int nlines, int ncols,
  int begin_y, int begin_x);
WINDOW *derwin(WINDOW *orig,
  int nlines, int ncols,
  int begin_y, int begin_x);
int mvderwin(WINDOW *win, int par_y, int par_x);
WINDOW *dupwin(WINDOW *win);
void wsyncup(WINDOW *win);
int syncok(WINDOW *win, bool bf);
void wcursyncup(WINDOW *win);
void wsyncdown(WINDOW *win);
```

## 颜色控制 `curs_color(3X)`
```c
/* variables */
int COLOR_PAIRS;
int COLORS;

int start_color(void);

bool has_colors(void);
bool can_change_color(void);

int init_pair(short pair, short f, short b);
int init_color(short color, short r, short g, short b);
/* extensions */
int init_extended_pair(int pair, int f, int b);
int init_extended_color(int color, int r, int g, int b);

int color_content(short color, short *r, short *g, short *b);
int pair_content(short pair, short *f, short *b);
/* extensions */
int extended_color_content(int color, int *r, int *g, int *b);
int extended_pair_content(int pair, int *f, int *b);

/* extension */
void reset_color_pairs(void);

int COLOR_PAIR(int n);
PAIR_NUMBER(int attr);
```

## 参考
- [四季夏目天下第一](https://www.cnblogs.com/VeniVidiVici/p/17318232.html)
