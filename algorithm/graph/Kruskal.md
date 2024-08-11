# Kruskal

Kruskal算法是一种基于贪心策略的最小生成树算法，它通过逐步**选择权重最小的边**，并确保该边不会形成环路来构建最小生成树。

**算法流程如下：**

1. 创建一个空的最小生成树 MST 和一个空的集合 visited，用于存放已经访问过的顶点。
2. 将图中的所有边按照权重从小到大进行排序。
3. 遍历排序后的边，对于每条边 (u, v)：
    - 如果加入边 (u, v) 后不会形成环路，则将该边加入 MST，并将顶点 u 和顶点 v 加入 visited 集合中。
    - 如果加入边 (u, v) 后形成环路，则忽略该边。
4. 重复步骤 3，直到 MST 中包含 n-1 条边，最终得到的 MST 就是最小生成树。

**java 模板如下：**

```java
static class Edge implements Comparable<Edge> {
 int src, dest, weight;
 public Edge(int src, int dest, int weight){
  this.src = src;
  this.dest = dest;
  this.weight = weight;
 }
 public int compareTo(Edge edge) {
  return this.weight - edge.weight;
 }
}
static int[][] kruskalMST(Edge[] edges, int V) {
 int[][] mst = new int[V][V]; // 最小生成树
 int E = edges.length; // 边数
 Arrays.sort(edges);
 
 // 使用并查集判断是否存在回路
 int[] parent = init(V);
 
 int edgeCount = 0; // 记录加入最小生成树的边数
 int index = 0; // 边数组的索引
 // 构建最小生成树
 while (edgeCount < V - 1 && index < E) {
  Edge nextEdge = edges[index++];
  int x = find(parent, nextEdge.src);
  int y = find(parent, nextEdge.dest);
  if (x != y) {
   mst[nextEdge.src][nextEdge.dest] = nextEdge.weight;
   mst[nextEdge.dest][nextEdge.src] = nextEdge.weight;
   edgeCount++;
   union(parent, x, y);
  }
 }
 return mst;
}

// 并查集
static int[] init(int n){
 int[] parent = new int[n]; // 用于判断是否形成环路
 for (int i = 0; i < n; i++) {
  parent[i] = i;
 }
 return parent;
}
static int find(int[] parent, int i) {
 if (parent[i] != i) {
  parent[i] = find(parent, parent[i]);
 }
 return parent[i];
}
static void union(int[] parent, int x, int y) {
 int xroot = find(parent, x);
 int yroot = find(parent, y);
 parent[yroot] = xroot;
}
```
