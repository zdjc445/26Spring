# Slides 前端完整模拟卷 07（题目与完整解析）

## 卷面说明

```text
考试时间：120 分钟
总分：100 分
说明：本卷独立覆盖 Slides 中编译器导论、有限自动机、CFG/PDA、二义性和 LL(1)。
所有题目所需数据均在题面中给出。
```

## A 卷题目

### 一、编译流程与中间表示（20 分）

某编译器处理下面程序：

```text
int x;
x = 2 + 3 * 4;
```

现有以下中间结果，顺序被打乱：

```text
① token 序列：int id ; id = num + num * num ;
② AST：Assign(x, Add(2, Mul(3,4)))
③ 带类型 AST：Assign<int>(x:int, Add<int>(2, Mul<int>(3,4)))
④ 三地址码：t1=3*4; t2=2+t1; x=t2
⑤ 目标代码：LOAD/ADD/MUL/STORE 指令序列
```

1. 写出 ① 到 ⑤ 的正确生成顺序及对应编译阶段。
2. 指出哪些阶段属于前端、中端和后端。
3. 说明 AST 与三地址码的结构差异。
4. 说明引入 IR 的两个直接好处。
5. 解释 phase 与 pass 的区别。

### 二、DFA 最小化与等价性（20 分）

给定 DFA：

```text
状态：A, B, C, D, E
字母表：0, 1
初态：A
终态：D, E

状态  0  1
A     B  C
B     B  D
C     B  C
D     B  E
E     B  E
```

1. 写出 DFA 五元组。
2. 使用划分细化法给出每轮状态划分。
3. 写出最小 DFA 的状态和转移表。
4. 写出原 DFA 接受的三个长度不超过 3 的串和三个拒绝串。
5. 说明用乘积自动机检查两个 DFA 等价的判定条件。

### 三、CFG、推导与 PDA（20 分）

给定文法：

```text
S -> a S b | epsilon
```

1. 写出串 `aabb` 的最左推导。
2. 写出串 `aabb` 的最右推导。
3. 写出该语言的集合描述。
4. 设计识别该语言的 PDA，写出状态、栈符号和转移规则。
5. 写出 PDA 处理 `aabb` 时的主要配置变化。

### 四、二义性与 CFL 性质（20 分）

给定文法：

```text
E -> E + E | E * E | id
```

1. 对 `id + id * id` 写出两种不同的最左推导，证明文法二义。
2. 改写文法，使 `*` 优先于 `+`，且二者均左结合。
3. 说明改写后的文法为什么消除了本题中的二义性。
4. 判断：所有正则语言是否都是 CFL。
5. 判断 CFL 对并、连接、Kleene 星、交、补是否封闭。

### 五、LL(1) 预测分析（20 分）

给定文法：

```text
S -> a A | b
A -> c A | d
```

1. 计算 FIRST(S)、FIRST(A)、FOLLOW(S)、FOLLOW(A)。
2. 构造完整预测分析表，空白项写 `error`。
3. 判断文法是否为 LL(1)。
4. 使用非递归预测分析器分析输入 `a c d $`，逐步写出分析栈、剩余输入和动作。
5. 写出输入 `a c c $` 发生错误的具体表项。

## B 完整解析

### 一、编译流程与中间表示解析

正确顺序：

```text
源程序
-> 词法分析：① token 序列
-> 语法分析：② AST
-> 语义分析：③ 带类型 AST
-> 中间代码生成：④ 三地址码
-> 目标代码生成：⑤ 目标代码
```

前端、中端、后端：

```text
前端：词法分析、语法分析、语义分析、中间代码生成。
中端：在 IR 上进行机器无关优化，例如常量折叠后可把 2+3*4 化为 14。
后端：指令选择、寄存器分配、指令调度和目标代码生成。
```

AST 与三地址码：

```text
AST 是树结构，保留表达式的层次、运算优先级和源语言构造。
三地址码是线性指令序列，把复杂表达式拆成每条至多一个主要运算的语句，并显式引入临时变量。
```

IR 的好处：

```text
1. 解耦源语言和目标机器：多个前端可共享同一中端和后端。
2. 提供统一优化载体：数据流分析、SSA 和优化不必直接处理源语言语法。
```

phase 与 pass：

```text
phase 是逻辑功能阶段，例如语法分析或寄存器分配。
pass 是对某种程序表示的一次完整处理或遍历。
一个 phase 可以由多个 pass 实现；一个 pass 也可以合并多个紧密相关的小阶段。
```

