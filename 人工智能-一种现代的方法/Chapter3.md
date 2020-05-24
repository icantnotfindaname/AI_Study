# Chapter3 通过搜索进行问题求解

搜索：寻找行动序列，返回问题的解 【有可能有分支策略】

简单的Agent设计：形式化 => 搜索 => 执行   【在执行解的序列时无视感知信息 => 称之为开环系统，因为没办法互相影响了】

良定义的问题和解：

- Initial state  初始状态
- Action set 可能的行动集合  【给定一个状态s，则 Action(s) 表示在s下可以执行的集合】
- Transfer model 转移模型    【Result(s,a)：在s下执行行动a之后达到的状态】  【后继状态：从给定状态出发通过单步行动可以到达的状态集合】
  

<font color=blue><I>以上三者定义了问题的状态空间，即从初始状态可以达到的所有状态的集合，形成一个有向网络或图</I></font>

- Target test  确定给定的状态是不是目标状态
- Path dissipation function（路径耗散函数）  边加权，反映性能度量

<font color=blue><I>以上五点可以看成一个数据结构，以此作为问题求解算法的1输入。问题的解是从初始状态到目标状态的一组行动序列，解的质量由路径耗散函数度量，所有解里的路径耗散值最小的解成为最优解。</I></font>

- Ex.八数码问题，八皇后问题

搜索方法:

Tree Search => Graph Search  【保留了一个探索集，减少冗余】

基础：
需要一个数据结构来记录搜索树的构造过程

定义节点的数据结构：
- State        对应状态空间中的状态
- Parent       父节点
- Action       父节点生成该节点时候采取的行动
- Path-cost    路径消耗

<font color=blue><I>注意区分节点和状态的区别，节点是用来表示搜索树的数据结构，状态则对应于世界的一个配置情况，所以节点是一种由Parent指针定义的特定路径，但状态不是。更进一步，如果同一个状态可以用过两种不同的路径生成，那么两个不同的结点就包含同样的世界状态。</I></font>

定义节点的存储空间的数据结构：队列  【可能是stack或者queue，或者说FIFO或者LIFO】
- isEmpty
- Pop
- Insert 

问题求解算法的性能评价标准：
- 完备性：当问题有解的时候，此算法是否能保证找到解
- 最优性：是否能找到最优解
- 时间复杂度
- 空间复杂度

<font color=blue><I>

时间和复杂度通常需要和问题的难度规模一起考虑。在理论计算机科学中，一种典型的度量方式是状态空间图的大小，$|V|+|E|$。在AI领域，状态空间图大多数由初始状态、行动和转移模型隐式表示，且大多数都是无限的，因此复杂度通常由下列三个量来表达：
- 分支因子
- 目标节点所在的最浅的深度
- 状态空间的任何路径的最大长度

时间常常由搜索过程中产生的节点数目来度量，空间是由在内存中储存的最多节点数来度量。大多数情况下，我们描述搜索树的时间和空间复杂度；对于图，这个答案依赖于状态空间中的路径有多冗余。
</I></font>

### 无信息搜索策略：

**1.BFS**

- 伪代码
``` python
function BFS(problem) return a solution , or failture
    
    node <= a node with STATE = problem and INITIAL_STATE, PATH-COST = 0
    if problem.GOAL-TEST(node.STATE) then return SOLUTION(node)
    frontier <= a FIFO queue with node as the only element 
    explored <= an empty set 

    loop do
        if EMPTY?(frontier) then return failture 
        node <= POP(frontier)
        add node.STATE to explored
        for each action in problem.ACTIONS(node.STATE) do 
            child <= CHILD-NODE(problem, node, action)
            if child.STATE is not in explored of frontier then 
                if problem.GOAL-TEST(child.STATE) then return SOLUTION(child)
                frontier <= INSERT(child, frontier)
```

- 性能评估

1. 完备性 ✔
2. 最优性：需要看路径代价函数，最浅的目标节点不一定是最优。如果路径代价是基于节点深度的非递减函数，那BFS是最优的。
3. 复杂度：假设搜索uniform tree的状态空间中每个state都有b个后继，有限深度为d，则节点总数为：$b+b^2+...+b^d=O(b^d)$，时间复杂度为 $O(b^d)$，空间复杂度也为 $O(b^d)$

