# 逻辑表达式 | 模拟 表达式解析 - SatJul8 2023
[提交记录 R114500101](https://www.luogu.com.cn/record/114500101)

[Luogu P8815](https://www.luogu.com.cn/problem/P8815)：给定逻辑表达式，求值并与、或短路次数。其中“与短路”是形如 `0&x` 的表达式，“或短路”是形如 `1|x` 的表达式。称其为“短路”，是因为这两种情况中都不需要对其求值便能知道表达式结果。接下来给出逻辑表达式的定义。
- `0` 和 `1` 是逻辑表达式。
- 如果 `s` 是逻辑表达式，且 `s` 不是形如 `(t)` 的字符串（其中 `t` 是逻辑表达式），那么 `(s)` 也是逻辑表达式。
- 如果 `a` 和 `b` 是逻辑表达式，那么 `a&b`、`a|b` 均是逻辑表达式。
- 以上方法可生成所有逻辑表达式。

## 解析
使用栈预处理出“左括号的下标”到“右括号的下标”的映射数组。定义函数 `solve(l, r)` 求解 $(l, r)$ 区间，把表达式看成 `x op1 y1 op2 y2 ... opm ym` 的形式，用循环枚举 `(opi, yi)`。如果 `yi` 是括号表示的形式，就递归求解 `yi`。用栈求解这个表达式，由于只有与、或两种运算，所以栈只需要装得下两个变量即可，也就是代码里的 `[vo(value or), va(value and)]`。

先不考虑断言。如果 `opi` 为 `&`，则 `va <- va and yi`；如果 `opi` 为 `|`，则 `vo <- vo or va; va <- yi`。接着考虑断言。如果遇到 `opi=and`，还要判断是不是在“或断言”里（`vo=1?`），是则跳过求解，不是就进一步判断是否需要“与断言”（`va=0?`），是就统计，不是就求值。遇到 `opi=or`，也同样判断是不是“或断言”。

## 代码
```cpp
#include <cstdio>
#include <cstring>
#define cal(i) (str[i] == '(' ? solve(i, match[i]) : str[i] == '1')
// 计算下标为 i 的元素的值

const int N = 1000006;
char str[N];
int ca, co, n, match[N], stk[N], top;
// ca: count and; co: count or. n: strlen(str).
// match: 右括号下标; stk: stack; top 用于计算 match

bool solve(int l, int r) {      // 求解 [l, r]
  if (match[l] == r) l++, r--;  // 匹配括号
  bool vo = false, va = cal(l); // 初始表达式栈 [vo, va]
                                // vo: value or; va:value and

  for (int i = str[l] == '(' ? match[l] + 1 : l + 1; i < r; i++) {
  // 用 i 遍历运算符. 即 str[i] 要么为 &, 要么为 |

    if (str[i] == '&' && !vo) { // !vo 使得不在 "或断言" 里才进行求值
      if (va) va = cal(i + 1);  // 1&x => x
      else ca++;                // 与断言
    } else if (str[i] == '|') {
      if (vo) co++; else vo = va; // 把 va 合并到 vo 里, 并更新 "或断言"
      if (!vo) va = cal(i + 1);   // 0|x => x
    }
    if (str[++i] == '(') i = match[i];  // 令 i 指向下一个运算符的前一位
  }
  if (vo) co++; else vo = va; // 记得把 va 合并到 vo 里 (相当于加上 |0)
  return vo;
}
int main() {
  scanf("%s", str), n = strlen(str);
  for (int i = 0; i < n; i++) {         // 预处理括号匹配
    match[i] = -1;                      // 显然这很多余
    if (str[i] == '(') stk[top++] = i;  // stk 为未闭合左括号的栈
    else if (str[i] == ')') top--, match[stk[top]] = i;
    // 新遇到的右括号对应的左括号的下标就是栈顶
  }
  printf("%d\n", solve(0, n - 1));
  printf("%d %d\n", ca, co);
}
```
