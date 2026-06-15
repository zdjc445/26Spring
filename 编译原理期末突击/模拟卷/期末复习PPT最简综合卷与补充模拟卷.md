# 期末复习 PPT 最简综合卷与补充模拟卷

## 使用说明

本文件基于以下材料整理：

- `期末复习PPT/期末复习一--词法解析-语法解析LL(1)文法(1).pdf`
- `期末复习PPT/期末复习二--语法解析LR(1)文法(1).pdf`
- `期末复习PPT/期末复习三--语义分析(1).pdf`
- `期末复习PPT/期末复习四--中间代码生成(1).pdf`
- `编译原理期末突击/模拟卷/期末复习PPT专项模拟卷合集-题目与详解.md`

整理原则：

```text
1. 最简综合卷保留每类核心题型一次，去掉原三套卷中的重复训练。
2. 补充模拟卷只覆盖原合集没有充分出题的 PPT 重点。
3. 每套题后给出简明答案，便于快速背卷面写法。
```

# 最简综合模拟卷

## A 卷题目

### 一、词法分析：正则表达式、NFA、DFA、最小化（20 分）

给定正则表达式：

```text
(a|b)*abb
```

1. 使用 Thompson 构造法给出等价 NFA。
2. 使用子集构造法构造 DFA，写出每个 DFA 状态对应的 NFA 状态集合。
3. 对 DFA 进行最小化。

### 二、LL(1) 分析（20 分）

文法：

```text
E -> E + T | T
T -> T * F | F
F -> ( E ) | id
```

1. 消除直接左递归。
2. 写出消除左递归后文法的 FIRST 集和 FOLLOW 集。
3. 构造预测分析表。
4. 判断消除左递归后的文法是否为 LL(1)。

### 三、LR(0) 项集族与 SLR 分析表（20 分）

文法：

```text
S' -> S
S  -> C C
C  -> c C | d
```

1. 构造规范 LR(0) 项集族。
2. 写出 GOTO 转移。
3. 写出 FOLLOW 集。
4. 写出 SLR 分析表中的 ACTION/GOTO 条目。

### 四、语义分析：S 属性与 L 属性（20 分）

1. 对下面声明文法设计 L 属性 SDD，把每个 `id` 加入符号表并记录类型。

```text
D  -> T L
T  -> int | float
L  -> id L'
L' -> , id L' | epsilon
```

2. 对下面表达式文法设计 S 属性 SDD，使属性 `code` 表示后缀表达式，并计算 `3 + 4 + 5` 的 `code`。

```text
E -> E + T | T
T -> num
```

### 五、中间代码：短路求值、回填、SSA（20 分）

1. 为下面语句生成带标签的三地址码，要求体现短路求值和回填后的跳转目标。

```text
if ((x < y) && (y < z) || (x == z))
  a = 2;
else
  a = 3;
```

2. 把下面循环翻译成 SSA 风格的三地址码，要求在循环头写出必要的 phi 函数。

```text
i = 0;
s = 0;
while (i < n) {
  s = s + i;
  i = i + 1;
}
```

## B 简明答案

### 一、词法分析答案

Thompson NFA 边表：

```text
q0 --epsilon--> q2
q0 --epsilon--> q1
q2 --epsilon--> q4
q2 --epsilon--> q6
q4 --a--> q5
q6 --b--> q7
q5 --epsilon--> q3
q7 --epsilon--> q3
q3 --epsilon--> q2
q3 --epsilon--> q1
q1 --epsilon--> q8
q8 --a--> q9
q9 --epsilon--> q10
q10 --b--> q11
q11 --epsilon--> q12
q12 --b--> q13
```

```text
起点：q0
终点：q13
```

子集构造：

```text
D0 = {q0,q1,q2,q4,q6,q8}
  on a -> D1
  on b -> D2
  终态：否

D1 = {q1,q2,q3,q4,q5,q6,q8,q9,q10}
  on a -> D1
  on b -> D3
  终态：否

D2 = {q1,q2,q3,q4,q6,q7,q8}
  on a -> D1
  on b -> D2
  终态：否

D3 = {q1,q2,q3,q4,q6,q7,q8,q11,q12}
  on a -> D1
  on b -> D4
  终态：否

D4 = {q1,q2,q3,q4,q6,q7,q8,q13}
  on a -> D1
  on b -> D2
  终态：是
```

最小化：