<font color=blue><I>如果算法是在选择扩展的结点时而不是在节点生成的时候进行目标检测，那么在目标被检测到之前深度d上的其他节点已经被扩展，这是时间复杂度应为 $O(b^{d+1})$</I></font>

【内存需求是BFS最大的问题，而且复杂度也难以接受】

=> 一般来说，指数级别复杂度的搜索问题不能用无信息的搜索算法求解，除非是规模很小的实例。

**2. 一致代价搜索（Uniform-cost search）**

当每一步的行动代价都相等时宽度优先搜索是最优的，因为它总是先扩展深度最浅的未扩展节点。而一致代价搜索是找到对**任意单步代价函数都是最优的算法**。也就是说此算法每一次扩展的是**路径消耗**最小的结点。在代码上就表现为**需要按路径代价对队列进行排序**。

<font color=orange>todo：伪代码</font>

UCS和BFS的两个重大不同：

- UCS的目标检测运用于节点被选择扩展时，而不是在节点生成的时候进行。
- 边缘中的结点有更好的路径到达该节点那么将会引进一个测试。

性能评估：
- 完备性 ✔  【在没有零代价的行动下】
- 最优性 ✔
- 复杂度

>一致代价搜索由路径代价而不是深度来引导，所以算法复杂度不能简单地用b和d来表示。引入 <font color=red>$C^*$</font> 表示最优解的代价，假设每个行动的代价至少为 <font color=red>$\varepsilon$</font> 。那么最坏情况下，算法的时间和空间复杂度为 <font color=red>$O(b^{1+\lfloor{C^*/ {\varepsilon}}\rfloor})$</font>，要比 $b^d$ 大得多。这是因为一致代价搜索在探索包含代价大的行动之前，经常会先探索代价小的行动步骤所在的很大的搜索树。当所有的单步耗散都相等的时候，$O(b^{1+\lfloor{C^*/ {\varepsilon}}\rfloor})$ 就是 $b^d$ 。此时，一致代价搜索与宽度优先搜索类似，除了算法终止条件，宽度优先搜索在找到解时终止，而一致代价搜索则会检查目标深度的所有结点看谁的代价最小：这样，在这种情况下一致代价搜索在深度d无意义地做了更多的工作。


**3. DFS、DLS**

使用LIFO队列，一般利用递归实现。

伪代码：
``` python
# 这里的 DFS加上了剪枝算法，所以称之为 DLS 即 Depth-first Limit Search

function DLS(problem, limit) returns a solution, or failture/cutoff
    return RECURSIVE-DLS(MAKE-NODE(problem.INITIAL-STATE), problem, limit)

function RECURSIVE-DLS(node, problem, limit) returns a solution, or failture/cutoff
    # 是否是目标
    if problem.GOAL-TEST(node.STATE) then return SOLUTION(node)
    # 如果限制深度已经为0了就要剪枝
    else if limit = 0 then return cutoff
    else 
        # 暂时不需要剪枝
        cutoff_occurred? <= false
        # 往下扩展
        for each action in problem.ACTIONS(node.STATE) do
            # 子节点
            child <= CHILD-NODE(problem, node, action)
            # 递归
            result <= RECURSIVE-DLS(child, problem, limit - 1)
            # 是否到达限定深度
            if result = cutoff then cutoff_occurred? <= true
            # 没有到达限定深度则返回结果
            else if result != failture then return result 
        if cutoff_occurred? then return cutoff else return failture
```

DFS评估：

- 完备性

>深度优先搜索算法的效率严重依赖于使用的是图搜索还是树搜索。避免重复状态和冗余路径的图搜索，在有限状态空间是完备的，因为它至多扩展所有结点。而树搜索，则不完备。如图3.6中，算法会陷入Arad- Sibiu- Arad-Sibiu的死循环。深度优先搜索可以改成无需额外内存耗费，它只检查从根结点到当前结点的新结点：这避免了有限状态空间的死循环，但无法避免冗余路径。在无限状态空间中，如果遭遇了无限的又无法到达目标结点的路径，无论是图搜索还是树搜索都会失败。

