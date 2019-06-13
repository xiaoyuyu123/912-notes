Conclusion on Chapter Six: Graph
================================

## 图的一些基本概念与术语

> 什么是图？为什么要有图这种数据结构？

图是一种更一般的数据结构，根据马克思辩证唯物主义中[万物的普遍联系与永恒发展]的观点，图正是表示了一个集合中所有元素之间相互联系关系的一种数据结构，因此更具有普遍性，理论上可以描述宇宙中的所有关联关系。

从结构上来看，图相对于前面介绍过的`Vector`，或者`Tree`，也具有更一般的结构性质。`Vector`是一个线性结构，在`Vector`中，只能描述一个元素与它的前驱/后继之间的关联关系；`Tree`是一个半线性结构，可以描述一个元素与它的父结点以及若干子结点之间的关系，相对于`Vector`更加普遍，而`Tree`的退化形式即成为了一个`List`，从而证明了这里的观点。图是一个非线性结构，可以表示一个结点与集合中所有元素的关联关系，采用类比的方法，我们可以归纳出研究图的一般方法--将图转化为我们熟悉的树，从而借用树的分析技巧来解决图的问题。

> 图的分类。

图的分类比较复杂，这里只讨论几个容易混淆的概念。

简单的概念，一般是表示不含有环路。

+ 简单图：不含有自环的图。
+ 简单通路：沿途顶点互异的通路。
+ 简单环路：除起点和终点外沿途所有顶点均互异的环路。

欧拉环路：经过图中各边一次且恰好一次的环路。比如说一笔画问题。
哈密尔顿环路：经过图中各顶点一次且恰好一次的环路。

## 图的表示

由于图是表示一个集合中所有元素相互之间的关联关系的数据结构，因此表达这里的关联关系是表示图的重点。元素之间的关联关系我们又称之为邻接关系，对应有邻接矩阵，邻接表两种表示方法。除此以外，图中还存在顶点和边之间的关系，这种关系我们称之为关联关系，对应有关联矩阵表示法。

> 邻接矩阵表示法。

邻接矩阵是表示图中的邻接关系。简单说来，就是引入一个$n\times n$的矩阵，矩阵中的每个元素代表相应的两个顶点之间是否有邻接关系，或者这种邻接关系的强度。

> 邻接表表示法。

上面的邻接矩阵表示法其实具有比较大的冗余，因为它需要$O(n^2)$的空间，而实际中的图边往往渐进的不超过$O(n)$。

邻接表其实基本思想和邻接矩阵完全相同，都是表示图的这种邻接关系，但是它是采用一个列表来表示与某个顶点相关联的其他顶点，这样需要的空间总量为$O(n+e)$，与图本身的规模相当，这已经是相当好的结果了。

与邻接矩阵相比，邻接表具有更好的动态性能，而邻接矩阵的静态性能则更优。例如邻接矩阵可以在$O(1)$时间内访问任意两个顶点的邻接关系，或者判断它们是否存在邻接关系；而对于邻接表而言，则需要遍历某一个顶点的邻接表，在最坏情况下需要$O(n)$的时间。但是邻接表在诸如插入和删除这种动态性能上更优，这都是由于`List`以及`Vector`的特性决定的。

但是与邻接矩阵相比，邻接表还有一个巨大的优势，即可以快速地访问到一个顶点的所有邻居，分摊时间只需要$O(1)$；而对于邻接矩阵，则不得不完全遍历邻接矩阵的某一行或者某一列。这个优势使得依靠于遍历一个顶点的所有邻居的算法，如dfs和bfs，在采用邻接表表示的图上，具有更优的时间复杂度。

## 图的遍历

上面已经说过了，为了研究更一般的图结构，一个比较好的策略就是将它转化成我们熟悉树结构，从而可以应用树的策略与技巧。为了将图转化成树，就需要这里的图的遍历算法。

由于要将更一般的图，转化成相对特殊的树结构，整个过程必然会有信息的丢失。为了尽可能地保存图中的原有信息，除了组成遍历树(traversal tree)的边以外，还需要提供关于原图的其他信息，例如环路，前向边，后向边等。

> 广度优先搜索策略(bfs, Breadth First Search)。

