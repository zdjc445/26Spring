# Advanced Algorithms After-Class Exercise Solutions

本文件根据三个 PPT 末页的 `Home Assignment` 题号整理：

- `06-HeapSort.ppt`: 4.37, 4.38, 4.39, 4.44
- `07-Selection.ppt`: 5.2, 5.4, 5.6, 5.8, 5.12, 5.13, 5.14, 5.17
- `09-GTraverse.ppt`: 7.12, 7.14, 7.15, 7.16

## 06-HeapSort

### 4.37

题意：用普通 Heapsort（不是 Accelerated Heapsort）把一个严格递减数组排成递增序，分析建堆阶段的比较次数。

1. 若有 10 个元素，建堆阶段做 `9` 次关键字比较。
2. 若有 `n` 个元素，建堆阶段做 `n - 1` 次关键字比较。
3. 递减数组已经满足最大堆性质。`constructHeap` 对每个内部结点调用 `fixHeap` 时都会立刻停止：有两个孩子的结点只需比较两个孩子并再与根比较一次，只有一个孩子的结点只需一次比较。所有内部结点合计正好是 `n - 1` 次，因此这是建堆阶段的最好情况。

### 4.38

1. 递归版 Heapsort 的额外栈空间为 `Θ(log n)`，因为 `fixHeap` 最深沿堆高递归，而堆高为 `⌊lg n⌋`。

2. `fixHeap` 的迭代版：

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

3. `fixHeapFast` 的迭代版可把递归调用改成更新 `vacant` 和当前子堆高度 `h` 的循环：

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

    process the remaining heap of height 0 or 1 directly
```

4. `constructHeap` 的迭代版：

```text
constructHeap(E, n):
    for root = floor(n / 2) downto 1:
        K = E[root]
        fixHeap(E, K, root, n)
```

若使用 `fixHeapFast`，循环结构相同，只需给每个 `root` 传入以该结点为根的子堆高度。

5. 迭代版 `constructHeap` 的最坏情况仍为 `Θ(n)` 次比较。因为每个结点最多下沉其子树高度，所有结点高度之和不超过 `n - 1`，而每层最多常数次比较。

### 4.39

记 `h(v)` 为结点 `v` 的高度。所有结点高度之和可写成：

```text
sum_v h(v) = sum_{k >= 1} #{高度至少为 k 的结点}
```

在一个含 `n` 个结点的二叉堆中，高度至少为 `k` 的结点数至多为 `⌊n / 2^k⌋`，因为每这样的结点下面至少对应一个相隔 `k` 层的后代区域。因此：

```text
sum_v h(v) <= sum_{k >= 1} floor(n / 2^k) <= n - 1
```

所以建堆阶段的最坏比较次数至多为所有结点高度之和的两倍，即不超过 `2n - 2`，从而为 `Θ(n)`。

### 4.44

证明：

```text
ceil(lg(floor(h / 2) + 1)) + 1 = ceil(lg(h + 1)),  h >= 1
```

分奇偶讨论。

若 `h = 2m + 1`，则：

```text
floor(h / 2) + 1 = m + 1
h + 1 = 2m + 2 = 2(m + 1)
```

所以：

```text
ceil(lg(m + 1)) + 1 = ceil(lg(2(m + 1))) = ceil(lg(h + 1))
```

若 `h = 2m`，令 `x = m + 1`。左边为 `ceil(lg x) + 1`，右边为 `ceil(lg(2x - 1))`。设 `r = ceil(lg x)`，则 `2^{r-1} < x <= 2^r`。因为 `x` 为整数，`2x - 1 > 2^r` 且 `2x - 1 < 2^{r+1}`，所以 `ceil(lg(2x - 1)) = r + 1`。等式成立。

## 07-Selection

### 5.2

1. 对比较排序，敌手策略可以维护“仍与所有回答一致的排列集合”。每次算法比较两个元素时，敌手选择能保留更多可能排列的回答。一次比较最多把候选排列数减半，因此若要唯一确定排序结果，必须有：

```text
2^t >= n!
t >= ceil(lg(n!))
```

这与基于决策树的排序下界一致，即 `Ω(n lg n)`。

2. 对合并两个各含 `n/2` 个元素的有序序列，可能输出只对应于两列元素的交错方式，共有：

```text
C(n, n/2)
```

种。用同样的“保留更多交错方式”的敌手策略，可得比较次数下界：

```text
ceil(lg C(n, n/2)) = n - Θ(lg n)
```

这个下界比合并问题常见的 `n - 1` 最坏情况比较下界略弱，但同样说明线性级别的比较不可避免。

### 5.4

1. `heapFindMax` 把原始元素放在 `E[n]` 到 `E[2n - 1]`，再自底向上把每对孩子的较大者复制到父结点。每个内部结点保存其子树中的最大值，因此最后 `E[1]` 是全局最大值。

2. 想知道哪些元素输给了最大值，只需从保存最大值的叶结点沿父指针一路走到根。路径上每一层的兄弟结点就是在锦标赛中直接输给最大值的元素。

3. 找第二大元素：

```text
secondLargestAfterHeapFindMax(E, n):
    maxKey = E[1]
    i = 1
    second = -infinity

    while i < n:
        left = 2 * i
        right = 2 * i + 1

        if E[left].key == maxKey:
            second = max(second, E[right])
            i = left
        else:
            second = max(second, E[left])
            i = right

    return second
