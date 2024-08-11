# Floyd

## Floyd 简介

Floyd-Warshall 算法的核心思想是利用动态规划的思想，通过逐步更新距离矩阵来求解所有节点对之间的最短路径。它适用于解决图中节点对数量较小且权重可以为负的情况。
时间复杂度为 O (n^3)。

## java 代码模板

```java
static final int INF = Integer.MAX_VALUE;
// graph中无边的权重为INF
static void floydWarshall(int[][] graph, int V) {
 int[][] dist = new int[V][V];
 // 初始化距离矩阵
 for (int i = 0; i < V; i++) {
  for (int j = 0; j < V; j++) {
   dist[i][j] = graph[i][j];
  }
 }
 // 计算最短路径
 for (int k = 0; k < V; k++) {
  for (int i = 0; i < V; i++) {
   for (int j = 0; j < V; j++) {
    // 如果节点k是最短路径上的中间节点，更新最短路径
    if (dist[i][k] != INF && dist[k][j] != INF 
     && dist[i][k] + dist[k][j] < dist[i][j]) {
     
     dist[i][j] = dist[i][k] + dist[k][j];
    }
   }
  }
 }
}

```
