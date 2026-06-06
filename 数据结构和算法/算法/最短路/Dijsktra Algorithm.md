N 节点个数
S 源节点
W 权重矩阵 W\[i, j\] 表示从节点i到节点j的权。
**greedy algorithm**
## 矩阵表示

```cpp
// n is node num
int n;
// s is source node;
int s;
// weight of edges
vector<vector<int>> weight;
vector<int> dis(n);
// init dis to inf;
INF = 9999999;
for(int i=0; i<n; i++)
	dis[i] = INF;
dis[s] = 0;
// visit
vector<bool> visit(n);
for(int i=0; i<n; i++) visit[i] = false;
for(int i=0; i<n; i++) {
	int min_node = -1, temp_min = INF;
	for(int j=0;j<n; j++) {
		if(visit[j]==false && dis[j] < temp_min) {
			temp_min = dis[j];
			min_node = j;
		}
	}
	if(min_node = -1) break;
	visit[min_node] = true;
	for(int j=0; j<n; j++) {
		if(weight[u][j]) {
			if (dis[u] + w[u][j] < dis[j]) dis[j] = dis[u] + w[u][j];
		}
	}
}
```
## 邻接表表示

