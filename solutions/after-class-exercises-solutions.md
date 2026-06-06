# Advanced Algorithms Homework Solutions

只保留题号、小问编号、核心公式和最简逻辑。

## 作业 06: HeapSort

### 4.37

**a.** 10 个元素时，建堆阶段比较 `9` 次。

**b.** `n` 个元素时，建堆阶段比较 `n - 1` 次。

**c.** 递减数组已经满足最大堆性质，是建堆阶段最好情况。  
每条父子关系最多被确认一次，所以总比较次数为 `n - 1`。

### 4.38

**a.** 递归版 Heapsort 的额外栈空间为：

```text
Theta(log n)
```

**b.** `fixHeap` 迭代版：

```text
fixHeap(E, K, vacant, heapSize):
    while 2 * vacant <= heapSize:
        larger = 2 * vacant
        if larger < heapSize and E[larger].key < E[larger + 1].key:
            larger = larger + 1
        if K.key >= E[larger].key:
            break
        E[vacant] = E[larger]
        vacant = larger
    E[vacant] = K
```

**c.** `fixHeapFast` 迭代版：

```text
fixHeapFast(E, K, vacant, h):
    while h > 1:
        hStop = floor(h / 2)
        vacStop = promote(E, hStop, vacant, h)
        vacParent = floor(vacStop / 2)

        if E[vacParent].key <= K.key:
            E[vacStop] = E[vacParent]
            bubbleUpHeap(E, vacant, K, vacParent)
            return

        vacant = vacStop
        h = hStop

    handle the remaining heap of height 0 or 1 directly
```

高度为 `0` 时直接放入 `K`；高度为 `1` 时只需和较大的孩子比较一次。

**d.** `constructHeap` 迭代版：

```text
constructHeap(E, n):
    for root = floor(n / 2) downto 1:
        K = E[root]
        fixHeap(E, K, root, n)
```

**e.** 最坏比较次数仍为：

```text
Theta(n)
```

理由：所有结点高度之和不超过 `n - 1`。

### 4.39

设 `h(v)` 为结点 `v` 的高度：

```text
sum_v h(v)
= sum_{k >= 1} #{height(v) >= k}
<= sum_{k >= 1} floor(n / 2^k)
<= n - 1
```

所以建堆最坏比较次数至多为：

```text
2(n - 1) = Theta(n)
```

### 4.44

证明：

```text
ceil(lg(floor(h/2) + 1)) + 1 = ceil(lg(h + 1))
```

**情况 1：** `h = 2m + 1`

```text
floor(h/2) + 1 = m + 1
h + 1 = 2(m + 1)
```

所以等式成立。

**情况 2：** `h = 2m`，令 `x = m + 1`。  
若 `r = ceil(lg x)`，则：

```text
2^{r-1} < x <= 2^r
2^r < 2x - 1 < 2^{r+1}
```

所以：

```text
ceil(lg(2x - 1)) = r + 1
```

等式成立。

## 作业 07: Selection

### 5.2

**a.** 比较排序有 `n!` 个可能结果：

```text
2^t >= n!
t >= ceil(lg(n!)) = Omega(n lg n)
```

**b.** 合并两个各含 `n/2` 个元素的有序序列，交错方式为：

```text
C(n, n/2)
```

所以下界为：

```text
ceil(lg C(n, n/2)) = n - Theta(lg n)
```

### 5.4

**a.** `heapFindMax` 自底向上比较，每个内部结点保存子树最大值，所以 `E[1]` 是最大值。

**b.** 输给最大值的元素，都在最大值叶结点到根路径的兄弟结点中。

**c.** 第二大只需在这些“输给最大值”的元素里找最大：

```text
follow max path from root to leaf
compare all sibling losers
return the largest loser
```

### 5.6

**a.** 最坏比较次数：

```text
1 + 2(n - 2) = 2n - 3
```

最坏例子：`1, 2, 3, 4, 5, 6`。

**b.** 平均比较次数：

```text
1 + sum_{i=3}^{n}(1 + 2/i)
= n + 2H_n - 4
```

### 5.8

**a.** Quickselect：

```text
findKth(E, k):
    choose pivot p
    partition into L = {x < p}, G = {x > p}
    if |L| + 1 == k: return p
    if k <= |L|: return findKth(L, k)
    return findKth(G, k - |L| - 1)
```

**b.** 最坏情况每次 pivot 都最偏：

```text
T(n) = T(n - 1) + Theta(n) = Theta(n^2)
```

**c.** 平均递推：

```text
T(n, k) = n - 1
        + (1/n) * sum_{j < k} T(n - j, k - j)
        + (1/n) * sum_{j > k} T(j - 1, k)
```

**d.** 平均运行时间：

```text
Theta(n)
```

### 5.12

偶数 `n`，中位数定义为第 `n/2` 小：

```text
critical comparisons >= n - 1
uncritical comparisons >= n/2 - 1
lower bound = 3n/2 - 2
```

### 5.13

**a. Insertion Sort：** 敌手让每个新元素插到最前，最坏 `Theta(n^2)`。

**b. Quicksort：** 敌手让 pivot 每次极不平衡，最坏 `Theta(n^2)`。

**c. Mergesort：** 敌手让两边交替胜出，每层线性，总计 `Theta(n lg n)`。

**d. Heapsort：** 敌手让元素一路下沉，总计 `Theta(n lg n)`。