对于当前的顶点，广度优先搜索，就是优先去访问它的邻居，等到它的邻居被访问完以后，再去访问邻居的邻居。它与树结构中的层次遍历相对应，因此这里bfs也需要引入一个队列，来存储当前顶点的全部邻居。

这里主要讨论一下遍历中可能会出现的几种情况。对于当前的顶点x，如果访问到它的邻居w时，w还是未被发现的，这意味着遍历树可以拓展一条边，因此将这两个顶点之间的边标记为'Tree'；但如果w已经被发现（访问）了，这种情况对应了

+ w是x的父结点。例如一开始就从w进入x，然后x在遍历它的邻居时又发现了w。这种情况下x与w之间的边是一条回边(Backward)，表示指向祖先顶点。要注意的是，这里的w只可能是x的父结点，而不可能是x更高的祖先结点。这是因为如果w是x比父结点更高的顶点的话，x早在访问w时就应该被发现了，从而w只能是x的父结点。
+ w是x祖先结点的邻居，经由x的祖先结点就已经访问到了w。这种情形对应了跨边(Cross)，表示x与w没有直接的血缘关系，来自相互独立的两个分支。

对于上面两种情况，由于指向父结点的回边感觉不是很有意义，我们在bfs中不加以区分，全部统一标记为跨边。因此bfs的算法可以表述如下：

```cpp
template <typename Tv, typename Te>
void Graph<Tv, Te>::BFS(int x, int& clock){
	Queue<int> Q;
	dtime(x)  = ++clock;
	status(x) = DISCOVERED; 
	Q.enqueue(x);
	while(!Q.empty()){
		x = Q.dequeue();
		for(int w = firstNeighbor(x); w != -1; w = nextNeighbor(x, w))
			if(status(w) == UNDISCOVERED){
				status(w)  = DISCOVERED;
				dtime(w)   = ++clock;
				parent(w)  = x;
				type(x, w) = TREE;
				Q.enqueue(w);
			}
			else type(x, w) = CROSS;
	}
	status(x) = VISITED;
}
```

可以看到，为了保存图原有的信息，算法中做了大量的标记工作。

一次BFS可以完全遍历以当前顶点x为起始的连通分量，但是如果图不连通的话，则需要做多次BFS，才能完成对全图的遍历。这时BFS遍历得到的不再是一棵遍历数，而是一个遍历森林。

```cpp
template <typename Tv, typename Te>
void Graph<Tv, Te>::bfs(int start){
	reset();
	int clock = 0;
	int curr = start;
	do{
		if(status(curr) == UNDISCOVERED) BFS(curr, clock);
	}while((curr = ++curr % num_of_vertices) != start);
}
```

> bfs算法复杂度的分析。

当采用邻接矩阵表示时，BFS中为了找到当前顶点的全部邻居，需要完全遍历邻接矩阵的一行，需要$O(n)$的时间,所有顶点都会入队一次，出队一次，故上面$O(n)$的遍历操作一共需要进行n次，故访问所有顶点需要$O(n^2)$的时间复杂度。此外，图中的每条边都需要进行一次标记，所以一共需要$O(n^2 + e) = O(n^2)$的时间。

而当采用邻接表表示图时，对于当前顶点访问到它的邻居只需要$O(1)$的时间，所有顶点都会被访问一次，所有边也会被访问一次，所以需要$O(n + e)$的时间，这个结果和图的规模相当，已经是期望的最优结果了。

但是，如果考虑到计算机体系结构中的缓存，当采用邻接矩阵表示时，邻接矩阵的结构很容易触发缓存机制，从而使邻接矩阵中一行数据全部存放到缓存中。由于缓存的速度往往是内存的百十万倍，这样，尽管从渐进的分析，仍然是$O(n^2)$的时间复杂度，但是前面的常数项已经非常小，可以忽略到为了找到全部邻居而需要的时间，从而仍然可以达到$O(n + e)$的时间复杂度。

> bfs的应用。

由上面所述，bfs显然可以用来进行连通性的检测。凡是连通的顶点都可以在一次BFS中被发现，因此对一个图进行bfs，调用BFS的次数，即是图的连通分量的个数。

