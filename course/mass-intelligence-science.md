---
title: 众智科学
description: 
published: true
date: 2023-12-28T04:36:41.071Z
tags: 
editor: markdown
dateCreated: 2023-12-28T04:36:14.976Z
---

众智科学在课程上主要包括两个部分：社会网络和博弈论。

社会网络涉及的内容包括：社会网络的基本概念、同质性、正负关系、小世界和搜索引擎。

博弈论包括：博弈论的基本概念、一些典型例子、广告问题、权利、从众与流行、新事物扩散、信息不对称、扩散和流行病学。

## 图基础

### 图的基本概念

#### 同构

同构是指两个图的节点和边的数量相同，且节点和边的连接关系相同。也就是说，两个互相同构的图其实只是画法不同，但是其本质是相同的。

#### 计算机内的表示

在计算机内，可以通过存储相邻节点的方式来表示图。这种方式的存储方式称为邻接矩阵；也可以通过存储边的方式来表示图，这种方式称为关联矩阵。

假设我们有一个简单的无向图，其中有4个节点和5条边，如下所示：

```text
   1 -- 2
   |  / |
   3 -- 4
```

邻接矩阵描述：

邻接矩阵是一个n x n的矩阵，其中n是节点的数量。如果节点i和节点j之间有一条边，则邻接矩阵中第i行第j列和第j行第i列的值为1，否则为0。对于无向图，邻接矩阵是对称的。

对于上面的图，邻接矩阵为：

```text
   0  1  1  0
   1  0  1  0
   1  1  0  1
   0  0  1  0
```

关联矩阵描述：

关联矩阵是一个n x m的矩阵，其中n是节点的数量，m是边的数量。如果节点i与边j相连，则在关联矩阵中第i行第j列的值为1；如果节点i不与边j相连，则为0。对于无向图，每条边在关联矩阵中对应两列，分别对应于两个相连的节点。

对于上面的图，关联矩阵为：

```text
   1  1  0  0  0
   1  0  1  0  0
   0  1  1  1  0
   0  0  0  1  1
```

### 路径与最短路径

路径是指两个节点之间的连接。路径的长度是指路径上边的数量。寻找最短路径是一个很基本的问题，也是很多算法的基础。

#### BFS搜索

BFS搜索是一种广度优先搜索，它从一个节点开始，逐层向外搜索，直到找到目标节点。BFS搜索的时间复杂度为$O(n)$，其中$n$是节点的数量。

对于BFS来讲，要搜索图中的最短路径，只需要在搜索的过程中记录每个节点的深度即可。

$A^*$是对BFS的一种改进，它在搜索的过程中，会优先搜索距离目标节点更近的节点。$A^*$的时间复杂度为$O(n)$。具体来讲，他引入了曼哈顿距离（Manhattan distance）的概念，即从当前节点到目标节点的最短距离。在搜索的过程中，$A^*$会优先搜索距离目标节点更近的节点。

#### DFS搜索

DFS搜索是一种深度优先搜索，它从一个节点开始，沿着一条路径一直向下搜索，直到找到目标节点或者无路可走。DFS搜索的时间复杂度为$O(n)$，其中$n$是节点的数量。

#### Floyd-Warshall算法

Floyd-Warshall算法是一种动态规划算法，用于计算图中任意两个节点之间的最短路径。Floyd-Warshall算法的时间复杂度为$O(n^3)$，其中$n$是节点的数量。他能够求出任意两个节点之间的最短路径，但是不能求出最短路径的具体路径。由于复杂度较高，通常在节点数量较少（<200）的情况下使用。

#### Bellman-Ford算法与SPFA

Bellman-Ford算法是一种动态规划算法，用于计算图中任意两个节点之间的最短路径。Bellman-Ford算法的时间复杂度为$O(nm)$，其中$n$是节点的数量，$m$是边的数量。他能够求出任意两个节点之间的最短路径，但是不能求出最短路径的具体路径。

SPFA是Bellman-Ford算法的一种优化，它在Bellman-Ford算法的基础上，引入了队列，用于记录需要更新的节点。SPFA的时间复杂度为$O(km)$，其中$k$是最短路径的长度。

#### Dijkstra算法

Dijkstra算法是一种贪心算法，用于计算图中两个节点之间的最短路径。Dijkstra算法的时间复杂度为$O(n^2)$，其中$n$是节点的数量。

### 图的连通性

对于一个图来讲，如果图中任意两个节点之间都存在路径，则称这个图是连通的。如果图中存在两个节点之间不存在路径，则称这个图是非连通的。

在一个图中可以存在若干个连通分量，每个连通分量都是一个连通的子图。

在连通问题下，有一些特殊的点和边：

- 割边/桥：如果删除这条边，图就不再连通

- 割点：如果删除这个点，图就不再连通

- 捷径：相连的两个顶点没有公共顶点

  > 删除捷径后，两点的距离将增加2以上

对有向图来讲，强连通分量是指任意两个节点之间都存在路径的连通分量。

### 欧拉问题

- 欧拉路：经过图中每条边一次且仅一次的路径。
- 欧拉回路：经过图中每条边一次且仅一次的回路。

在无向连通图中：

