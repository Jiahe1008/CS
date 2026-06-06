伪代码
```
BellmanFord(graph, s):
    dist[] = ∞
    dist[s] = 0

    for i = 1 to V-1:
        for each edge (u, v, w):
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w

    for each edge (u, v, w):
        if dist[u] +w < dist[v]:
            error "存在负权环"
```