此外，由bfs访问顶点的次序可以看出，bfs总是优先访问离当前顶点最近的顶点，这使得bfs可以用以发现图中的最短路径。但是这里的最短路径只能是拓扑结构的最短路径，或者说无权图，或者等权图的最短路径。

然后，邓公说还可以用来做连通分量的分解，无向图的环路检测，以后再探索一下。

> 深度优先搜索(dfs, Depth First Search)策略。

dfs其实等价于树的先序遍历。简单说来，就是尽可能深地去遍历下一个顶点，也就是所谓的深度优先遍历。

等价于bfs，dfs也会在遍历结束后生成一棵支撑树(Spaning Tree)。除此以外，dfs还需要保存原图的一些其他信息，也就是对应遍历结束后除了树边的其他类型的边，这里包括前向边(Forward)，回边(Backward)，以及跨边(Cross)，详细说明如下：

+ 树边：访问到状态仍然是`UNDISCOVERED`的邻居，它们之间的边构成支撑树的一条边。随后递归地进入该邻居继续dfs。
+ 回边：访问到状态是`DISCOVERED`的邻居。这意味着该邻居是当前顶点的一个祖先。该边指向一个祖先顶点，故称之为回边。不难看出，出现回边意味着图中存在环路。
+ 前向边：与回边相对应，表示指向后继结点的边。当访问到的邻居状态为`VISITED`，并且访问时间晚于当前顶点时，就会出现前向边。
+ 跨边：类似于bfs中出现过的跨边，表示当前顶点与被访问到的邻居没有直接血缘关系。当访问到的邻居状态为`VISITED`，并且访问时间早于当前顶点时，就会出现跨边。

需要注意的是，这里的边的信息仅限于有向图。这是因为，在无向图中，区分回边和前向边没有意义，因为有一条回边就会有一条后向边，两者完全等效。此外，无向图中不存在跨边（我说不清楚，自己思考一下吧）。

除了边的信息以外，根据上面的讨论，还需要记录各个顶点被发现以及被访问的时间，分别记为`dtime`和`ftime`。至此，DFS的代码可以表述如下：

```cpp
template <typename Tv, typename Te>
void Graph<Tv, Te>::DFS(int x, int& clock){
	dtime(x)  = ++clock;
	status(x) = DISCOVERED;
	for(int w = firstNeighbor(x); w != -1; w = nextNeighbor(x, w)){
		switch(status(w)){
			case UNDISCOVERD:
				parent(w) = x;
				type(x, w)= TREE;
				DFS(w, clock);
				break;

			case DISCOVERED:
				type(x, w) = BACKWARD;
				break;

			case VISITED:
			default:
				type(x, w) = dtime(x) < dtime(w)? FORWARD: CROSS;
				break;
		}
	}
	ftime(x)  = ++clock;
	status(x) = VISITED;
}
```

与bfs类似，当图不连通时，需要多次调用DFS才能完成全图的深度优先遍历。同样，这时得到的是一个DFS遍历森林。

DFS的时间复杂度也与BFS一致。

> dfs的括号引理。

根据各个顶点的发现时间`dtime`和访问时间`ftime`，可以对顶点进行分类，获得顶点之间的祖先与后代关系信息。

记`active(u) = [dtime(x), ftime(x)]`为顶点x在有向图G中的活跃期，括号引理即

+ 若$active(u) \subseteq active(v)$，则u是v的后代。
+ 若$active(u) \supseteq active(v)$，则u是v的祖先。
+ 若$active(u) \cap active(v) = \phi$，则u和v无关。

> dfs的应用。

dfs是图遍历算法中最重要的一个。大量与图相关的算法都是由dfs导出的，比如连通分量分解，拓扑排序等。此外，dfs还可以用来做带权图的最短路径算法框架。

## 拓扑排序

> 什么是拓扑排序？

事物之间往往会有一个依赖关系，因此形成一个了先后次序关系。比如我得先学好`Vector`和`List`，才能来学习依赖于两者的树，从而才能学习依赖于树的图结构。它们之间的这种次序就构成了一个拓扑顺序。

对于一般的有向图而言，就是要找到一个线性序列，使得对于任意个顶点x，排在它后面的顶点一定不会是当前顶点的前驱顶点。这个序列表示在访问x之前，x的所有前驱顶点一定要首先被访问。这个序列就是原图的一个拓扑排序(Topological Sort)。