```text
G0 = {D0,D2}
G1 = {D1}
G2 = {D3}
G3 = {D4}

G0 --a--> G1, G0 --b--> G0
G1 --a--> G1, G1 --b--> G2
G2 --a--> G1, G2 --b--> G3
G3 --a--> G1, G3 --b--> G0

G0 是初态，G3 是终态。
```

### 二、LL(1) 答案

消除直接左递归：

```text
E  -> T E'
E' -> + T E' | epsilon
T  -> F T'
T' -> * F T' | epsilon
F  -> ( E ) | id
```

FIRST：

```text
FIRST(E)  = { (, id }
FIRST(E') = { +, epsilon }
FIRST(T)  = { (, id }
FIRST(T') = { *, epsilon }
FIRST(F)  = { (, id }
```

FOLLOW：

```text
FOLLOW(E)  = { ), $ }
FOLLOW(E') = { ), $ }
FOLLOW(T)  = { +, ), $ }
FOLLOW(T') = { +, ), $ }
FOLLOW(F)  = { *, +, ), $ }
```

预测分析表：

```text
M[E,  ( ] = E -> T E'
M[E, id ] = E -> T E'

M[E', + ] = E' -> + T E'
M[E', ) ] = E' -> epsilon
M[E', $ ] = E' -> epsilon

M[T,  ( ] = T -> F T'
M[T, id ] = T -> F T'

M[T', * ] = T' -> * F T'
M[T', + ] = T' -> epsilon
M[T', ) ] = T' -> epsilon
M[T', $ ] = T' -> epsilon

M[F,  ( ] = F -> ( E )
M[F, id ] = F -> id
```

结论：

```text
预测分析表没有多重入口，因此消除左递归后的文法是 LL(1)。
```

### 三、LR(0)/SLR 答案

规范 LR(0) 项集族：

```text
I0:
S' -> . S
S  -> . C C
C  -> . c C
C  -> . d

I1:
S' -> S .

I2:
S -> C . C
C -> . c C
C -> . d

I3:
C -> c . C
C -> . c C
C -> . d

I4:
C -> d .

I5:
S -> C C .

I6:
C -> c C .
```

GOTO：

```text
GOTO(I0, S) = I1
GOTO(I0, C) = I2
GOTO(I0, c) = I3
GOTO(I0, d) = I4

GOTO(I2, C) = I5
GOTO(I2, c) = I3
GOTO(I2, d) = I4

GOTO(I3, C) = I6
GOTO(I3, c) = I3
GOTO(I3, d) = I4
```

FOLLOW：

```text
FOLLOW(S) = { $ }
FOLLOW(C) = { c, d, $ }
```

SLR 表条目：

```text
ACTION[0,c] = s3
ACTION[0,d] = s4
GOTO[0,S] = 1
GOTO[0,C] = 2

ACTION[1,$] = acc

ACTION[2,c] = s3
ACTION[2,d] = s4
GOTO[2,C] = 5

ACTION[3,c] = s3
ACTION[3,d] = s4
GOTO[3,C] = 6

ACTION[4,c] = r(C -> d)
ACTION[4,d] = r(C -> d)
ACTION[4,$] = r(C -> d)

ACTION[5,$] = r(S -> C C)

ACTION[6,c] = r(C -> c C)
ACTION[6,d] = r(C -> c C)
ACTION[6,$] = r(C -> c C)
```

### 四、语义分析答案

L 属性 SDD：

```text
D -> T L
  L.in = T.type

T -> int
  T.type = int

T -> float
  T.type = float

L -> id L'
  addType(id.name, L.in)
  L'.in = L.in

L' -> , id L'1
  addType(id.name, L'.in)
  L'1.in = L'.in

L' -> epsilon
  不执行动作
```

这是 L 属性的原因：

```text
L.in 来自左侧的 T.type。
L'.in 来自父结点 L 或 L'。
继承属性只依赖父结点或左侧兄弟结点，不依赖右侧兄弟结点。
```

S 属性 SDD：

```text
E -> E1 + T
  E.code = E1.code || T.code || "+"

E -> T
  E.code = T.code

T -> num
  T.code = num.lexeme
```

计算：

```text
3 + 4 + 5 按左结合为 ((3 + 4) + 5)

3 + 4 的 code = 3 4 +
(3 + 4) + 5 的 code = 3 4 + 5 +

最终 E.code = 3 4 + 5 +
```

### 五、中间代码答案

布尔表达式回填后的三地址码：

```text
1: if x < y goto 3
2: goto 5
3: if y < z goto 7
4: goto 5
5: if x == z goto 7
6: goto 9
7: a = 2
8: goto 10
9: a = 3
10:
```