- 如果图中的点全都是偶点，则存在欧拉回路；任意一点都可以作为起点和终点。
- 如果只有2个奇点，则存在欧拉路，其中一个奇点是起点，另一个是终点。
- 不可能出现有奇数个奇点的无向图。

在有向连通图中：

- 当且仅当图中所有点的入度等于出度时，存在欧拉回路；任意一点都可以作为起点和终点。
- 如果只有一个度数为1的点，一个度数为 -1的点，其它所有点的度数为0，那么存在欧拉路径，其中度数为1的是起点，度数为 -1的是终点。

### 二分图

二分图是指图中的点可以分成两个集合，使得图中的每条边的两个端点都分别属于两个集合。二分图的充要条件是：图中不存在奇数环。

二分图的一个常见问题是：给定一个二分图，如何将图中的点分成两个集合，使得图中的每条边的两个端点都分别属于两个集合。通常，我们可以使用染色法来解决这个问题。

对于二分图来讲，如果图中的点可以分成两个集合，使得图中的每条边的两个端点都分别属于两个集合，则称这个二分图是完全二分图。可以使用最大流或者匈牙利算法来解决。

---

## 社会网络基本概念

- [ ] 三元闭包

### 基本概念

社会/社交网络（Social Network， SN）与在线社交网络（Online Social Network， OSN）是研究讨论的主要内容。

社会网络的定义为：

- 由许多节点构成的一种社会结构。节点通常是指个人或组织， 而社交网络代表着各种社会关系。
- 在社交网络中，成员之间因为互动而形成相对稳定的关系体系， 这种关系体系可以是朋友关系、 同学关系，也可以是生意伙伴关系抑或种族信仰关系。
- 通过这些关系， 社交网络把从偶然相识的泛泛之交到紧密结合的家庭关系，再到社会活动中的各种人们组织串联起来

在线社交网络则是各种网络应用，例如：

- IM（即时通讯）
- 在线社交
- 微博类
- 共享空间等

在对社会网络的各种研究中，最终落到几个基本常见问题上：关系强度、三元闭包、小世界、结构平衡、同质性。

