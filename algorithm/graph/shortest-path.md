> 来自[OI Wiki](https://oi-wiki.org/graph/shortest-path/)

## 不同方法的比较

|最短路算法| Floyd| Bellman–Ford| Dijkstra| Johnson|
|:--:|:--:|:--:|:--:|:--:|
|最短路类型| 每对结点之间的最短路| 单源最短路 |单源最短路 |每对结点之间的最短路|
|作用于 |任意图 |任意图 |非负权图| 任意图|
|能否检测负环？ |能 |能 |不能| 能|
|时间复杂度 |$O(N^3)$ |$O(NM)$| $O(M\log M)$| $O(NM\log N)$|

## Floyd 算法

### 适用场景

Floyd 算法（Floyd-Warshall algorithm）是用来求**任意两个结点**之间的最短路的算法。

复杂度$O(n^3)$比较高，但是容易实现（只有三个 for）。

适用于任何**没有负环**的图，不管有向无向，边权正负，但是最短路必须存在。

### 实现

我们定义一个数组$f[k][x][y]$，表示只允许经过结点 1 到 k（也就是说，在子图$V'={1, 2, \ldots, k}$中的路径，注意，x 与 y 不一定在这个子图中），结点 x 到结点 y 的最短路长度。

很显然，$f[n][x][y]$就是结点 x 到结点 y 的最短路长度（因为$V'={1, 2, \ldots, n}$即为 V 本身，其表示的最短路径就是所求路径）。

接下来考虑如何求出 f 数组的值。

$f[0][x][y]$：x 与 y 的边权，或者 0，或者$+\infty（f[0][x][y]$什么时候应该是 +\infty？当 x 与 y 间有直接相连的边的时候，为它们的边权；当 x = y 的时候为零，因为到本身的距离为零；当 x 与 y 没有直接相连的边的时候，为 +\infty）。

$f[k][x][y] = min(f[k-1][x][y], f[k-1][x][k]+f[k-1][k][y])$ ($f[k-1][x][y]$ 为不经过 k 点的最短路径，而$f[k-1][x][k]+f[k-1][k][y]$为经过了 k 点的最短路)。

上面两行都显然是对的，所以说这个做法空间是 O(N^3)，我们需要依次增加问题规模（k 从 1 到 n），判断任意两点在当前问题规模下的最短路。

> 因为第一维对结果无影响，我们可以发现数组的第一维是可以省略的，于是可以直接改成$f[x][y] = min(f[x][y], f[x][k]+f[k][y])$。

<!-- tabs:start -->
#### **CPP**

```cpp
for (int k = 1; k <= n; k++) {
    for (int x = 1; x <= n; x++) {
        for (int y = 1; y <= n; y++) {
            f[x][y] = min(f[x][y], f[x][k] + f[k][y]);
        }
    }
}
```

#### **JAVA**

```java
for (int k = 1; k <= n; k++) {
    for (int x = 1; x <= n; x++) {
        for (int y = 1; y <= n; y++) {
            f[x][y] = Math.min(f[x][y], f[x][k] + f[k][y]);
        }
    }
}
```
<!-- tabs:end -->

## Dijkstra 算法

### 适用场景

Dijkstra是一种求**非负权**上**单源**最短路径的算法。

### 过程

将结点分成两个集合：已确定最短路长度的点集（记为 S 集合）的和未确定最短路长度的点集（记为 T 集合）。一开始所有的点都属于 T 集合。

初始化 dis(s)=0，其他点的 dis 均为 $+\infty$。

然后重复以下操作：

1. 从 T 集合中，选取一个最短路长度最小的结点，移到 S 集合中。
2. 对那些刚刚被加入 S 集合的结点的所有出边执行松弛操作。

直到 T 集合为空，算法结束。

### 实现

暴力做法实现:$O(n^2)$

<!-- tabs:start -->
#### **CPP**

```cpp
struct edge {
    int v, w;
};

vector<edge> e[maxn];
int dis[maxn], vis[maxn];

void dijkstra(int n, int s) {
    memset(dis, 0x3f, (n + 1) * sizeof(int));
    dis[s] = 0;
    for (int i = 1; i <= n; i++) {
        int u = 0, mind = 0x3f3f3f3f;
        for (int j = 1; j <= n; j++){
            if (!vis[j] && dis[j] < mind) {
                u = j, mind = dis[j];
            }
        }     
    }
    vis[u] = true;
    for (auto ed : e[u]) {
        int v = ed.v, w = ed.w;
        if (dis[v] > dis[u] + w) dis[v] = dis[u] + w;
    }
}
```
<!-- tabs:end -->
优先队列优化实现:$O(mlogm)$
<!-- tabs:start -->

#### **CPP**

```cpp
struct edge {
    int v, w;
};

struct node {
    int dis, u;
    bool operator>(const node& a) const { return dis > a.dis; }
};

vector<edge> e[maxn];
int dis[maxn], vis[maxn];
priority_queue<node, vector<node>, greater<node> > q;

void dijkstra(int n, int s) {
    memset(dis, 0x3f, (n + 1) * sizeof(int));
    dis[s] = 0;
    q.push({0, s});
    while (!q.empty()) {
        int u = q.top().u;
        q.pop();
        if (vis[u]) continue;
        vis[u] = 1;
        for (auto ed : e[u]) {
            int v = ed.v, w = ed.w;
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.push({dis[v], v});
            }
        }
    }
}
```

#### **JAVA**

```java
/**
 * 
 * @param n 节点数[0, n)
 * @param s 起点
 * @param edges 邻接表 eges[i]: 表示所有与节点i相邻的[节点，对应的边权]
 * @return 所有节点到起点的最短距离
 */
int[] dijkstra(int n, int s, List<List<int[]>> edges) {
    int[] dis = new int[n + 1];
    boolean[] vis = new boolean[n + 1];

    Arrays.fill(dis, Integer.MAX_VALUE / 2);
    dis[s] = 0;

    // 0 - dis, 1 - v
    PriorityQueue<int[]> q = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    q.offer(new int[] { 0, s });

    while (!q.isEmpty()) {
        int u = q.poll()[1];
        if (vis[u]) {
            continue;
        }
        vis[u] = true;

        for (int[] ed : edges.get(u)) {
            int v = ed[0], w = ed[1];
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.offer(new int[] { dis[v], v });
            }
        }
    }
    return dis;
}
```
<!-- tabs:end -->

## Bellman–Ford 算法

### 适用场景

Bellman–Ford 算法是一种基于松弛（relax）操作的求解**负权**上**单源**最短路算法，可以求出有负权的图的最短路，并可以对最短路不存在的情况进行判断。

在国内 OI 界，你可能听说过的「SPFA」，就是 Bellman–Ford 算法的一种实现。

### 过程

先介绍 Bellman–Ford 算法要用到的松弛操作（Dijkstra 算法也会用到松弛操作）。

对于边 (u,v)，松弛操作对应下面的式子：$dis(v) = \min(dis(v), dis(u) + w(u, v))$。

这么做的含义是显然的：我们尝试用 $S \to u \to v$（其中 $S \to u$ 的路径取最短路）这条路径去更新 v 点最短路的长度，如果这条路径更优，就进行更新。

Bellman–Ford 算法所做的，就是不断尝试对图上每一条边进行松弛。我们每进行一轮循环，就对图上所有的边都尝试进行一次松弛操作，当一次循环中没有成功的松弛操作时，算法停止。

每次循环是 O(m) 的，那么最多会循环多少次呢？

在最短路存在的情况下，由于一次松弛操作会使最短路的边数至少 +1，而最短路的边数最多为 n-1，因此整个算法最多执行 n-1 轮松弛操作。故总时间复杂度为 O(nm)。

但还有一种情况，如果从 S 点出发，抵达一个负环时，松弛操作会无休止地进行下去。注意到前面的论证中已经说明了，对于最短路存在的图，松弛操作最多只会执行 n-1 轮，因此如果第 n 轮循环时仍然存在能松弛的边，说明从 S 点出发，能够抵达一个负环。

> **负环判断中存在的常见误区**
> 需要注意的是，以 S 点为源点跑 Bellman–Ford 算法时，如果没有给出存在负环的结果，只能说明从 S 点出发不能抵达一个负环，而不能说明图上不存在负环。
> 因此如果需要判断整个图上是否存在负环，最严谨的做法是建立一个超级源点，向图上每个节点连一条权值为 0 的边，然后以超级源点为起点执行 Bellman–Ford 算法。

### 代码实现

<!-- tabs:start -->
#### **CPP**

```cpp
struct Edge {
  int u, v, w;
};

vector<Edge> edge;

int dis[MAXN], u, v, w;
const int INF = 0x3f3f3f3f;

bool bellmanford(int n, int s) {
  memset(dis, 0x3f, (n + 1) * sizeof(int));
  dis[s] = 0;
  // 用于判断一轮循环过程中是否发生松弛操作
  bool flag = false;  
  for (int i = 1; i <= n; i++) {
    flag = false;
    for (int j = 0; j < edge.size(); j++) {
      u = edge[j].u, v = edge[j].v, w = edge[j].w;
      // 无穷大与常数加减仍然为无穷大
      // 因此最短路长度为 INF 的点引出的边不可能发生松弛操作
      if (dis[u] == INF) continue;
      if (dis[v] > dis[u] + w) {
        dis[v] = dis[u] + w;
        flag = true;
      }
    }
    // 没有可以松弛的边时就停止算法
    if (!flag) {
      break;
    }
  }
  // 第 n 轮循环仍然可以松弛时说明 s 点可以抵达一个负环
  return flag;
}
```

#### **Python**

```python
def dijkstra(e, s):
    """
    输入：
    e:邻接表
    s:起点
    返回：
    dis:从s到每个顶点的最短路长度
    """
    dis = defaultdict(lambda: float("inf"))
    dis[s] = 0
    q = [(0, s)]
    vis = set()
    while q:
        _, u = heapq.heappop(q)
        if u in vis:
            continue
        vis.add(u)
        for v, w in e[u]:
            if dis[v] > dis[u] + w:
                dis[v] = dis[u] + w
                heapq.heappush(q, (dis[v], v))
    return dis
```

#### **JAVA**

```java
import java.util.*;

class Edge {
    int u, v, w;
    public Edge(int u, int v, int w) {
        this.u = u;
        this.v = v;
        this.w = w;
    }
}

class Solution {
    static final int MAXN = 100005;
    static List<Edge> edge = new ArrayList<>();
    static int[] dis = new int[MAXN];
    static final int INF = 0x3f3f3f3f;

    public static boolean bellmanford(int n, int s) {
        Arrays.fill(dis, INF);
        dis[s] = 0;

        // 用于判断一轮循环过程中是否发生松弛操作
        boolean flag = false;
        for (int i = 1; i <= n; i++) {
            flag = false;
            for (Edge e : edge) {
                // 无穷大与常数加减仍然为无穷大
                // 因此最短路长度为 INF 的点引出的边不可能发生松弛操作
                if (dis[e.u] == INF) continue;
                if (dis[e.v] > dis[e.u] + e.w) {
                    dis[e.v] = dis[e.u] + e.w;
                    flag = true;
                }
            }
            // 没有可以松弛的边时就停止算法
            if (!flag) break;
        }
        // 第 n 轮循环仍然可以松弛时说明 s 点可以抵达一个负环
        return flag;
    }
}
```
<!-- tabs:end -->

## SPFA

SPFA 是 Bellman–Ford 算法的一种优化实现，它用队列维护松弛操作的顺序，从而避免了不必要的松弛操作，使得算法的时间复杂度降低到 O(km)，其中 k 是每个结点被放入队列的次数。

很显然，只有上一次被松弛的结点，所连接的边，才有可能引起下一次的松弛操作。

那么我们用队列来维护「哪些结点可能会引起松弛操作」，就能只访问必要的边了。

SPFA 也可以用于判断 s 点是否能抵达一个负环，只需记录最短路经过了多少条边，当经过了至少 n 条边时，说明 s 点可以抵达一个负环。

## 代码实现

<!-- tabs:start -->
#### **CPP**

```cpp
struct edge {
  int v, w;
};

vector<edge> e[maxn];
int dis[maxn], cnt[maxn], vis[maxn];
queue<int> q;

bool spfa(int n, int s) {
  memset(dis, 0x3f, (n + 1) * sizeof(int));
  dis[s] = 0, vis[s] = 1;
  q.push(s);
  while (!q.empty()) {
    int u = q.front();
    q.pop(), vis[u] = 0;
    for (auto ed : e[u]) {
      int v = ed.v, w = ed.w;
      if (dis[v] > dis[u] + w) {
        dis[v] = dis[u] + w;
        cnt[v] = cnt[u] + 1;  // 记录最短路经过的边数
        if (cnt[v] >= n) return false;
        // 在不经过负环的情况下，最短路至多经过 n - 1 条边
        // 因此如果经过了多于 n 条边，一定说明经过了负环
        if (!vis[v]) q.push(v), vis[v] = 1;
      }
    }
  }
  return true;
}
```
<!-- tabs:end -->