SSA：

```text
entry:
i1 = 0
s1 = 0
goto L_header

L_header:
i2 = phi(i1, i3)
s2 = phi(s1, s3)
t1 = i2 < n
if t1 goto L_body else L_exit

L_body:
s3 = s2 + i2
i3 = i2 + 1
goto L_header

L_exit:
```

# 补充模拟卷 01：自顶向下分析与 LR 概念补缺

## A 卷题目

### 一、递归下降、回溯与左公因子（20 分）

文法：

```text
S -> a A | a B
A -> c
B -> d
```

1. 说明该文法在递归下降分析中为什么可能需要回溯。
2. 提取左公因子。
3. 判断提取左公因子后的文法是否适合构造 LL(1) 预测分析表。

### 二、二义性文法与预测分析表（20 分）

文法：

```text
E -> E + E | E * E | id
```

1. 说明该文法为什么不适合直接构造 LL(1) 预测分析表。
2. 指出它至少存在的两个问题。
3. 简述通常需要怎样处理。

### 三、句柄与归约序列（20 分）

文法：

```text
S -> A B
A -> a
B -> b
```

对输入串：

```text
a b
```

1. 写出自底向上的归约序列。
2. 说明该归约序列与最右推导的关系。

### 四、规约-规约冲突（20 分）

某 LR(0) 状态中有：

```text
A -> id .
B -> id .
```

1. 说明为什么这是规约-规约冲突。
2. 若 `FOLLOW(A) = { ; }`，`FOLLOW(B) = { ) }`，说明 SLR 如何处理。
3. 若 `FOLLOW(A)` 和 `FOLLOW(B)` 有公共符号，会发生什么。

### 五、内核项、非内核项与 SLR 弱点（20 分）

回答：

1. 什么是 LR(0) 项集中的内核项。
2. 什么是非内核项。
3. SLR 为什么比 LR(0) 强。
4. SLR 为什么仍然有弱点。

## B 简明答案

### 一、递归下降、回溯与左公因子答案

原文法：

```text
S -> a A | a B
```

两条 `S` 的产生式右部都以 `a` 开头。递归下降分析看到输入第一个符号 `a` 时，不能只根据这一个符号判断应该选择 `S -> a A` 还是 `S -> a B`，因此可能先选错再回溯。

提取左公因子：

```text
S  -> a S'
S' -> A | B
A  -> c
B  -> d
```

也可以直接写成：

```text
S  -> a S'
S' -> c | d
```

判断：

```text
FIRST(c) = { c }
FIRST(d) = { d }
```

两条 `S'` 规则右部的 FIRST 集不相交，因此提取左公因子后可以构造 LL(1) 预测分析表。

### 二、二义性文法与预测分析表答案

该文法：

```text
E -> E + E | E * E | id
```

不适合直接构造 LL(1) 预测分析表，原因：

```text
1. 有直接左递归：E -> E + E 和 E -> E * E。
2. 没有体现 + 和 * 的优先级与结合性，表达式可能有多棵语法树。
```

例如：

```text
id + id * id
```

可能先结合 `+`，也可能先结合 `*`。通常需要：

```text
1. 改写文法，消除二义性。
2. 消除左递归。
3. 必要时提取左公因子。
4. 再计算 FIRST、FOLLOW 并构造预测分析表。
```

### 三、句柄与归约序列答案

输入：

```text
a b
```

自底向上归约：

```text
a b
=> A b
=> A B
=> S
```

每一步都是把某个产生式右部归约为左部：

```text
a => A
b => B
A B => S
```

自底向上分析的归约序列等价于某个最右推导的逆过程。

对应最右推导：

```text
S => A B => A b => a b
```

### 四、规约-规约冲突答案

状态中有两个完成项：

```text
A -> id .
B -> id .
```

在 LR(0) 中，完成项会在所有终结符上放置规约动作，因此同一状态可能同时要求：

```text
reduce A -> id
reduce B -> id
```

这就是规约-规约冲突。

若：

```text
FOLLOW(A) = { ; }
FOLLOW(B) = { ) }
```

SLR 只在 FOLLOW 集对应的终结符上填规约：

```text
ACTION[状态, ;] = r(A -> id)
ACTION[状态, )] = r(B -> id)
```

如果 `FOLLOW(A)` 和 `FOLLOW(B)` 有公共符号，那么公共符号对应的 ACTION 表项仍然会同时出现两个规约动作，冲突仍然存在。

### 五、内核项、非内核项与 SLR 弱点答案