```

这里假设关键字互异；若允许重复，应额外保存元素身份而不只比较关键字。

### 5.6

1. 最坏情况比较次数为：

```text
1 + 2(n - 2) = 2n - 3
```

例如 `n = 6` 时输入 `1, 2, 3, 4, 5, 6`。初始化后，每个新元素都先大于 `second`，又大于 `max`，所以每轮做两次比较。

2. 平均比较次数：

第 `i` 个元素（`i >= 3`）总要先与 `second` 比较一次；只有当它是当前前 `i` 个元素中的最大或第二大时，才会再与 `max` 比较一次。该概率为 `2 / i`。因此期望比较次数为：

```text
1 + sum_{i=3}^{n} (1 + 2/i)
= n - 1 + 2(H_n - 3/2)
= n + 2H_n - 4
```

其中 `H_n` 是第 `n` 个调和数。

### 5.8

1. Quickselect 形式的 `findKth`：

```text
findKth(E, k):
    choose a pivot p
    partition E into L = {x < p}, G = {x > p}

    if |L| + 1 == k:
        return p
    else if k <= |L|:
        return findKth(L, k)
    else:
        return findKth(G, k - |L| - 1)
```

2. 找中位数时，若每次 pivot 都是当前子数组的最小或最大元素，则递归规模每次只减少 1：

```text
T(n) = T(n - 1) + Θ(n) = Θ(n^2)
```

3. 若 pivot 在 `n` 个位置中等概率出现，找第 `k` 小元素的平均时间满足：

```text
T(n, k) = n - 1
          + (1/n) * sum_{j < k} T(n - j, k - j)
          + (1/n) * sum_{j > k} T(j - 1, k)
```

其中 `j` 是 pivot 的秩。

4. 平均运行时间为 `Θ(n)`。直观上，每轮分区花线性时间，而随机 pivot 使期望递归子问题规模按常数比例下降；标准归纳可证明 `T(n) <= cn`。

### 5.12

若 `n` 为偶数，且把中位数定义为第 `n/2` 小元素，则中位数以下有 `n/2 - 1` 个元素，中位数以上有 `n/2` 个元素。

在敌手下界证明中，仍至少需要 `n - 1` 次关键比较来证明除中位数外的每个元素在中位数哪一侧。同时，敌手可以强迫至少：

```text
n/2 - 1
```

次非关键比较。因此偶数情形的下界为：

```text
(n - 1) + (n/2 - 1) = 3n/2 - 2
```

即：任何基于比较的算法在最坏情况下至少要做 `3n/2 - 2` 次比较。

### 5.13

1. Insertion Sort：比较通常非常不均衡。敌手可让每个新元素都插到当前有序前缀最前端，迫使每轮扫描整个前缀，达到 `Θ(n^2)` 最坏情况。
2. Quicksort：若 pivot 选择不受保护，敌手可让每次划分都极不平衡，使递归规模变成 `n - 1`，达到 `Θ(n^2)`。随机化 pivot 可以避免固定输入上的这种敌手。
3. Mergesort：合并时的比较通常较接近“双方都有信息量”。敌手可以让两个子序列交替胜出，使每次合并用满线性比较次数，但整体仍为 `Θ(n lg n)`。
4. Heapsort：`fixHeap` 中敌手可让待下沉元素一路沉到叶子，使每次删除最大值花 `Θ(lg n)`，总计 `Θ(n lg n)`。
5. Accelerated Heapsort：它减少了普通 `fixHeap` 中逐层反复比较待插入元素的开销。敌手仍可造成接近每层一次的主路径比较和 `Θ(lg lg n)` 的修正开销；课件中的最坏情况为 `n lg n + Θ(n lg lg n)`。

### 5.14

用 6 次比较找 5 个元素的中位数。设元素为 `a, b, c, d, e`。

1. 比较 `a, b`，重命名使 `a <= b`。
2. 比较 `c, d`，重命名使 `c <= d`。
3. 比较 `a, c`。若 `c < a`，交换两组标签，使仍有 `a <= c`。此时 `a` 不可能是中位数，可丢弃。
4. 比较 `b, d`。
5. 若 `b <= d`，则 `d` 不可能是中位数，剩下只需求 `b, c, e` 的中位数。
6. 若 `d < b`，则 `b` 不可能是中位数，剩下只需求 `c, d, e` 的中位数。

三元素中位数可在 2 次比较内得到。因此总比较次数最多为 `4 + 2 = 6`。

### 5.17

设数组 `E[0..n]` 是 unimodal：先严格递增到 `M`，再严格递减。

1. 当 `n = 2` 时有三个元素 `E[0], E[1], E[2]`。比较 `E[0]` 与 `E[1]`、`E[1]` 与 `E[2]` 两次即可确定 `M`。一次比较不够，因为一次比较后至少还有两个峰值位置仍可能成立。

2. 一个二分式算法：

```text
findPeak(E, lo, hi):
    while lo < hi:
        mid = floor((lo + hi) / 2)
        if E[mid] < E[mid + 1]:
            lo = mid + 1
        else:
            hi = mid
    return lo
