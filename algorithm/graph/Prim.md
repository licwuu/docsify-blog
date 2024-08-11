# Prim

Prim 算法是一种用于解决最小生成树（Minimum Spanning Tree）问题的贪心算法。它通过不断选择与**当前生成树连接的最小权重边**来逐步构建最小生成树。

具体流程如下：

1. 创建一个空的最小生成树 MST 和一个空的集合 visited，用于存放已经被访问过的顶点。
2. 选择一个起始顶点，将其加入 visited 集合中。
3. 从 visited 集合中选择一条边的权重最小的边，该边的另一端顶点不在 visited 集合中。
4. 将该边加入 MST，并将该边的另一端顶点加入 visited 集合中。
5. 重复步骤 3 和 4，直到 MST 中包含了所有顶点。
6. 最终得到的 MST 就是最小生成树。

java 代码模板如下：

```java
int[][] primMST(int[][] graph) {
 int V = graph.length; // 顶点数
 int[][] mst = new int[V][V]; // 最小生成树
 boolean[] visited = new boolean[V]; // 记录顶点是否被访问过
 int[] dist = new int[V]; // 记录顶点到最小生成树的距离

 // 初始化距离为无穷大，将起始顶点设为0
 for (int i = 0; i < V; i++) {
  dist[i] = Integer.MAX_VALUE;
 }
 dist[0] = 0;

 // 构建最小生成树
 for (int i = 0; i < V - 1; i++) {
  int u = minDistance(dist, visited);
  visited[u] = true;
  // 通过当前节点更新其他节点到树的距离和到树的边
  for (int v = 0; v < V; v++) {
   if (graph[u][v] != 0 && !visited[v] && graph[u][v] < dist[v]) {
    mst[u][v] = graph[u][v];
    mst[v][u] = graph[u][v];
    dist[v] = graph[u][v];
   }
  }
 }
 return mst;
}
// 获取距离树最近的点
int minDistance(int[] dist, boolean[] visited) {
 int min = Integer.MAX_VALUE;
 int minIndex = -1;
 for (int v = 0; v < dist.length; v++) {
  if (!visited[v] && dist[v] < min) {
   min = dist[v];
   minIndex = v;
  }
 }
 return minIndex;
}
```