内核项：

```text
1. 初始项 S' -> . S。
2. 所有点不在产生式最左端的 LR(0) 项。
```

非内核项：

```text
除了 S' -> . S 之外，点在产生式最左端的项。
```

SLR 比 LR(0) 强：

```text
LR(0) 对完成项不看输入符号，容易在所有终结符上放置规约。
SLR 使用 FOLLOW 集限制规约，只在 FOLLOW(A) 上对 A -> alpha . 规约。
```

SLR 的弱点：

```text
FOLLOW 集只和非终结符有关，不区分具体 LR 状态中的上下文。
FOLLOW 集过大时，仍可能在不该规约的位置放置规约动作。
因此有些冲突需要 LR(1) 或 LALR(1) 才能处理。
```

# 补充模拟卷 02：语义分析、AST、符号表补缺

## A 卷题目

### 一、依赖图与拓扑求值顺序（20 分）

考虑语义规则：

```text
E -> E1 + T
  E.val = E1.val + T.val

E -> T
  E.val = T.val

T -> num
  T.val = num.lexeme
```

对输入：

```text
3 + 4
```

1. 写出主要属性依赖关系。
2. 写出一种合法的属性求值顺序。
3. 计算最终 `E.val`。

### 二、受控副作用（20 分）

考虑声明翻译：

```text
D -> T id
T -> int
```

语义动作：

```text
addType(id.name, T.type)
```

1. 说明该语义动作的副作用是什么。
2. 为什么该动作必须在 `T.type` 已经确定之后执行。
3. 写出受控副作用应满足的基本要求。

### 三、CST 与 AST（20 分）

表达式：

```text
a + b * c
```

1. 说明 CST 和 AST 的区别。
2. 画出该表达式的 AST。
3. 说明为什么后续语义分析更常使用 AST。

### 四、符号表与作用域（20 分）

考虑程序片段：

```text
{
  int x;
  {
    float x;
  }
}
```

1. 说明进入和退出代码块时符号表栈如何变化。
2. 在内层代码块中查找 `x` 时应得到哪个声明。
3. 退出内层代码块后查找 `x` 时应得到哪个声明。

### 五、类型信息与标识符绑定（20 分）

回答：

1. 符号表通常记录哪些信息。
2. AST 中的标识符结点为什么要绑定符号表条目。
3. 符号表对类型检查有什么作用。

## B 简明答案

### 一、依赖图与拓扑求值顺序答案

对 `3 + 4`，主要属性依赖：

```text
T1.val 依赖 num(3).lexeme
E1.val 依赖 T1.val
T2.val 依赖 num(4).lexeme
E.val  依赖 E1.val 和 T2.val
```

一种合法求值顺序：

```text
1. T1.val = 3
2. E1.val = T1.val = 3
3. T2.val = 4
4. E.val = E1.val + T2.val = 3 + 4 = 7
```

最终：

```text
E.val = 7
```

### 二、受控副作用答案

副作用：

```text
addType(id.name, T.type) 会修改符号表。
```

必须在 `T.type` 确定后执行，因为符号表中需要记录标识符的正确类型。如果 `T.type` 尚未计算，就无法把正确类型写入符号表。

受控副作用要求：

```text
1. 执行位置明确。
2. 执行顺序确定。
3. 不破坏属性依赖关系。
4. 不引入依赖环。
5. 不导致重复插入或错误类型绑定。
```

### 三、CST 与 AST 答案

CST：

```text
具体语法树，完整反映文法推导过程，包含括号、辅助非终结符等语法细节。
```

AST：

```text
抽象语法树，去掉冗余语法细节，保留表达式和语句的核心结构。
```

`a + b * c` 的 AST：

```text
      +
     / \
    a   *
       / \
      b   c
```

后续语义分析更常使用 AST，因为 AST 更直接表示程序语义结构，便于类型检查、符号绑定、优化和代码生成。

### 四、符号表与作用域答案

进入外层代码块：

```text
push 外层符号表
插入 x : int
```

进入内层代码块：

```text
push 内层符号表
插入 x : float
```

内层代码块中查找 `x`：

```text
先查内层符号表，得到 x : float
```

退出内层代码块：

```text
pop 内层符号表
```

此后查找 `x`：

```text
得到外层的 x : int
```

退出外层代码块：

```text
pop 外层符号表
```

### 五、类型信息与标识符绑定答案

符号表通常记录：

```text
名字
类型
作用域
存储位置
参数信息
函数信息
```

AST 中标识符结点绑定符号表条目的原因：