要注意拓扑排序通常都是研究有向图，因为它反映了事物的一个先后次序。无向图不具有这样一个先后的次序，研究它的拓扑排序是没有意义的。

> 有向无环图与拓扑排序。

接下来要解决的一个问题就是，什么样的图具有拓扑排序？因为直观上来看，有环图必然是没有拓扑排序的。因此，接下来我们重要要研究的是就是有向无环图(DAG, Directed Acyclic Graph)。

那么，所有的DAG都具有拓扑排序吗？答案是是的，为了证明这个答案，我劝你还是学完离散数学再来吧......

> 拓扑排序的构造方法。

根据拓扑排序的定义进行构造。拓扑排序的每一步，都是寻找依赖项已经完成访问的顶点。因此，作为拓扑排序的起点，必然是一个入度为零的顶点，表示该顶点不依赖于任何其他顶点。从这里我们也可以看出，拓扑排序并不是唯一的，因为同时没有依赖的顶点可能有多个，此时它们都可以等效地加入拓扑排序的序列中。

在将任意一个准备被访问的顶点加入拓扑排序序列中后，相当于图中依赖于该顶点的其他顶点已经满足了对其的依赖关系，因此可以将该顶点从图中删除，以及删除与该顶点有联系的所有边。接下来的图仍然是一个DAG，从而可以将算法递归地进行下去，直到图中不再有任何顶点为止。

> 基于dfs的拓扑排序算法

其实，我们对比拓扑排序的定义以及dfs遍历的次序，可以发现它们之间具有某种相似性。对于拓扑排序而言，每次是选择入度为零的顶点加入拓扑排序序列；而对于dfs而言，只有当一个顶点的所有邻居都访问完毕后，这个顶点才会被标记`VISITED`，这样dfs中第一个被访问的顶点，必然是出度为零的顶点，而倘若没访问结束一个顶点，就将该顶点删除，那么dfs每一步都是访问出度为零的顶点。可以看出，dfs的访问次序，恰好是拓扑排序的逆序。

其实，上面的结论并不是偶然，而是由dfs的特性决定的--在dfs中，对于当前顶点x，总是需要优先访问完所有依赖于它的顶点，才能进行对x的访问。这恰好是拓扑排序的对称情况。

为了完成对图的拓扑排序，只需要逆序输出它的dfs访问序列就可以了。为此，我们需要引入一个栈用以延迟缓冲。

```cpp
template <typename Tv, typename Te>
bool Graph<Tv, Te>::tSort(int x, Stack<int> &S){
	status(x) = DISCOVERED;
	for(int w = firstNeighbor(x); w != -1; w = nextNeighbor(x, w)){
		switch(status(w)){
			case UNDISCOVERED:
				if(!tSort(w, S)) return false;
				break;

			case DISCOVERED:
				return false;//cyclic graph

			case VISITED:
			default:
				break;
		}
	}
	status(x) = VISITED;
	S.push(x);
}
```

代码执行结束后，拓扑排序序列就存储在栈`S`中。需要注意的是，利用dfs的环路检测的性质，可以轻易地判断当前图是否是一个DAG，一旦发现一个环路，就可以立即终止循环，并且报告不可拓扑排序。

由于该算法是采用dfs的框架，其时间复杂度也和dfs一样，为$O(n + e)$。

## 双连通域的分解

## 优先级搜索

## 最小支撑树

> 什么是最小支撑树(MST, Minimal Spanning Tree)

沿用之前对树的定义，树是一个极大无环图和极小连通图。最小支撑树也满足这样的定义。

对于任意一个无向连通图而言，例如若干城市和它们之间的道路组成的网络，从某一个城市出发到达另一个城市往往具有若干条不同的道路，所谓条条大路通罗马。这个网络的一棵支撑树，就是只通过最少的路径数，就能将该网络的所有城市都连接起来的若干通路。如果再考虑这些不同的通路之间具有不同的权重，例如时间成本不同，具有最小权重和的一棵支撑树，就是最小支撑树(MST)。

> 如何建立连通图的最小支撑树？