![社会网络研究的经典](https://s2.loli.net/2023/06/04/YneJMvsjD3bUIxk.png)

### 三元闭包

三元闭包是社会网络演化的基本结构性原因。他可能是由机会、信任和动机三个原因带来的。

> 例如，如果两个本来互不相识的人有了一个共同朋友，那么他们俩将来成为朋友的可能性提高。

对三元闭包进行拓展讨论：

- 两个人的共同朋友越多，则他们成为朋友的可能性越高

  > 这是从“量”方面的拓展

- 两个人与共同朋友的关系越密切，则他们成为朋友的可能性越高

  > 这是从“质”方面的拓展

- 三个原因（机会、信任、动机）的作用在这些拓展的意义上保持一致

刻画三元闭包现象常用一个参数：节点聚集系数。节点A的聚集系数 = A的任意两个朋友之间也是朋友的概率（即邻居间朋友对的个数除以总对数）

#### 社会网络结构的统计特征

- 度分布：

  1. 度分布函数$P(k)$：$P(k)$表示网络中度为$k$的节点在整个网络中所占的比例。

  2. 累积度分布函数（CumulativeDegree Distribution Function）。$P_k$表示度不小于$k$的节点的概率分布，其分布关系为
     $$
     P_k=\sum_{x=k}^{\infin}P:(x)
     $$

- 平均路径长度

- 网络直径

- 有效直径：至少90%的互连节点对之间的距离均不超过的最小值。

- 网络密度

- 绝对密度公式

- 介数

#### 社会问题研究的两个视角

> 为什么对找工作这种重要的事情，提供有效帮助的人更多只是一般熟人，而不是亲近的朋友？

强三元闭包原理（假设）：如果A-B和A-C之间的关系为强关系；则B-C之间形成边的**可能性应该很高**；若A有两个强关系邻居B和C，但B-C之间**没有任何关系（s或w）**，则称节点A违背了强三元闭包原理；如果节点A没有违背强三元闭包原理，则称节点A符合强三元闭包原理。

断言：若节点A符合强三元闭包，且至少有两个强关系邻居，则与A相连的任何捷径必定意味着是弱关系。两人关系的强度如何与是否有共同朋友相关（不等价），捷径意味着没有共同朋友，强度为“弱”。

> 数学的证明，得出一个具有社会学意义的结论。这个结论将一个社交概念（关系的强弱）和一个结构概念（捷径）连接了起来。

由此可以得出社会网络结构的一个基本意象：

- 用桥（或捷径，或邻里重叠度很低的边，弱关系）连接起来的相对比较密集互连的节点群
- 其中，那些是多个桥的端点的节点（B）值得特别讨论：聚集系数较低；她与群组内部的节点（A）相比，有什么利弊？怎样与她打交道？

#### 介数

介数：一条边承载的一种“流量”。两个节点A和B，设想1个单位的流量从A到B，均分到它们之间所有的最短路径上，K条最短路径，则每条路径上分得1/k，若一条边被m条路径共用，则在它上面流过m/k；所有节点对都考虑后，一条边上的累记流量就是它的介数（betweenness）。

## 社会网络的同质性

- [ ] 测度
- [ ] 影响
- [ ] 社团闭包
- [ ] 会员闭包

### 同质性的含义

每个人的特质可以分为固有特质（自然属性）和可变特质（建构属性）。

同质性是社会网络结构形成的基本外部原因。

> 社会学的一个基本问题是人们是因为相似，才成为朋友 （selection）；还是因为成为朋友后变得相似（social influence）？

### 评估同质现象

将一个图中的所有节点分成两种类型，其中一个占比为$p$，另一个占比为$1-p$，则两端点类型不同的边占比为$2p(1-p)$。

实际情况下，端点颜色相同的边越多，其同质性表现得越强。因此，端点异色的边数/总边数如果明显小于$2p(1-p)$，则说明同质性很强。

### 归属网

相同兴趣，共同参与某种活动，增加建立关系的可能性。描述这种情况的网络叫做从属网（affiliation network），它描述了人们相遇的机会。这也是一种“闭包”现象，源于selection因素，称为社团闭包（community closure）。

社交关系也可能促进人们参与原先不参与的活动，这种情况下，社交关系是一种“影响”（influence），称为会员闭包（membership closure）。

社会归属网综合考察了同质性，三元闭包、社团闭包、会员闭包都可以在其中显现。

![对同质性机理认识与归纳](https://s2.loli.net/2023/06/05/RqVsUHvQ4jB3WIY.png)

### 隔离——谢林模型

隔离是同质性的影响与结果（固有特质相同到可变特质趋同）。

谢林模型的内涵是隔离现象并不一定是个人可以选择的结果。

## 社会网络的正负关系

- [ ] 平衡（定理证明、应用）

### 边的正负性

社会网络中，两个节点之间的关系（边）可能携带着各种各样的社会性含义。除了强弱以外，还有支持（＋）与反对（－），朋友（＋）与敌人（－）等利害关系。

### 结构平衡

三角关系的稳定性：从社会心理学角度看，一个平衡的三角关系要么（＋＋＋），要么（－－＋）。否则结构不平衡，即隐含有一种改变的力量（趋势）。

图的稳定性：（完全）图的结构是平衡的，若其中所有三角关系都是平衡的（即每个三角关系要么3＋，要么1＋和2－）。

平衡定理：如果一个标记（＋／－）的完全图是平衡的，则要么它的所有节点两两都是朋友，要么它的节点可以被分为两组，X和Y，其中X组内的节点两两都是‘＋’关系，Y组内的节点两两也都是‘＋’关系，而X组中的每个节点与Y组中每个节点之间都是‘－’关系。

平衡定理的推广：

1. 弱平衡：只是不允许（++-）
2. 非完全网络的平衡：允许一些边的缺失，即考虑非完全图情形

#### 弱平衡

注意到在平衡网络中排除的两种三角关系在社会关系的含义（分量）上是有区别的

- 改变（－、－、－）的动力较弱
- 改变（＋、＋、－）的动力较强

弱平衡网络：只是不存在（＋、＋、－）三角关系的标注完全图（即，我们放松了对平衡的要求）。节点可分成若干组，组内均为朋友（＋），组间均为敌人（－）。

> “平衡”意味着改变关系属性的动力不足，或者有较强的维持现有关系性质的动机

#### 近似平衡网络

#### 非完全网络中的平衡

## 小世界

- [ ] 短视搜索
- [ ] WS/WSK模型解释与应用

### 短视搜索

一种有目标的、基于局部信息的网络搜索过程。具有如下特点：

- 每个节点有一个特征（根据需求可有不同定义），任何两个节点之间的特征可以谈差别（特征距离，不同于网络距离）
- 每个节点仅知道自己和自己网络邻居节点的特征；特别是（因短视），不知道邻居有哪些邻居
- 搜索过程可看成是信息传递的过程，节点将信息（目标节点的特征）传给离目标节点距离较近（差别较小）的网络邻居节点

形成社会网络的两种基本力量：

- 同质性：相似的人更容易聚集在一起
- 弱联系：人们通过朋友的朋友结识新朋友

“小世界”问题的探究经历了以下历程：

1. 1967: Milgram的“六度分隔”实验，发现六度分割以及所有的信都能沿短路径到达。
2. 1998: Watts和Strogatz的“小世界”模型，解释了六度分隔的原因
3. 2000: Watts-Strogatz-Kleinberg模型，解释了信能沿短路径到达的原因。
4. 2005: W-S-K模型在大规模OSN（大数据）中得到验证

### Watts-Strogatz模型

W-S模型是一个随机图模型，它包含许多“三角形”和少数随机的“远程边”。连接近邻具有确定性，连接远程节点具有随机性。

可以证明，任何两个节点之间存在短路径的概率很高，因此人们倾向于世界上任意两人之间存在很短的“认识关系链”几乎是必然的。

### Watts-Strogatz-Kleinberg模型

W-S模型无法解释“六度分隔”实验中的另一个重要发现：短路径不仅存在，而且通过短视搜索能发现。于是，W-S-K模型引入了“弱连接的概率应该随距离的幂次递减”的假设，即：

$$
P_{ij} \propto d_{ij}^{-\alpha}
$$

这证明了短路径不仅存在，而且通过短视搜索能发现。同时还得到幂次指数$\alpha$的值为2。

在寄信的实验过程中，每个人被告知：如果不认识目标人，就不能直接寄给他，但要有意识地转发，希望信件的下一站能离目标人近一些。因此，需要一种社会网络模型既反应任意节点对之间短路径的存在性，也支持在这种转发方式下短路径的可实现性。在结构上，就要求：1）网络中两个节点无论相距多远，都要有机会很快接近；2）网络中任意两个节点之间的距离越近，存在直接连接的机会越大。

为此，在W-S的基础上，让两个节点之间存在随机边的概率与它们之间的距离成反比，即：

$$
P_{ij} \propto d_{ij}^{-\alpha}
$$

这里，$d_{ij}$是节点$i$和$j$之间的距离，$\alpha$是一个常数，他用来控制远程连接的概率。当$\alpha=0$时，网络就是W-S模型；当$\alpha=2$时，网络就是W-S-K模型。

给定每个节点的空间位置，可以计算出每个节点对每个节点的相对rank，然后利用这些关系数据，计算：

$$
\frac{排位r上朋友节点数}{排位r上所有节点数}
$$

能够发现，随距离的增大，这个占比会越来越小。

## 搜索引擎

- [ ] Hit方法
- [ ] PageRank算法

对“最可能满足”的多义性包括：

- 同一个查询，不同的需求
- 不同的查询，同一个需求

### 传统信息检索技术

传统信息检索技术基于词语之间的相关性，例如传统的文献检索、关键词查询等。在这种情况下，查询目标包含查询词是一个合理假设。

### 反复改进方法

有效利用链接关系蕴含的信息，是搜索引擎超越传统信息检索系统、技术进步的最重要标志。

万维网中一篇网页具有两方面的属性：

从观念上：

- 被很多网页指向：权威性高，认可度高
- 指向很多网页：中枢性强

从HITS算法：计算网页的权威值（auth）和中枢值（hub）

对于一个输入的有向图，我们可以这样计算$auth(p)$和$hub(p)$：

1. 初始化：对于每一个节点$p$，auth(p)=1, hub(p)=1$
2. 利用中枢值更新权威值：对于每一个节点$p$，$auth(p)=\sum_{q\in in(p)}hub(q)$即$p$的权威值等于指向$p$的节点的中枢值之和
3. 利用权威值更新中枢值：对于每一个节点$p$，$hub(p)=\sum_{q\in out(p)}auth(q)$即$p$的中枢值等于$p$指向的节点的权威值之和
4. 重复上面的过程，直到收敛

与观念相对应的，在搜索引擎领域，auth值较高的网页，被认为是权威网页；hub值较高的网页，被认为是中枢网页。

auth和hub的执都会随着迭代次数不断递增，他们的意义在于相对大小，而不是绝对大小。因此，在上面的每一轮迭代之后，都要对auth和hub进行归一化处理，也就是计算$auth(p)/\sum_{q\in V}auth(q)$和$hub(p)/\sum_{q\in V}hub(q)$。归一化的结果会随着迭代次数的增加而趋于稳定，也就是收敛到一个极限。

### PageRank算法

PageRank算法通过计算网页间的链接关系来计算网页重要性，它的基本思想是：一个网页的重要性，可以通过它的入链数来计算，而一个网页的入链数又可以通过它的出链数来计算。

他的基本描述为：

1. 选择操作的步骤数$k$，并初始化每个网页的PageRank值为1
2. 重复$k$次以下操作：
   1. 对于每个网页$p$，计算$p$的出链数$n_p$，并将$p$的PageRank值平均分配给它的出链。如果没有出链，则认为传递给自己（或者说保留给自己）
   2. 对于每个网页$p$，计算$p$的入链数$n_p$，并将$p$的PageRank值平均分配给它的入链

这种情况下，PageRank算法可能被一些“共谋”的网站影响，导致结果不准确。为了解决这个问题，PageRank算法引入了一个比例因子$s$，经验值在0.8到0.9之间，用来控制网页的PageRank值在传递过程中的损失。这样，在每一节点的PageRank值传递给下一节点时，都会加上$(1-s)/N$的值，其中$N$是网页总数。

---

## 博弈论基本概念

- [ ] 纯策略/多策略平衡
- [ ] 社会福利

### 博弈基础

博弈三要素：

- 参与人（Player，玩家）
- 策略集（Strategy，策略）
- 回报（Payoff，回报）

博弈特点：

- 每个茶与人有一个策略集
- 策略组：每个参与人出一个策略构成的策略组合
- 对应每个策略组合，每个参与人有一个回报

博弈论中关注的是参与人最终的选择，存在一种严格占优策略：对一个参与人来说，若存在一个策路，无论另一个参与人选择何种策略，该策略都是严格最佳的选择，则这个策略就称为是前者的严格占优策略。在博弈推理假设中，参与人都是理性的，即都会选择自己的严格占优策略。

### 占优策略和应对策略

占优策略和应对策略都可以再区分为是否严格，当策略收益不存在相等时，就是严格占优策略和严格应对策略。

### 纳什均衡

纳什均衡就是互为最佳应对的策略组合，即每个参与人都选择了自己的严格占优策略。一般来说，纳什均衡只能缩小到有限个，而不能缩小到一个。

纳什的贡献在于：证明了具有有限参与者和有限纯策略集的博弈一定存在纳什均衡。

引入随机性，考虑参与人将以一定的概率在不同策略间进行选择，一个概率对应一个“策略”（称为混合策路）。此时，选择策路就是选择概率，而博弈矩阵中给出的选项称为纯策略。

> 一般地，混合策路是一个概率分布，双策路情形等价为一个概率。

通常，在有两个纯策略$H$和$T$的情形，我们说

- 你的策路是概率$p$，是指你以概率$p$执行$H$;以概率$1-p$执行$T$。
- 他的策路是概率$q$，是指他以概率$q$执行$T$;以概率$1-q$执行$T$。

### 社会最优

社会福利：一个策略组对应的回报的总和。

社会最优和纳什均衡是有可能一致的，从社会应用的意义来讲，均衡与社会最优一致的系统是理想系统。

## 博弈的应用

- [ ] 交通管制（无差别）
- [ ] 拍卖（次价平衡拍卖）
- [ ] 匹配问题

### 交通管制

布雷斯悖论：多修一条路，人们出行状况反而更差。

### 拍卖

### 匹配问题

## 广告问题

- [ ] GSP
- [ ] VCG

搜索引擎的广告市场是上网时间、广告量与广告费之间的矛盾。

### 广告定价

搜索引擎通过出售网页上的广告位赚钱，广告主对每一次点击带来的收入有一个期望值，即期望点击率。搜索引擎的收入与广告主的期望点击率成正比，与广告主的出价成正比。

通常，不同的广告位点击率不同，于是本质上价格是不同的；不同广告主的点击估值也不一样，因此同一个广告位对于不同的广告主的价格也不一样。所以这是一个匹配市场，假设广告主对每个广告位的点击估值是已知的，那么广告主的出价就是已知的，就可以推演出广告位的社会最优价格。

一个问题是要对广告位定价还是点击定价，尽管广告主一般更关注一次点击（点击估值），但是广告位价格=点击价格*点击率，所以广告位的供应商可以计算出广告位的价格再计算出点击价格。

在由点击率和点击估值确定的估值矩阵情形，按照点击估值的高低配置对应的广告位达到社会最优。也就是说，这个完美匹配的对应形式不是偶然的。这种特定完美匹配可以如此证明：

- 对$n$个广告位$a_i$和$n$个广告主$x_i$，每个广告位有一个点击率$r_i$假设${r_i} \gt {r_j}, i \lt j$，每个广告主有一个点击收入估值$p_i$假设${p_i} \gt {p_j}, i \lt j$。最终形成一个对广告位的估值矩阵$V = \{{v_{ij}}\}, {v_{ij}}={p_i}*{r_j}$。

- 可以断言，在如此估值矩阵下，对应匹配市场的社会最优配置方案一定是$a_i$匹配给$x_i$。  

- 这等价于说估值矩阵对角线元素之和是其中任意n个不同行不同列元素之和的最大值。

- 证明（反证法）：假设最优配置对应的不是断言中的那种匹配，则一定有一个最小的$k$，使得$x_k$匹配到$a_i$，其中$k\lt i$。不失一般性，我们设$k＝1$。下面说明在这种情况下存在另一种配置，对应较大的社会福利，从而说明假设错误，即最优配置对应的一定是断言中的那种匹配：

  - 设$x_1$没有和$a_1$匹配而是和$a_i$匹配（$i\gt 1$），那么一定有$a_1$被$x_j$匹配（$j\gt 1$），此时这两个匹配对社会福利的贡献是${v_{1i}+{v_{j1}}}$。

  - 现在考虑交换这两个匹配的效果（其他匹配关系都不动），即让$p_1$和$a_1$匹配，$p_j$和$a_i$匹配，此时这两对匹配对社会福利的贡献是$v_{11}+v_{ji}$。

  - 注意到估值矩阵的生成规则，点击率和点击收入分别的序关系，我们有：
    $$
    \begin{aligned}
    (v_{11}+v_{ji})-(v_{1i}+v_{j1})&=p_1*r_1+p_j*r_i-p_1*r_i-p_j*r_1\\
    &=p_1*(r_1-r_i)+p_j*(r_i-r_1)\\
    &=p_1*(r_1-r_i)+p_j*(r_i-r_1)\\
    &\gt 0
    \end{aligned}
    $$

### GSP定价

GSP: 单品次价拍卖机制的一种“自然”推广。GSP相当于是说；如果你的出价最高，则意味着你最看重这类商品的价值，于是你应该得到价值最高的商品，而且我们让你用次高的报价得到它。

在多个商品同时拍卖的情形，如此推广的一种次价拍卖规则（GSP）没有单品次价拍卖（鼓励真实报价）的优良性质。

在单品次价拍卖场景下，支付价的另一种含义其实是补偿给由于中标者的出现给其他人（集体）带来的价值损失。在多个商品同时拍卖的情形下，这种补偿的含义就不明确了。

### VCG定价机制

让每个人支付的价格等于他的出现对其他人造成的价值损失总和。具体来说：

1. 按照出价的高低，将广告位（按点击率递减顺序）分给广告主。
2. 对于广告主 X ，他的支付价格如下确定
   - 设，在该匹配中，其他人的出价总和为Σ1
   - 从广告主集合中去掉 X，其他人按照出价形成新的最优匹配（对应一个新的出价总和Σ2）
   - Σ2−Σ1 就是 x要支付的价格（VCG价格）

## 权力分配

- [ ] 稳定结果
- [ ] 纳什议价解

经典的权力定义（Weber）：迫使他人服从自己意志的能力。权力是有关系性的；不能单独说某人有权力，而应该是说在什么关系中，某人更有权力。

在社会网络中的权力是指某个节点：其他节点对其具有依附性；它具有排他性，饱和性，中心性；处于结构洞位置上的节点。

讨论两个节点之间的权力关系，可以将网络中其他部分的影响归结为一个外部选项。

### 纳什议价解

结果：给定一个图，“结果”是其中的一个匹配（没有节点冲突的边子集），以及每个节点在$[0,1]$之间的一个价值，满足：

- 如果节点$u$和$v$对应匹配中的一条边，则它们赋值之和为1
- 如果节点$u$不涉及到匹配中任何边，则它的赋值为0

不稳定因素是不在结果中的两端节点的价值之和小于1的边，不包含不稳定因素的结果称为稳定结果。

纳什议价解是指在一个博弈中，参与者通过协商达成的一个结果。纳什议价解的一个重要性质是：它是对称的，即对于任何一个参与者，他的议价解都是一样的。

纳什的理论结果是双方均满足于均分$s$，即$s=1-x-y$。对于$A$，$x+s/2 = (x+1-y)/2$；对于$B$，$y+s/2 = (1-x+y)/2$。

给定一个结果，我们可得到每一个节点的外部选项，进而可以根据结果中节点的赋值算得匹配中每条边上的交换是否满足纳什议价解。

平衡结果（定义）：结果中匹配的每条边上的价值划分都满足纳什议价解

> 不平衡的结果在实验中不太可能出现。

综上，稳定结果是平衡结果的一个子集。

对于任意网络结构，存在算法可以计算出平衡结果。

## 从众与流行

- [ ] 信息级联（贝叶斯公式）
- [ ] 幂律问题

从众是在持有私有信息的情况下，逐个进行决策时会发生的一种现象。在信息级联的情况下，从众是一种理性的行为。

信息级联是从众现象背后的机制。

贝叶斯公式是分析信息级联的重要工具。在依次决策的情况下，每个人参考的概率是考虑到前面所有信息的一个条件概率，此时的概率结果取决于：

- 私有信号的个数
- 私有信号数相同时两种概率相同
- 私有信号多出一个时，相应概率会高于$\frac{1}{2}$

对于后面的人，他们对于前面人的私有信号：

- 未形成信息级联时：判断和自己的私有信号一致
- 形成级联时：忽略自身信号，判断和前面人的信号一致

由此，我们可以得出以下结论：

1. 当一个私有信号比另一个多出两个时就会形成信息级联
2. 形成级联前后人能够推测前人的私有信号，级联形成后便不能准确猜测

将决策模型通用化之后，可以得到两个结论：

1. 对于$Pr[G|{s_1},{s_2},{s_3},...,{s_n}]$私有信号序：
   - 如果$H$数量多于$L$，则$Pr[G|{s_1},{s_2},{s_3},...,{s_n}]$会高于$\frac{1}{2}$
   - 如果$H$数量少于$L$，则$Pr[G|{s_1},{s_2},{s_3},...,{s_n}]$会低于$\frac{1}{2}$
   - 如果$H$数量等于$L$，则$Pr[G|{s_1},{s_2},{s_3},...,{s_n}]$会等于$\frac{1}{2}$

2. 对于$Pr[G|{r_1},{r_2},{r_3},...,{r_N}$判断结果序：
   - 对于第$k+1$个人，如果两种信号相同则按照$s_{k+1}$，个数相差1也按照$s_{k+1}$，个数相差2则按照忽略自己的信号形成级联
   - 当$N\rightarrow \infty$时，信号个数相差2的概率为1，也就是说一定会发生信息级联

## 新事物扩散

- [ ] 如何更快扩散
- [ ] 知晓/行动

### 网络中的扩散

一个场景是在社会网络中，可能会存在A、B两种新、旧事物。每个人只能采纳A或B中的一个，两个相邻的人如果都采用A则得到回报a，都采用B则得到回报b，若不同则回报0。

这个问题可以表达为两个相邻节点之间的博弈，可以得到这样的表格：

|      | A    | B    |
| ---- | ---- | ---- |
| A    | a,a  | 0    |
| B    | 0    | b,b  |

对于任意一个节点$v$，假设他有$d$个邻居，占比$p$的邻居选择A，占比$1-p$的邻居选择B。那么$v$选择A的收益为$pda$，选择B的收益为$(1-p)db$。当$p>\frac{b}{a+b}$时，$v$选择A的收益大于选择B的收益，因此$v$会选择A。

我们可以直观的发现，有些节点选择了相同的类型，因此有很强的聚集性。可以使用“聚簇/抱团”：称一个节点集合为密度为$r$的聚簇，若其中每个节点至少有占比为$r$的网络邻居也属于这个节点集合，也就是每个节点的好友出现在这个集合中的比例。

聚簇和级联关系存在以下定理：

- 设网络中一个初用节点集采用 A，剩余网络的其他节点采用 B ，且它们改用A的门槛值为$q$
- 如果剩余网络中包含一个密度大于$1-q$的聚族，则这个初用节点集不能形成A的完全级联
- 而且，如果一个初用节点集不能形成一个完全级联，则剩余网络一定包含一个密度大$1-q$的聚簇

他的证明如下：

- 若剩余网络中存在密度大于$1-q$的聚簇：

  > 其中每个节点（选择为B）都有至少$1-q$占比的邻居在这个聚簇中。因此不会有节点首先改变选择，因此这个初用节点集不能形成A的完全级联

- 若级联形不成

  > 剩下的每个节点都有大于$1-q$的占比的邻居仍然在采用B，因此他们就可以形成一个密度大于$1-q$的聚簇

异值门槛：每个节点的门槛值不同。

异值门槛阻碍聚组是因为：剩余网络中的一个节点集合，任何一个节点$v$都有超过$1-{q_v}$的邻居在这个集合中，因此$v$不会改变选择，因此这个节点集合不能形成一个完全级联。由此可以推广行程完全级联的充要条件：不能形成完全级联，当且仅当剩余网络中存在一个阻碍聚簇。

对于一个社会网络中的每个节点，各自可以了解邻居节点的门槛值。此时节点的态度知晓受限于网络结构，行动则会成为公共知识。

## 信息不对称

- [ ] 内生事件市场
- [ ] 外生事件市场
- [ ] 柠檬市场

制度下群体行为有两种基本区分：

- 外生事件：该事件如何发生与人们的行动情况无关
- 内生事件：该事件如何发生直接取决于人们的行动情况

在市场上，根据参与者的行动对市场动态的影响，可以相应的分为两类：

- 外生事件市场：事件发生的概率不受行动者参与行为影响
- 内生事件市场：事件发生的概率受到行动者参与行为影响

市场失效：没能达成成交协议，商品没有卖出去。

> 可能因为信息不准确或缺乏导致估值不准确。

### 市场的基本认识

对于一个投资，如果收益与获胜概率的乘积大于投资成本，则投资者会进行投资，并且是All in。

然而，事实上随着金钱数量的增长，收入给人带来的效用会递减，相同金额的风险则会带来更大的效用损失。此时通常会用斜率递减的模型来描述效用函数，例如对数效用函数。可以证明，这种情况下，投在财富A上的占比最好就是A获胜的概率。

状态价格：赔付率的倒数。体现出群体对一个选项的信念的聚合，即对每人信念的“加权平均”，这个“权”就是一个人的财富在总财富量中的占比。

当个人的智慧与“群众智慧“一致时，总不会吃亏。

### 内生事件

信息不对称：内生事件市场的复杂性。信息弱势的一方会在考虑到另一方有充分信息的情形下形成对交易商品质量的预期，并按照预期决定自己愿意支付（接受）的期望价格。

### 柠檬市场

柠檬市场：市场上存在一些质量差的商品，而且买方无法区分质量好坏，因此买方愿意支付的价格会低于质量好的商品的价格。他的要点在于：

- 市场中的商品有多个质量等级
- 买家和卖家对每一等级商品有不同的底线价格（设同一等级中买家估值>卖家底价）
- 买卖双方对每一具体商品的质量信息不对称
- 因此买家只可能出一个期望价格，卖家按照所持有具体商品的底价与买家给出的价格的关系决定是否出售（期望价格与不同等级商品的占比分布和估值有关）

市场失效就是当质量较差商品占比太高时，买卖底价差太小。

## 表决

- [ ] 中位项原理
- [ ] 单峰偏好
- [ ] 投票规则

表决是通过众人投票，形成对事物的群体判断。他是一种基本的制度，体现在社会生活的方方面面。合理的表决制度需要体现集体的偏好，并且不容易被个别人的投票操纵。

### 偏好关系和群体偏好

假设每个表决者$({V_1},{V_2},...,{V_n})$都有一个偏好关系，最终按照少数服从多数汇聚成的结果未必是完备且传递的，因此需要对偏好关系进行限制。

孔多塞悖论：一般地，从传递性个体偏好，按少数服从多数聚合方式，得出了非传递的群体偏好，称为孔多塞悖论。

> 孔多塞在1700年研究表决问题的时候，发现在3个人对3个备选项（X,Y,Z）进行表决的场合，有可能每人偏好都满足完备性和传递性，但按少数服从多数原则得到的群体偏好却不一定满足传递性。

由此对聚合规则提出两个要求：

1. 趋同性：对于任意2个侯选项$（X, Y）$，如果所有个体都偏好X，则群体排序结果中也应该偏好X，称之为趋同性原理

2. 独立于无关项（IIA）：群体对侯选项$（X, Y）$的排序，仅取决于个体对它们的偏好，与个体对其他侯选项的看法无关。

   > 即，X和Y在群体排序中的结果，不能因为某一个个体调整了某个 Z 的相对位置而改变

阿罗不可能定理：在3个或更多侯选项的条件下，任何多于2人参与的表决系统，都不可能同时满足（1）趋同性；（2）IIA（独立于无关选项）；（3）非独裁性。换句话说，若满足了（1）和（2），则群体排序一定就等于某个个体的排序。

### 单峰偏好

表决者的态度应该满足单峰偏好指的是：

- 若$X_i$被他排在了第一位，则对于$k \lt j \lt i$，$X_j$要排在$X_k$的前面；且对于$i \lt j \t k$，$X_j$要排在$X_k$的前面
- 即选择了一个“最爱”后，对其他选项的偏爱程度应参照所关注的性质在两边随与这个最爱的距离下降（左右两边相互之间没有要求）

定理：弱所有选举人的排序都满足：单峰偏好“，则按照少数服从多数规则两两比较候选项产生的群体偏好是完备且传递的。

一种构造方式是逐次找出“最大的”（群体意义）：

1. 记${L_1},{L_2},...,{L_M}$为个体排序表，${L_i(1)}$为对应个体表中的第一个（最大的）元素。
2. 将$L_i(1), i=1,2,...,M$按照${X_1},{X_2},...,{X_N}$的顺序排列
3. 从如此排列的$M$个元素中取中间项作为群体排序的第一项
4. 从${L_1},{L_2},...,{L_M}$中删除该元素，留下的依然是单峰排序表，接着可以依次取出第二个。

中位项定理：相继取出的中位项，在少数服从多数的原则下，比所有其他剩下的候选项都要大。

### 信号

以美国陪审团裁定制度为例，在有罪信号下有罪的概率大于0.5，无罪信号下无罪的概率也大于0.5，且其他人都诚实投票时，你的一票有作用的情形仅限于：所有人都得到有罪信号，你得到无罪信号。然而，无论得到什么信号，总投“有罪”票可能利于集体形成正确裁决。

因此，为了避免影响“大局”，人们会倾向于忽略得到的无辜信号。这就是美国陪审团制度的一个弱点

## 扩散和流行病学

- [ ] SIR模型

### 基本再生数和二分法

基本再生数$R_0$，当$R_0 \gt 1$时，疾病会流行，当$R_0 \lt 1$时，疾病会消失。如果$R_o$接近1，疾病会呈现“刀刃”特性

### SIR模型

- S（Susceptible）：易感期
- I（Infectious）：传染期
- R（Removed）：移出期

演变过程为：

1. 最初一些节点处于I状态，其余节点处于S状态
2. 每个进入I的节点$v$在固定的步骤$t_1$期内具有传染性
3. 在$t_1$期后，以概率$p$将疾病传染给他的处于易感期的邻居
4. 经过$t_1$步后，节点$v$进入R状态，不再具有传染性

在这个过程中，$S(t)+I(t)+R(t)=N$，$N$为网络中的节点数。其微分方程为：

$$
\begin{cases}
\frac{dS}{dt}=-\Beta SI \\
\frac{dI}{dt}=\Beta SI-\gamma I \\
\frac{dR}{dt}=\gamma I
\end{cases}
$$

### SIS模型

- S（Susceptible）：易感期
- I（Infectious）：传染期

演变过程为：

1. 最初，一些节点处于状态I，其他节点状态S
2. 每个进入I的节点$v$在固定的步骤$t_1$期间内具有传染性
3. 在$t_1$的每一步，以概率$p$将疾病传染给它的处于状态S的邻居
4. 经过$t_1$步后，节点$v$不再具有传染性，返回状态S

在这个过程中，$S(t)+I(t)=K$，$K$为网络中的节点数。其微分方程为：

$$
\begin{cases}
\frac{dS}{dt}=-\Beta SI \\
\frac{dI}{dt}=\Beta SI-\gamma I
\end{cases}
$$

其指数增长率$r=\Beta K$，因此可以导出：

$$
\frac{dI}{dt}=r I(1-\frac{I}{K})
$$

这说明在传染率一定时，总人数$k$越多，疾病传播的速度越快，当$I=\frac{K}{2}$时，传播速度最快。

### SIRS模型

演变过程为：

1. 最初，一些节点处于状态I，其他节点状态S
2. 每个进入I的节点$v$在固定的步骤$t_1$期间内具有传染性
3. 在$t_1$的每一步，以概率$p$将疾病传染给它的处于状态S的邻居
4. 经过$t_1$步后，节点$v$不再具有传染性，进入R状并在步骤数$t_r$期间维持在该状态，之后回到S状态

在这个过程中，$S(t)+I(t)+R(t)=K$，$K$为网络中的节点数。其微分方程为：

$$
\begin{cases}
\frac{dS}{dt}=-\Beta SI+\alpha R \\
\frac{dI}{dt}=\Beta SI-\gamma I \\
\frac{dR}{dt}=\gamma I-\alpha R
\end{cases}
$$

此处的$\alpha$表示康复者获得免疫的平均保持时间。在这个系统内有两个不动点$S=N(I=R=0)$和$S=\frac{\gamma}{\Beta}(\frac{I}{R}=\frac{\gamma}{\alpha})$，前者表示从研究地区消除疾病，后者表示疾病流行的稳定状态。消除前者的参数条件是$\gamma \gt \Beta N$，若做不到则要尽量增加$\gamma$。

### SEIR模型

- S（Susceptible）：易感期
- E（Exposed）：潜伏期
- I（Infectious）：传染期
- R（Removed）：移出期

### 线粒体夏娃