- 最优性：无论是基于图还是树的搜索都不是最优的。

- 复杂度

<u>时间复杂度</u>受限于状态空间的规模，且DFS的树搜索可能在搜索树上生成所有 $O(b^m)$ 个节点，其中m指的是任意节点的最大深度，可能比状态空间要大得多。而且**m可能比d(最浅解的深度)大很多，并且如果树是无界限的，那么m可能是无限的**。

DFS的优点在于其<u>空间复杂度</u>，对图搜索而言，DFS只需要存储一条从根节点到叶节点的路径，以及该路径上的每个节点的所有未被扩展的兄弟节点即可。一旦节点被扩展，当它的所有后代都被探索完之后，它就会从内存中删除。假设状态空间分支因子为b，最大深度为m，则DFS只需要存储$O(bm)$个节点。

【深度优先搜索的一种变形称为**回溯搜索（ backtracking search）**，所用的内存空间更少。在回溯搜索中，每次只产生一个后继而不是生成所有后继；每个被部分扩展的结点要记住下一个要生成的结点。这样，内存只需要 $O(m)$而不是 $O(b^m)$。回溯搜索催化了另一个节省内存（和节省时间）的技巧：通过直接修改当前的状态描述而不是先对它进行复制来生成后继。这可以把内存需求减少到只有一个状态描述以及$O(m)$个行动。为了达到这个目的，当我们回溯生成下一个后继时，必须能够撤销每次修改。对于状态描述相当复杂的问题，例如机器人组装问题，这些技术是成功的关键。】

DLS评估：

- 完备性：虽然解决了无穷路径问题，但是如果 $l<d$, 则它也是不完备的。

- 最优性：✖

- 复杂度：时间复杂度为 $O(b^l)$, 空间复杂度是 $O(bl)$

【DFS可以看成是 $l=\inf$ 的DLS】
【最好的深度界限是状态空间的**直径**】

**4. 迭代加深的深度优先搜索（Iterative deepening search)**

可以确定最好的深度界限。方法：**不断增大深度限制直到找到目标**。所以当深度界限达到d，即最浅目标节点所在的深度时，就能找到目标节点。

伪代码：
``` python
function IDS(problem) returns a solution, or failture
    for depth = 0 to INF do
        result <= DLS(problem, depth)
        if result != cutoff then return result 
```

评估：

迭代加深的深度优先搜索算法结合了深度优先搜索和宽度优先搜索的优点，它的空间需求是合适的：$O(bd)$。和宽度优先搜索一样，当分支因子有限时是该搜索算法是完备的，当路径代价是结点深度的非递减函数时该算法是最优的。

<font color=blue><I>

也许迭代加深的深度优先搜索看起来比较浪费，因为状态被多次重复生成。但事实上代价并不是多大。原因是在分支因子相同（或者近似）的搜索树中，绝大多数的结点都在底层，所以上层的结点重复生成多次影响不大。在迭代加深的深度优先搜索中，底层（深度d）结点只被生成一次，倒数第二层的结点被生成两次，依此类推，一直到根结点的子结点，它被生成d次。因此，生成结点的总数为:
$$
    N ( IDS ) = ( d ) b + ( d - 1 ) b ^ { 2 } + \cdots + ( 1 ) b ^ { d }
$$

时间复杂度为 $O(b^d)$ 与宽度优先搜索相近。重复生成上层结点需要付出额外代价，但不是很大。

</I></font>

 <font color=orange>Todo: 迭代加长搜索(IDS + UCS)</font>

>迭代加深的深度优先搜索和广度优先搜索相似，每次迭代要把当前层的新结点全都探索过。结合一致代价搜索的迭代搜索是有价值的，在一致代价搜索确保最优化的同时避免了大量的内存需求。它的主要思想是用不断增加的路径代价界限代替不断增加的深度界限基于这种思想的算法被称为迭代加长搜索（ iterative lengthening search）。不幸的是，与一致代价搜索相比，事实上迭代加长搜索将导致实在的额外开销。