```text
同名标识符可能出现在不同作用域中。
绑定后可以明确该标识符对应哪一个声明。
```

符号表对类型检查的作用：

```text
通过符号表查出变量、函数、表达式的类型，
判断赋值、运算、函数调用等是否类型匹配。
```

# 补充模拟卷 03：中间代码表示补缺

## A 卷题目

### 一、四元式、三元式、间接三元式（25 分）

将表达式：

```text
x = (a + b) * (c - d) + e
```

翻译为：

1. 普通三地址码。
2. 四元式。
3. 三元式。
4. 间接三元式。

### 二、函数调用与参数传递（15 分）

将下面语句翻译为三地址码：

```text
y = f(a, b + c)
```

要求体现 `param` 和 `call`。

### 三、数组访问与地址计算（20 分）

假设 `int` 宽度为 4，将下面语句翻译为三地址码：

```text
val = a[i]
a[i] = y
```

要求体现下标地址计算。

### 四、fall-through 与减少冗余跳转（20 分）

为下面语句生成三地址码，尽量利用顺序执行减少冗余 `goto`：

```text
if (x < 100 || x > 200 && x != y)
  x = 0;
```

说明 `fall-through` 的含义。

### 五、do-while、break、continue（20 分）

1. 为下面语句生成三地址码：

```text
do {
  a = a + 1;
} while (a < 10 && b > 0);
```

2. 说明 `break` 和 `continue` 在循环中的跳转目标。

## B 简明答案

### 一、四元式、三元式、间接三元式答案

普通三地址码：

```text
t1 = a + b
t2 = c - d
t3 = t1 * t2
t4 = t3 + e
x = t4
```

四元式：

```text
(+, a, b, t1)
(-, c, d, t2)
(*, t1, t2, t3)
(+, t3, e, t4)
(=, t4, -, x)
```

三元式：

```text
0: (+, a, b)
1: (-, c, d)
2: (*, (0), (1))
3: (+, (2), e)
4: (=, (3), x)
```

间接三元式：

三元式表：

```text
0: (+, a, b)
1: (-, c, d)
2: (*, (0), (1))
3: (+, (2), e)
4: (=, (3), x)
```

指针表：

```text
p0 -> 0
p1 -> 1
p2 -> 2
p3 -> 3
p4 -> 4
```

### 二、函数调用与参数传递答案

```text
t1 = b + c
param a
param t1
t2 = call f, 2
y = t2
```

说明：

```text
先计算实参表达式 b + c。
再依次传递参数。
call f, 2 表示调用 f，参数个数为 2。
```

### 三、数组访问与地址计算答案

`val = a[i]`：

```text
t1 = i * 4
t2 = a + t1
val = *t2
```

`a[i] = y`：

```text
t3 = i * 4
t4 = a + t3
*t4 = y
```

说明：

```text
i * 4 计算偏移量。
a + 偏移量得到元素地址。
*地址 表示访问该地址中的值。
```

### 四、fall-through 与减少冗余跳转答案

由于 `&&` 优先级高于 `||`，条件按：

```text
x < 100 || (x > 200 && x != y)
```

处理。

一种三地址码：

```text
if x < 100 goto L_then
if x <= 200 goto L_next
if x == y goto L_next

L_then:
x = 0

L_next:
```

说明：

```text
x < 100 为真时直接进入 then。
x < 100 为假时继续检查 x > 200 && x != y。
若 x <= 200，则 x > 200 为假，直接到 L_next。
若 x == y，则 x != y 为假，直接到 L_next。
其余情况顺序落入 L_then。
```

`fall-through` 的含义：

```text
不显式生成 goto，而是让控制流自然顺序执行到下一条指令。
```

### 五、do-while、break、continue 答案

三地址码：

```text
L_body:
t1 = a + 1
a = t1

if a < 10 goto L_check2
goto L_exit

L_check2:
if b > 0 goto L_body
goto L_exit

L_exit:
```

说明：

```text
do-while 是后测试循环，循环体至少执行一次。
a < 10 为假时退出。
a < 10 为真时才检查 b > 0。
b > 0 为真时回到循环体，否则退出。
```

`break` 和 `continue`：

```text
break 跳到循环出口。
continue 跳到本轮循环结束后的下一次条件检查位置。
```

对 `while`：

```text
continue 通常跳到循环头条件判断处。
break 跳到循环后的出口标签。
```

对 `do-while`：

```text
continue 跳到循环底部的条件判断处。
break 跳到循环后的出口标签。
```