**e. Accelerated Heapsort：** 最坏为：

```text
n lg n + Theta(n lg lg n)
```

### 5.14

5 个数 6 次比较找中位数：

```text
1. compare a,b; make a <= b
2. compare c,d; make c <= d
3. compare a,c; make a <= c, discard a
4. compare b,d; discard the larger one
5. find median of the remaining 3 elements using 2 comparisons
```

总计：

```text
4 + 2 = 6
```

### 5.17

**a.** `n = 2` 时，两个比较必要且充分。

**b.** 算法：

```text
while lo < hi:
    mid = floor((lo + hi) / 2)
    if E[mid] < E[mid + 1]:
        lo = mid + 1
    else:
        hi = mid
return lo
```

**c.** 最坏比较次数：

```text
ceil(lg(n + 1))
```

**d.** 若 `n = F_k`，可用 Fibonacci search，比较次数为 `k - 1`。

**e.** 敌手下界可迫使至少：

```text
lg n + 2
```

## 作业 08: HashTable

### 6.1

扩容因子从 2 改成 4：

```text
factor 2: total moving < 2n
factor 4: total moving < 4n/3
```

结论：

- 时间：乘以 4 搬移次数更少，摊还插入仍为 `Theta(1)`。
- 空间：乘以 4 刚扩容后装载率约 `1/4`，比乘以 2 更浪费空间。

### 6.2

**a.** pop 后 `< N/2` 缩到 `N/2`：可能反复扩缩，不能保证常数摊还。

**b.** pop 后 `< N/4` 缩到 `N/4`：可常数摊还，但缩完接近满。

**c.** pop 后 `< N/4` 缩到 `N/2`：较好，缩完装载率约 `1/2`，两边都有缓冲。

**d.** 可改为 `< N/3` 时缩到 `2N/3`，仍有线性缓冲，空间常数更好。

### 6.18

开放寻址三种状态：

```text
EMPTY      never used
OCCUPIED   currently stores key
OBSOLETE   deleted tombstone
```

搜索：

```text
search: stop at EMPTY; skip OBSOLETE
```

插入：

```text
insert: remember first OBSOLETE; keep probing to avoid duplicate;
        insert into first OBSOLETE or first EMPTY
```

删除：

```text
delete: mark found slot as OBSOLETE
```

### 6.19

设：

```text
alpha_C = n / n_C
```

**a. key = 1 word, node = 2 words**

```text
S_C = n_C + 2n = n_C(1 + 2alpha_C)
alpha_O = alpha_C / (1 + 2alpha_C)
```

**b. key = 4 words, node = 5 words**

```text
S_C = n_C + 5n = n_C(1 + 5alpha_C)
alpha_O = 4alpha_C / (1 + 5alpha_C)
```

表：

| `alpha_C` | part a | part b |
|---:|---:|---:|
| 0.25 | 0.167 | 0.444 |
| 0.50 | 0.250 | 0.571 |
| 1.00 | 0.333 | 0.667 |
| 2.00 | 0.400 | 0.727 |

## 作业 09: GTraverse

### 7.12

图 7.30 的边：

```text
A->B, H->A, B->H, B->D, D->F, F->D, D->G, G->F,
E->D, H->E, E->C, C->I, I->J, J->C, J->K, K->I
```

分类规则：

```text
tree       first discovers a white vertex
back       points to a gray ancestor
descendant points to a descendant but is not tree edge
cross      all other finished-branch edges
```

**a. 顶点字母序，邻接表字母序**

```text
tree: A->B, B->D, D->F, D->G, B->H, H->E, E->C, C->I, I->J, J->K
back: H->A, F->D, J->C, K->I
cross: G->F, E->D
```

**b. 顶点逆字母序，邻接表字母序**

```text
tree: K->I, I->J, J->C, H->A, A->B, B->D, D->F, D->G, H->E
back: B->H, F->D, C->I, J->K
cross: G->F, E->D, E->C
```

**c. 顶点字母序，邻接表逆字母序**

```text
tree: A->B, B->H, H->E, E->D, D->G, G->F, E->C, C->I, I->J, J->K
back: H->A, F->D, J->C, K->I
descendant: B->D, D->F
```

**d. 顶点逆字母序，邻接表逆字母序**

```text
tree: K->I, I->J, J->C, H->E, E->D, D->G, G->F, H->A, A->B
back: J->K, C->I, F->D, B->H
descendant: D->F
cross: E->C, B->D
```

### 7.14

根到 `v`、根到 `w` 的路径唯一。  
两条路径的最长公共前缀最后一个顶点就是 `LCA c`。  
从 `c` 到 `v` 和从 `c` 到 `w` 离开 `c` 后走不同孩子，所以无公共边。

### 7.15

若 `active(w) subset active(v)`：

- 若 `v` 是 `w` 后代，则应有 `active(v) subset active(w)`，矛盾。
- 若二者无祖先关系，则活动区间应不相交，矛盾。

所以 `w` 是 `v` 的后代；若是真子集，则是真后代。

### 7.16

```text
dfsSweep(G):
    mark all vertices white
    for each vertex v:
        if v is white:
            dfs(v)

dfs(v):
    mark v gray
    for each edge v->w:
        if w is white:
            output v->w
            dfs(w)
    mark v black
```

若图不连通，输出的是 DFS forest。