### 二、DFA 最小化与等价性解析

DFA 五元组：

```text
Q={A,B,C,D,E}
Sigma={0,1}
q0=A
F={D,E}
delta 由题目转移表给出
```

划分细化：

```text
P0={{D,E},{A,B,C}}

在 {A,B,C} 中：
A: 0->非终态组，1->非终态组
B: 0->非终态组，1->终态组
C: 0->非终态组，1->非终态组
所以分为 {A,C}、{B}。

在 {D,E} 中：
D: 0->{B}，1->{D,E}
E: 0->{B}，1->{D,E}
二者不能再分。

最终 P={{A,C},{B},{D,E}}。
```

令：

```text
X={A,C}
Y={B}
Z={D,E}
```

最小 DFA：

```text
状态  0  1  接受
X     Y  X  否
Y     Y  Z  否
Z     Y  Z  是

初态 X，终态 Z。
```

长度不超过 3 的示例：

```text
接受：01、001、101
拒绝：epsilon、0、11
```

乘积自动机判等价：

```text
从两个初态组成的状态对开始遍历乘积自动机。
如果能到达某个状态对 (p,q)，其中恰好一个属于接受状态，则存在区分串，两个 DFA 不等价。
如果所有可达状态对的接受性都一致，则两个 DFA 等价。
```

### 三、CFG、推导与 PDA 解析

最左推导：

```text
S
=> a S b
=> a a S b b
=> a a epsilon b b
=> aabb
```

该文法每一步只有一个非终结符，因此本题最右推导形式相同：

```text
S
=> a S b
=> a a S b b
=> a a epsilon b b
=> aabb
```

语言：

```text
L={a^n b^n | n>=0}
```

PDA：

```text
状态：q_push, q_pop, q_accept
输入字母表：{a,b}
栈字母表：{Z,A}，Z 为栈底符号

delta(q_push,a,Z)=(q_push,AZ)
delta(q_push,a,A)=(q_push,AA)
delta(q_push,b,A)=(q_pop,epsilon)
delta(q_pop,b,A)=(q_pop,epsilon)
delta(q_push,epsilon,Z)=(q_accept,Z)       ; 接受空串
delta(q_pop,epsilon,Z)=(q_accept,Z)        ; 输入耗尽后接受
```

`aabb` 的配置变化，格式为 `(状态,剩余输入,栈)`：

```text
(q_push,aabb,Z)
|- (q_push,abb,AZ)
|- (q_push,bb,AAZ)
|- (q_pop,b,AZ)
|- (q_pop,epsilon,Z)
|- (q_accept,epsilon,Z)
```

### 四、二义性与 CFL 性质解析

第一种最左推导，对应先做加法：

```text
E
=> E * E
=> E + E * E
=> id + E * E
=> id + id * E
=> id + id * id
```

其语法树含义为 `(id+id)*id`。

第二种最左推导，对应先做乘法：

```text
E
=> E + E
=> id + E
=> id + E * E
=> id + id * E
=> id + id * id
```

其语法树含义为 `id+(id*id)`。同一串有两棵不同语法树，所以原文法二义。

无二义文法：

```text
E -> E + T | T
T -> T * F | F
F -> id
```

`F` 形成基本项，`T` 只处理乘法，`E` 处理加法，因此乘法层级高于加法；左递归使相同运算符左结合。

CFL 性质：

```text
所有正则语言都是 CFL。
CFL 对并、连接、Kleene 星封闭。
CFL 对一般交运算和补运算不封闭。
```

### 五、LL(1) 预测分析解析

FIRST/FOLLOW：

```text
FIRST(S)={a,b}
FIRST(A)={c,d}
FOLLOW(S)={$}
FOLLOW(A)={$}
```

预测分析表：

```text
       a        b       c        d       $
S      S->aA    S->b    error    error   error
A      error    error   A->cA    A->d    error
```

同一表项没有多条产生式，因此文法是 LL(1)。

分析 `a c d $`，栈顶写在右侧：

```text
分析栈   剩余输入   动作
$S       acd$       用 S->aA，压入 A、a
$Aa      acd$       匹配 a
$A       cd$        用 A->cA，压入 A、c
$Ac      cd$        匹配 c
$A       d$         用 A->d，压入 d
$d       d$         匹配 d
$        $          接受
```

输入 `a c c $`：

```text
匹配 a、c、c 后，栈顶仍为 A，而当前输入为 $。
查询 M[A,$]=error，因此在该表项报错。
```