据邓公所说，蛮力算法需要$O(n^{n-2})$的时间复杂度，是根据Cayley公式，我还比较懵......

首先考虑一种较为一般的情况。考虑图G顶点集V，V的一个非平凡子集U以及U的补集V\U构成了G的一个割。最小支撑树总是会采用每一割的最短跨越边。

根据上面的性质，就可以得到构造最小支撑树的算法：总是将原图视作一个割，两个顶点集分别是已经加入到最小支撑树中的顶点和未加入的顶点。通过找到这个割的最短跨越边，从而将一个新的顶点加入到最小支撑树中。这样不断地迭代，直到最小支撑树覆盖了全图的所有顶点。

这里比较复杂的问题是，如何快速地找到当前割的最短跨越边，为此，可以模仿dijkstra算法，维护一个数组，来保存所有未加入最小支撑树的顶点到最小支撑树的路径，每加入一个新的顶点，将这个数组进行更新。这里并没有真正地用一个数组，而是用每个顶点的`priority`字段来保存这个信息。

```cpp
template <typename Tv, typename Te>
void Graph<Tv, Te>::primPU(int x){
	for(int w = firstNeighbor(x); w != -1; w = nextNeighbor(x, w))
		if(status(w) == UNDISCOVERED){
			if(weight(x, w) < priority(w)) priority(w) = weight(x, w);
			parent(w) = x;
		}
}
```

通过优先级搜索的框架，即可以完成最小支撑树的生成。

## 最短路径

最短路径的定义与应用意义不言而喻，我们直接讨论如何求得最短路径的算法。

> 最短路径树

在任意一个带权网络中，考察从源点到其余各个顶点的最短路径，它们之间并不组成任何回路。因此将它们组合在一起，可以构成最短路径树(SPT, Shortest Path Tree)。

需要注意的是，SPT和MST不一样。

> Dijkstra算法

首先来考虑最短路径具有的若干性质。

考虑从源点s到任意顶点v的最短路径，其路径上还有若干其他顶点。那么该路径上从s到这些顶点的一段，也是从s到这些顶点的最短路径。这个性质是容易证明的，因为否则的话，从s到v还有一条更短的路径，这与一开始的假设矛盾。

从上面的性质可以看出，为了构造从s到某一顶点的最短路径，首先需要构造从s到该顶点路径上更前面顶点的最短路径。将图中各点按距离s的远近次序由近到远排个序，就构成了最短路径子树序列。
因此为了构造从源点s到所有其他顶点的最短路径，需要依次找到距离s最近的$u_1, u_2, ..., u_k$，从而完成最短路径树的构造。

+ 首先需要找到距离s最近的顶点$u_1$。实际上，$u_1$是s的邻居中距离s最近的顶点。这是因为，倘若存在s的非邻居顶点$x$，距离s的距离比$u_1$更近，那么它必然通过某个顶点$y$与s连接，$y$是s的邻居顶点。所以，$x$到s的距离为
$$
dist(x, s) = dist(x, y) + dist(y, s)
$$
而
$$
dist(y, s) > dist(u_1, s)
$$
所以$x$到s的距离实际上比$u_1$远，故$u_1$才是距离s最近的顶点。

+ 已知$u_k$，找到接下来距离s最近的顶点$u_{k + 1}$。这个顶点就是所有的与
s以及$u_1$到$u_k$连通的顶点中，距离s最近的一个。这个的证明和前面的原理一致，可以自己试试。

至此，我们已经归纳地证明了最短路径树序列的构造方法，而这个算法，就是Dijkstra算法。

同样地，找到当前的具有最短距离的顶点，具有一定的困难。为此，同样引入一个数组来保存前面已经保存了的距离信息，一旦有新的顶点加入最短路径序列中，就将这个数组进行更新。

```cpp
template <typename Tv, typename Te>
void Graph<Tv, Te>::dijkstraPU(int x){
	for(int w = firstNeighbor(x); w != -1; w = nextNeighbor(x, w)){
		if(status(w) == UNDISCOVERED){
			if(weight(x, w) + priority(x) < priority(w))
				priority(w) = weight(x, w) + priority(x);
				parent(w)   = x;
		}
	}
}
```