```

3. 每次比较都把候选峰值区间缩小到至多一半，因此最坏比较次数为：

```text
ceil(lg(n + 1))
```

这是 `o(n)`。

4. 若 `n = F_k`，可使用 Fibonacci search。维护一个已知包含 `M` 的区间，其长度按 Fibonacci 数记为 `F_m`。在区间内选择两个 Fibonacci 比例的探测点，比较它们；若左探测点较小，峰值在右侧，否则在左侧。下一轮保留一个旧探测点，只新增一个探测点，于是参数从 `m` 降到 `m - 1`。从 `F_k` 开始，经过 `k - 1` 次比较即可把区间缩到一个位置。

5. 敌手下界思路：维护一个仍可能包含峰值的活动区间。对算法提出的任意比较，敌手选择能保留更多峰值候选位置的回答，并保持区间两端仍可被构造成单峰数组。每次比较最多使活动区间按常数因子缩小，并且还必须额外确定峰值相对两侧的上升/下降边界信息。由此可强迫至少 `lg n + 2` 次比较（`n >= 4`），说明该问题比普通有序数组搜索略难。

## 09-GTraverse

### 7.12

图 7.30 的有向边按图读为：

```text
A->B, H->A, B->H, B->D, D->F, F->D, D->G, G->F,
E->D, H->E, E->C, C->I, I->J, J->C, J->K, K->I
```

分类中，`tree` 为树边，`back` 为回边，`descendant` 为指向后代但非树边的边，`cross` 为交叉边。

1. 顶点数组按字母序，邻接表按字母序：

```text
A->B tree
H->A back
B->H tree
B->D tree
D->F tree
F->D back
D->G tree
G->F cross
E->D cross
H->E tree
E->C tree
C->I tree
I->J tree
J->C back
J->K tree
K->I back
```

2. 顶点数组按逆字母序，邻接表按字母序：

```text
A->B tree
H->A tree
B->H back
B->D tree
D->F tree
F->D back
D->G tree
G->F cross
E->D cross
H->E tree
E->C cross
C->I back
I->J tree
J->C tree
J->K back
K->I tree
```

3. 顶点数组按字母序，邻接表按逆字母序：

```text
A->B tree
H->A back
B->H tree
B->D descendant
D->F descendant
F->D back
D->G tree
G->F tree
E->D tree
H->E tree
E->C tree
C->I tree
I->J tree
J->C back
J->K tree
K->I back
```

4. 顶点数组按逆字母序，邻接表按逆字母序：

```text
A->B tree
H->A tree
B->H back
B->D cross
D->F descendant
F->D back
D->G tree
G->F tree
E->D tree
H->E tree
E->C cross
C->I back
I->J tree
J->C tree
J->K back
K->I tree
```

### 7.14

设有根为 `r` 的同一棵有向树，且 `v` 与 `w` 互不为祖先/后代。因为树中从根到任一顶点的路径唯一，所以根到 `v` 的路径和根到 `w` 的路径有一个最长公共前缀。令这个公共前缀的最后一个顶点为 `c`。

则 `c` 同时是 `v` 和 `w` 的祖先；从 `c` 到 `v`、从 `c` 到 `w` 的两条树路径在离开 `c` 后走向不同孩子，因此没有公共边。这个 `c` 就是 `v` 与 `w` 的 least common ancestor。

### 7.15

证明 Theorem 7.1 的第 3 项：若 `active(w) ⊆ active(v)`，则 `w` 是 `v` 的后代；若为真子集，则是 proper descendant。

反证。若 `w` 不是 `v` 的后代，则只有两种可能：

1. `v` 是 `w` 的真后代。此时由 DFS 活动区间的嵌套性质，`active(v) ⊂ active(w)`，与 `active(w) ⊆ active(v)` 矛盾。
2. `v` 和 `w` 没有祖先/后代关系。此时二者活动区间应当互不相交，也与 `active(w) ⊆ active(v)` 矛盾。

因此 `w` 必为 `v` 的后代。若 `active(w) ⊂ active(v)`，则 `w != v`，所以 `w` 是 `v` 的真后代。

### 7.16

在 DFS skeleton 中，只要发现一条通向白色顶点的边，就把它加入输出列表；这条边正是 DFS tree edge。

```text
dfsSweep(G):
    mark every vertex white
    treeEdges = empty list

    for each vertex v in G:
        if v is white:
            dfs(G, v, treeEdges)

    return treeEdges

dfs(G, v, treeEdges):
    mark v gray

    for each edge v->w in adjacency list of v:
        if w is white:
            append v->w to treeEdges
            dfs(G, w, treeEdges)

    mark v black
```

若图不连通，`dfsSweep` 会产生一片 DFS forest；每个新根没有入树边。
