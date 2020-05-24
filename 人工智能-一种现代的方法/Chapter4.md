# Chapter4 超越经典搜索

**1. 爬山法**

它是简单的循环过程，不断向值增加的方向持续移动。算法在到达一个“峰顶”时终止，邻接状态中没有比它值更高的。算法不维护搜索树，因此当前结点的数据结构只需要记录当前状态和目标函数值。爬山法不会考虑与当前状态不相邻的状态。

伪代码：

``` python
    function  HILL-CLIMBING(problem) returns a state that is a local maximum

        current <- MAKE-NODE(problem.INITIAL-STATE)
        loop do 
            neighbor <- a highest-valued successor of current
            if neighbor.VALUE <= current.VALUE then return current.STATE
            current <- neighbor
```

爬山法的困境：
1. 局部最大值
2. 山脊（会造成一系列的局部最大值）
3. 高原

考虑随机重启爬山法，即通过生成随机的初始状态来找到目标状态。这种算法的完备性无限接近于1。

**2. 模拟退火搜索**

允许下山的随机爬山法。

``` python
function SIMULATED-ANNEALING(problem, schedule) returns a solution state 
    inputs:problem, # a problem 
           schedule # a mapping from time to 'temperature'

    current <- MAKE-NODE(problem.INITIAL-STATE)
    for t = 1 to INF do
        T <- schedule(t)
        if T = 0 then return current
        next <- a randomly selected successor of current 
        dE <- next.VALUE - current.VALUE
        if dE > 0 then current <- next
        else current <- next only with probability e ** (dE / T)
```

**3. 局部束搜索（Local beam search)**

从k个初始状态开始，每一步全部k个状态的所有后继状态全部被生成，如果其中有一个是目标状态，则算法停止。否则，它从整个后继列表中选择k个最佳的后继，重复这个过程。

>如果是最简单形式的局部束搜索，那么由于这k个状态缺乏多样性——它们很快会聚集到状态空间中的一小块区域内，使得搜索代价比高昂的爬山法版本还要多。随机束搜索（ stochastic beam search）为解决此问题的一种变形，它与随机爬山法相类似。随机束搜索并不是从候选后继集合中选择最好的k个后继状态，而是随机选择k个后继状态，其中选择给定后继状态的概率是状态值的递增函数。随机束搜索类似于自然选择，“状态”（生物体）根据“值”（适应度）产生它的“后继”（后代子孙）。

**4. 遗传算法（Genetic algorithm）**

