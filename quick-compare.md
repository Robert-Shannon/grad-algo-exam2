# ⚖️ Quick Compare
## Algorithm Comparison Table
| Abbreviation | Full Algorithm Name | Type | Edge Weights | Graph Direction | Handles Negative? | Output Variables | Runtime |
|---|---|---|---|---|---|---|---|
| BFS | Breadth-First Search | Traversal | Unweighted | Both | N/A | dist[], prev[] | O(n+m) |
| DFS | Depth-First Search | Traversal | Ignored | Both | N/A | ccnum[], pre[], post[], prev[] | O(n+m) |
| Topo Sort | Topological Sort | Ordering | N/A | Directed DAG only | N/A | order[] | O(n+m) |
| SCC | Strongly Connected Components | Decomposition | N/A | Directed | N/A | G_SCC (DAG metagraph) | O(n+m) |
| Dijkstra's | Dijkstra's Algorithm | Shortest Path | Non-negative ≥ 0 | Both | ❌ FAILS | dist[], prev[] | O((n+m) log n) |
| Bellman-Ford | Bellman-Ford | Shortest Path | Any (incl. negative) | Both | ✅ Works | dist[], prev[], iter[][] | O(nm) |
| Floyd-Warshall | Floyd-Warshall | All-Pairs SP | Any (incl. negative) | Both | ✅ Works | dist[][], iter[][][] | O(n³) |
| Kruskal's | Kruskal's Algorithm | Spanning Tree | Required | Undirected | Negate for max ST | edges[] | O(m log n) |
| Prim's | Prim's Algorithm | Spanning Tree | Required | Undirected | Negate for max ST | prev[] | O(m log n) |
| Ford-Fulkerson | Ford-Fulkerson | Max Flow | Integer capacities | Directed | N/A | flow[], C | O(mC) |
| Edmonds-Karp | Edmonds-Karp | Max Flow | Positive capacities | Directed | N/A | flow[], C | O(nm²) |
| 2-SAT | 2-SAT | Satisfiability | N/A | Directed (implication graph) | N/A | assignments[] | O(n+m) |
---
## DFS Pre/Post Number Rules
| Rule | Meaning |
|---|---|
| post[u] > post[v] for every u→v in DAG | Foundation of topological sort |
| post[u] < post[v] for edge u→v | Back edge → CYCLE exists |
| [pre[v],post[v]] inside [pre[u],post[u]] | v is descendant of u in DFS tree |
| Highest post[] value in any DFS | That node is in a source SCC |
| Clock counts from 1 to 2n total | n pre + n post timestamps |
---
## Strongly Connected Components Output
| Property | Detail |
|---|---|
| G_SCC is ALWAYS a DAG | Apply topo sort or DP to it directly |
| V_SCC sorted SINK→SOURCE | V_SCC[1] = a sink SCC |
| ccnum[v] from final DFS | Which SCC vertex v belongs to |
| Source SCC = no incoming in G_SCC | Sink SCC = empty adj list in G_SCC |
---
## Spanning Tree Key Facts
| Fact | Detail |
|---|---|
| Spanning tree includes ALL n vertices | s and t always in the tree |
| Exactly n-1 edges, no cycles | Tree is always connected |
| Negate weights → max spanning tree | Run Kruskal's on -w(e) |
| s→t path in max ST = widest path | Theorem: provably optimal bottleneck |