**5. 双向搜索（Bidirectional search)**

>双向搜索（ bidirectional search）的思想是同时运行两个搜索—个从初始状态向前搜索同时另一个从目标状态向后搜索—希望它们在中间某点相遇，此时搜索终止。理由是 $b ^ { d / 2 } + b ^ { d / 2 }$ 要比 $b ^ { d }$ 小很多，或者可以看图，两个小圆的面积相加比以起点为中心到达目标的大圆的面积要小很多。

目标测试：检查两个方向的搜索的边缘节点集是否相交

<font color=blue><I>注意：这种方法找出来的解不一定是最优解，即使两个方向采用的都是BFS</I></font>

复杂度：

>双向都使用宽度优先搜索的算法时间复杂度是 $O(b^{d/2})$。空间复杂度也是 $O(b^{d/2})$。如果一个方向的搜索改为迭代加深的深度优先搜索，复杂度大约可以减半，但至少一个边缘结点集一定要存放在内存中，这样才能检查是否有交集。降低的时间复杂度使得双向搜索很诱人，但是如何向后搜索呢？这并不像听起来那么简单。定义结点x的祖先是所有以结点x为后继的结点集。双向搜索需要计算祖先的算法。如果状态空间中所有的行动都是可逆的，x的祖先正是它的后继。其他情况则需要具体情况具体分析。让我们考虑一下“从目标开始的向后搜索”中的“目标”是什么。在八数码问题和罗马尼亚问题中，都只有一个目标状态，因此向后搜索与向前搜索类似。如果某问题有几个明确列出的目标状态——例如，图3.3中的两个无尘目标状态—那么我们可以构造一个虚拟的目标状态，它的直接祖先结点是所有真实的目标状态。但如果目标状态是一种抽象描述的话，如n皇后问题的目标是“没有皇后攻击另一个皇后”，就很难应用双向搜索。

无信息搜索策略对比表：
<img src="https://tlgur.com/d/8QeJkbl8" />

【树搜索策略比较。$b$指分支因子；$d$指最浅解的深度；$m$指搜索树的最大深度；$l$是深度界限。右上角标的含义如下：$^a$ 当b有限时算法是完备的；$^b$ 若对正数 $\varepsilon$有单步代价 $\ge \varepsilon$ 则是完备的；$^c$ 当单步代价相同时算法最优；$^d$当双向都使用宽度优先搜索】

### 有信息（启发式）搜索策略

使用问题本身的定义之外的特定知识进行问题求解。

一般算法称之为**最佳优先搜索（Best-first search）**，与一致代价搜索类似，但是最佳有限搜索是根据f值而不是g值对优先级队列排队。（f是一个评价函数）

【复习：g是路径耗散函数】

对f的选择决定了搜索的策略，大多数由**启发函数(heuristic function)** 构成:
$$
    h ( n ) = \text { 结点 } n\text{ 到目标结点的最小代价路径的代价估计值}
$$


**1. 贪婪最佳优先搜索(Greedy best-first search)**

扩展离**目标**最近的节点，理由是这样可能可以很快地找到解，因此它只用启发式信息，即：$f(n)=h(n)$

评估：
- 不完备，可能陷入死循环
- 最坏的情况下，算法的时间复杂度和空间复杂度都是$O(b^m)$

<font color=blue><I>如果有一个好的启发式函数，那么复杂度就可以得到有效的降低，下降的幅度取决于特定的问题和启发式函数的质量。</I></font>

**2. A*搜索**

对节点的评估结合了$g(n)$， 即到达此节点已经花费的代价。也就是说：$f(n) = g(n) + h(n)$

在启发式函数满足特定的条件下，A*搜索既是完备的也是最优的，算法与一致代价搜索类似。

保证最优性的条件：可采纳性和一致性

- $h(n)$是一个可采纳启发式，即从来不会过高估计到达目标的代价。也就是说它是乐观的，认为解决问题所花的代价比实际代价小。

