# 结构体 | 模拟/代码规范 - SunAug 18 2024
[Luogu P9754](https://www.luogu.com.cn/problem/P9754)：给出 n 条命令，有结构体声明、变量声明等，并计算出相应的内存占用等信息。

内存对齐：任何类型的**大小**和该类型元素在内存中的**起始地址**均应对齐到该类型对齐要求的**整数倍**：
- 基本类型：对齐要求等于其占据空间大小，如 `int` 类型需要对齐到 4 字节。
- 结构体类型：对齐要求等于其成员的对齐要求的**最大值**，如一个含有 `int` 和 `short` 的结构体类型需要对齐到 4 字节。

模拟 4 种操作：
1. 定义一个结构体类型。具体而言，给定该类型的<u>成员数量 k</u>，该类型的<u>类型名</u>，每个<u>成员的类型</u>，每个<u>成员的名称</u>。输出该结构体类型的<u>大小</u>和<u>对齐要求</u>。

2. 定义一个元素，给定该元素的<u>类型</u>与<u>名称</u>。所有被定义的元素将按顺序，从内存地址为 0 开始依次排开，并需要满足地址对齐规则。输出<u>新元素的起始地址</u>。

3. 访问某个元素。给定所访问的元素表达式。采用 `.` 来访问结构体类型的成员，如 `x.ba.ab`。输出如上被访问的<u>最内层元素的起始地址</u>。

4. 访问某个内存地址。给定所访问的<u>地址</u>，你需要判断是否存在一个**基本类型**的元素占据了该地址。若是，则按操作 3 中的访问元素格式输出<u>该元素</u>；否则输出 `ERR`。

更多细节（输入格式、数据范围）见原题面。

## 解析
### 技巧方面
- 开一个 1e18 的数组模拟内存肯定不行，应该记录每个类型的信息。
- 模拟样例会发现，新建变量和结构体新建成员的流程相同，所以维护一个全局结构体 `global`，声明变量时就往里面加成员。
- 定义结构体不占内存，可是模拟后会发现不论结构体变量放哪里，只要整体对齐了，成员的地址和结构体首地址的差（相对地址）不会变。所以不需要每个变量都存一个地址，在结构体存一下每个成员的相对地址即可。这样还有好处：
  - 逐级进入时，把每一级的相对地址加起来就是当前进入结构体的绝对地址（在内存空间的地址）。
  - 按地址查询，依次减去每一级的相对地址就是查询地址相对于当前位置的地址。
- 根据地址查询时不用二分，这题 n、k 不超过 100，遍历成员就好。

### 模拟方面
经过几个小时的试错，得到了最终的代码架构。~~看名字应该能猜到这是干什么的~~
```cpp
#define global types[0]

using ll=long long;
using String=std::string;
using IMap=std::map<String, int>;

struct Type {
  String name;
  ll size;
  int align;
  IMap member_id;
  std::vector<Member> members;

  Type(String name, ll size=1, int align=1):
    name(name), size(size), align(align) {}
  void add_member(String type, String name);
};

std::vector<Type> types;
IMap type_id;

void init_basic(String name, int id);
```
其实只要确定下这个框架，后面的事情都好办了。

### 出现的漏洞 / 应该注意的
- 成员函数出现和变量重名时用 `this->...` 区分。
- 如果 `global` 是引用，那么多次 `push_back` 之后 `types` 重新分配内存，`global` 变成了野指针。
  > [`std::vector::push_back`](https://zh.cppreference.com/w/cpp/container/vector/push_back)<br/>
  如果操作后新的 `size()` 大于原 `capacity()` 则会发生重分配，这种情况下，指代元素的所有迭代器（包括 `end()` 迭代器）和所有引用均会失效。
- 结构体的大小也要对齐。比如某个结构体分别有一个 `long` 和 `byte`，那么 `byte` 后面七个字节也记入结构体大小。
- 每个下标都要考虑越界。比如数据可能在没有声明变量时，或者对于某个空结构体，问操作 4，这个时候 `Type::members[0]` 也会越界。
- switch 里的 case 不能声明变量。
- 结构体的默认函数只有下面6种，当显示声明了自己的构造函数后，无参数构造再会自动销毁。用 `=default` 或 `=delete` 强制指定要不要某种函数。`emplace_back` 传的参数根构造函数一样。大括号表达式是强制赋值（没有类型转换），详见 [RAII与智能指针](https://bilibili.com/video/BV1LY411H7Gg)。
```cpp
struct C {
  C();                       // 构造
  C(C const &c);             // 拷贝构造
  C(C &&c);                  // 移动构造
  C &operator=(C const &c);  // 拷贝赋值
  C &operator=(C &&c);       // 移动赋值
  ~C();                      // 解构
};
```

### 代码规范：
很显然只是给我看的，~~毕竟厉害的 OIer 哪里要这么多要求~~。
代码结构：
```
#include <c库>
#include <c++库>
#include "自己的其它代码"
如果可能，按常用的程度排序，高的在上面

#define 宏定义

using 函数
using 类型别名=typename
const 常量声明

struct 结构体声明 {
  成员变量

  成员函数（声明，不定义）
}

全局变量声明（根据目的分行，按类型基础程度排序）
int n, m
int xxx （比如图论）
int x[], xx（某个栈）
std::vector<int> depth;
Type xxxx 结构体变量

函数定义

结构体::函数定义

int main() {
  按照不同部分分行而不同 input() 等函数提取
  // input
  cin ...
  // init
  for hd=-1...
  //cal
  while (m--)...
}
```
命名习惯：
- 不用 `using namespace std`，改用 `using std::cin` 等。
- 全局变量和结构体成员下划线分隔，如 `push_up`。
- 函数内临时变量小驼峰，如 `typeId` `memberId`。但是很废话的变量能短就短，`for (int i=0; i<n; i++)`。
- 结构体、类型重命名大驼峰，如 `ReplacementTransform`，超级常用的除外，如 `ll=long long`。
- 文件名下划线分隔，如 `global_varible.hpp`。
- 常量大写

## AC Code
看了其他提交，这种写法代码量 3093 字符算少了（而且还是用长名字情况下）。
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <map>

#define get(v) int v; std::cin >> v;
#define global types[0]

using ll=long long;
using String=std::string;
using IMap=std::map<String, int>;
const int N=105;

struct Member {
  int type;
  ll addr;
  String name;
};

struct Type {
  String name;
  ll size;
  int align;
  IMap member_id;
  std::vector<Member> members;

  Type(String name, ll size=1, int align=1):
    name(name), size(size), align(align) {}
  void add_member(String, String);
};

std::vector<Type> types;
IMap type_id;

void init_basic(String name, int id) {
  types.emplace_back(name, 1<<id, 1<<id);
  type_id[name] = id+1;
}

void Type::add_member(String type, String name) {
  int typeId = type_id[type], memberId;
  member_id[name] = memberId = members.size();

  int align = types[typeId].align;
  if (align > this->align) this->align = align;

  ll addr = 0;
  if (memberId) {
    addr = members[memberId-1].addr;
    addr += types[members[memberId-1].type].size;
  }
  while (addr % align) addr++;

  members.push_back({typeId, addr, name});
  size = addr + types[typeId].size;
  while (size % this->align) size++;
}

int main() {
  types.emplace_back("@");
  init_basic("byte", 0);
  init_basic("short", 1);
  init_basic("int", 2);
  init_basic("long", 3);

  get(n); while (n--) {
    get(op); if (op == 1) {
      String typeName; std::cin >> typeName;
      Type type(typeName);
      get(nMember); while (nMember--) {
        String memberType, memberName;
        std::cin >> memberType >> memberName;
        type.add_member(memberType, memberName);
      }

      type_id[typeName] = types.size();
      types.push_back(type);
      std::cout << type.size << ' ' << type.align << '\n';

    } else if (op == 2) {
      int elementId = global.members.size();
      String elementType, elementName;
      std::cin >> elementType >> elementName;
      global.add_member(elementType, elementName);
      std::cout << global.members[elementId].addr << '\n';

    } else if (op == 3) {
      String expr; std::cin >> expr;
      int curr=0, i=0, m = expr.size();
      ll addr = 0;
      while (i < m) {
        int j=i; while (j < m && expr[j] != '.') j++;
        int nextId = types[curr].member_id[expr.substr(i, j-i)];
        Member &member = types[curr].members[nextId];
        addr += member.addr;
        curr = member.type;
        i = j+1;
      }
      std::cout << addr << '\n';

    } else {
      ll addr; std::cin >> addr;
      int curr = 0;
      String path;
      while (true) {
        int i = 0, m = types[curr].members.size();
        if (!m) { path = "ERR\n"; break; }
        while (i+1<m && types[curr].members[i+1].addr <= addr) i++;

        Member &member = types[curr].members[i];
        if (types[member.type].size <= addr - member.addr) {
          path = "ERR\n"; break;
        }

        curr = member.type;
        addr -= member.addr;
        path += member.name;

        if (1<=curr && curr <= 4) {
          path += '\n'; break;
        } else path += '.';
      }
      std::cout << path;
    }
  }
}
```