- 一致性，或曰单调性。（这个条件比第一个条件稍强，只运用于图搜索之中）内容：<u>我们称启发式 $h(n)$ 是一致的，如果对于每个结点n和通过任一行动a生成的n的每个后继结点n，从结点n到达目标的估计代价不大于从n到n的单步代价与从n到达目标的估计代价之和：</u>
$$
    h ( n ) \leq c ( n , a , n ^ { \prime } ) + h ( n ^ { \prime } )
$$

其实就是一个一般的三角不等式。

<font color=blue><I>一致的启发式都是可采纳的。</I></font>

<font color=orange>Todo：证明这一点。</font>

<font color=blue><I>如果 $h(n)$ 是可采纳的，那么A* 的树搜索版本是最优的；如果 $h(n)$ 是一致的，那么图搜索的A*算法是最优的。</I></font>

但是！

A* 算法的时间和空间复杂度都是指数级的，且因为它在内存中保留了所有已生成的结点，所以常常在计算完之前就耗尽了它的内存。因此 A*算法对于很多大规模问题来说显得并不适用。

**3. 存储受限的启发式搜索**

> A* 算法减少内存需求的简单办法就是将迭代加深的思想用在启发式搜索上，即迭代加深A*（IDA*）算法。IDA* 和典型的迭代加深算法的主要区别是所用的截断值是f代价（g+h）而不是搜索深度；每次迭代，截断值取超过上一次迭代截断值的结点中最小的f代价值。IDA* 算法对很多每步代价都是单位代价的问题是实用的，它可以避免结点队列排序的实际系统开销。不幸的是，对于每步代价都是某个实数的问题，它会遇到与<u>习题3.17</u>中描述的迭代的一致代价搜索相同的困难。

- RBFS 递归最佳优先搜索

伪代码：
``` python
function RBFS(problem) returns a solution, or failure 
    return RBFS(problem, MAKE-NODE(problem.INITIAL-STATE), INF)

function RBFS(problem, node, f_limit) returns a solution, or failture and a new f-cost limit
    if problem.GOAL-TEST(node.STATE) then return SOLUTION(node)
    successor <- []
    for each action in problem.ACTIONS(node.STATE) do
        add CHILD-NODE(problem, node, action) into successors
    if successors is empty then return failure, INF
    for each s in successors do 
        s.f <- max(s.g + s.h, node.f)
    loop do 
        best <- the lowest f-value node in successors 
        if best.f > f_limit then return failure, best.f
        alternative <- the second-lowest f-value among successors 
        result, best.f <- RBFS(problem, best, min(f_limit, alternative))
        if result != failure then return result
```

缺点：使用的内存空间过于小，且图中的冗余路径可能会带来复杂度潜在的指数级的增长

<font color=orange><I>Todo：SMA*算法</I></font>

**4. 学习以促搜索**

<font color=orange><I>Todo：元学习算法</I></font>

**5. 启发式函数**

有效分支因子b* —— 用于刻画启发式的方法

对于某一问题，如果A* 算法生成的总结点数为N，解的深度为d，那么b*就是深度为d的标准搜索树为了能够包括N+1个结点所必需的分支因子。即：
$$
    N + 1 = 1 + b ^ { * } + ( b ^ { * } ) ^ { 2 } + \cdots + ( b ^ { * } ) ^ { d }
$$
**6. 从松弛问题出发设计可采纳的启发式**

减少了行动限制的问题称为松弛问题。

可以利用松弛的操作来构造启发式。但是如果<u>松弛问题本身很难求解</u>，使用它的值作为对应的启发式就得不偿失了。

生成新的启发式函数的难点在于经常不能找到“无疑最好的”启发式。如果可采纳启发式的集合$h_1…h_m$对问题是有效的，并且其中没有哪个比其他的更有优势，我们应该怎样选择呢？其实我们不用选择。我们可以这样定义新的启发式从而得到其中最好的：

$$
    h(n)=max[h_1(n), ..., h_m(n)]
$$

**7. 从子问题出发设计可采纳的启发式**

储存子问题实例所需要的解代价。

**8. 从经验中学习启发式**

通过过往求解的经验构建启发式。