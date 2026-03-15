Here is all the Pattern Recognition tab content, fully extracted and restructured in clean plain text — ready to be copied by another agent:

---

# CS6515 Graph Algorithms — Pattern Recognition Guide

---

## HOW TO USE THIS TAB
- **Step 1** — Extract what the problem WANTS. A path? A grouping? Max flow? An ordering?
- **Step 2** — Identify the graph type. Directed or undirected? Weighted? Negative weights possible?
- **Step 3** — Find your pattern below.

---

## INSTANT MATCH — What does the problem want?

| Problem Wants | Use |
|---|---|
| Minimum hops (no weights) | Breadth-First Search |
| Cheapest path (positive weights) | Dijkstra's Algorithm |
| Cheapest path (any weights) | Bellman-Ford |
| All-pairs shortest paths | Floyd-Warshall |
| Maximum total throughput s→t | Ford-Fulkerson or Edmonds-Karp |
| Connect all nodes cheaply | Kruskal's or Prim's Algorithm |
| Maximize bottleneck on one path | Max Spanning Tree + BFS |
| Order by dependencies | Topological Sort |
| Mutual reachability groups | Strongly Connected Components |
| Does a cycle exist? | Depth-First Search (back edge check) |
| Connected components / groups | Depth-First Search (undirected) |
| Detect any negative cycle | Bellman-Ford or Floyd-Warshall |

---

## SECTION 1: Shortest / Cheapest Path Problems
*These problems ask for the minimum cost to travel from one place to another. Key distinctions: are edges weighted? Can weights be negative? Do you need one source or all pairs?*

---

### Pattern 1.1 — Minimum hops / edges between two nodes (no weights)
**Use: Breadth-First Search**

**WHY THIS ALGORITHM?**
BFS explores the graph layer by layer from the source. The first time it reaches a destination node, that is guaranteed to be via the fewest edges possible — no later path could be shorter, because BFS processes all distance-k nodes before any distance-(k+1) node.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run BFS starting from vertex s, passing in the graph. The output dist[t] gives the minimum number of edges from s to t. Follow prev[t] → ... → s to reconstruct the shortest hop path.

**REAL-WORLD EXAMPLE**
"What is the fewest number of streets you must cross to get from building A to building B?" or "What is the minimum number of handshakes separating two people in a social network?"

**USE WHEN**
- Graph is unweighted (or all edge weights are equal)
- You want the fewest edges, not minimum total cost

**DO NOT USE FOR**
- Edges have different weights — use Dijkstra's Algorithm instead
- You need pre/post timestamps — use Depth-First Search

---

### Pattern 1.2 — Shortest weighted path — all edge weights positive or zero
**Use: Dijkstra's Algorithm**

**WHY THIS ALGORITHM?**
Dijkstra's Algorithm uses a priority queue to always process the closest unvisited node first. Because all weights are non-negative, once a node is popped from the queue its distance is FINAL — no future path through a later node could possibly be shorter. This greedy correctness breaks down the moment any weight is negative.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Dijkstra's Algorithm from source s, passing in the graph and non-negative edge weight list. The output dist[t] = shortest weighted distance to t. Follow prev[t] → ... → s to reconstruct the shortest path.

**REAL-WORLD EXAMPLE**
"Find the fastest drive from Klaus to Skiles where each road has a travel time in minutes (all positive)." or "Minimum cost package delivery route."

**USE WHEN**
- All edge weights are ≥ 0
- Single source — want distance from s to one or all destinations

**DO NOT USE FOR**
- Any negative edge weight — Dijkstra's will give WRONG answers. Use Bellman-Ford.
- All-pairs distances — use Floyd-Warshall

---

### Pattern 1.3 — Shortest weighted path — some edge weights may be negative
**Use: Bellman-Ford**

**WHY THIS ALGORITHM?**
Bellman-Ford converges after exactly n-1 iterations because any simple path uses at most n-1 edges. It handles negative weights by relaxing all edges repeatedly, ensuring that even paths that go "backward" in cost are correctly computed.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Bellman-Ford from source s. After n-1 iterations, dist[t] = shortest weighted distance. If a further (n-th) iteration still reduces any distance, a negative cycle is reachable.

**USE WHEN**
- Graph has negative edge weights
- Need to detect negative cycles reachable from s

**DO NOT USE FOR**
- All non-negative weights — Dijkstra's is faster (O((n+m) log n) vs O(nm))
- All-pairs shortest paths — use Floyd-Warshall

---

### Pattern 1.4 — Shortest path between EVERY pair of nodes (all-pairs)
**Use: Floyd-Warshall**

**WHY THIS ALGORITHM?**
Floyd-Warshall builds a dist[i][j] table by iterating over every possible intermediate vertex k. After processing all k, dist[i][j] = shortest path from i to j using any intermediate vertices. Also detects negative cycles by checking if dist[v][v] < 0.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Floyd-Warshall on the complete graph. Output is dist[i][j] for all pairs. Check dist[v][v] < 0 for any v to detect negative cycles anywhere in the graph.

**USE WHEN**
- Need distances between every pair of nodes
- Want to detect any negative cycle in the whole graph

**DO NOT USE FOR**
- Single-source queries — Dijkstra's or Bellman-Ford is faster

---

## SECTION 2: Connectivity & Reachability Problems
*These problems ask whether nodes are connected, how many separate groups exist, or whether any path exists at all. Undirected vs directed changes which algorithm to use.*

---

### Pattern 2.1 — Can node A reach node B? Is any path possible?
**Use: Breadth-First Search**

**WHY THIS ALGORITHM?**
BFS explores all nodes reachable from the starting point. If the destination t is reached, a path exists. BFS also gives you the actual shortest path via prev[], which is a bonus if needed.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run BFS from s. If dist[t] ≠ ∞, then t is reachable from s. Follow prev[t] back to s to get the path. If you only need yes/no reachability and no path, Depth-First Search works equally well.

**REAL-WORLD EXAMPLE**
"Is warehouse A connected to store B by any road?" or "Can user A send a message to user B through this network?"

**USE WHEN**
- Undirected or directed graph
- Only need to know if a path exists (no weights needed)

**DO NOT USE FOR**
- When you need the shortest weighted path — use Dijkstra's Algorithm

---

### Pattern 2.2 — Find all connected components — how many groups of connected nodes?
**Use: Depth-First Search (on undirected graph)**

**WHY THIS ALGORITHM?**
The outer loop of DFS iterates over all vertices and increments a counter each time it starts a new Explore. Every vertex gets assigned a ccnum equal to the current counter value. After DFS completes, any two vertices with the same ccnum are in the same connected component.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run DFS on the undirected graph WITHOUT specifying a starting vertex (so it runs from all vertices). After DFS, group vertices by their ccnum[] value. The number of distinct ccnum values = number of components.

**REAL-WORLD EXAMPLE**
"How many separate island clusters exist in this archipelago network?" or "Which servers are isolated from the main cluster?"

**USE WHEN**
- Undirected graph
- Need to identify groups and count components

**DO NOT USE FOR**
- Directed graphs — ccnum[] is UNRELIABLE on directed graphs because direction matters. Use Strongly Connected Components instead.

---

### Pattern 2.3 — Find groups where EVERY node can reach every other node (directed graph)
**Use: Strongly Connected Components**

**WHY THIS ALGORITHM?**
In a directed graph, being in the "same connected component" requires mutual reachability — you can go from A to B AND from B to A. Regular DFS ccnum only captures one-directional reachability. The SCC algorithm (Kosaraju's) uses two DFS passes on the original and reversed graph to correctly find all maximal mutually-reachable groups.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run SCC on the directed graph. The output G_SCC is the metagraph where each node in V_SCC represents one SCC. Use ccnum[v] from the final DFS to find which SCC any original vertex v belongs to. The metagraph G_SCC is always a DAG.

**REAL-WORLD EXAMPLE**
"Find all districts in a one-way road city where you can drive between any two locations." or "Find all Twitter user groups where everyone follows everyone else (possibly through intermediaries)."

**USE WHEN**
- Directed graph
- Need mutual reachability groups (can go from A to B AND B to A)

**DO NOT USE FOR**
- Undirected graphs — just use DFS with ccnum[]

---

## SECTION 3: Cycle Detection & Ordering Problems
*These problems involve detecting whether cycles exist, ordering vertices by dependency relationships, or determining whether a valid linear order even exists. Key insight: cycles make topological ordering impossible.*

---

### Pattern 3.1 — Does this directed graph have a cycle? Is it a valid DAG?
**Use: Depth-First Search**

**WHY THIS ALGORITHM?**
A directed graph has a cycle if and only if DFS reveals a BACK EDGE — an edge (u, v) where v is an ancestor of u in the current DFS recursion stack. The pre/post timestamps identify this: a back edge u→v satisfies post[u] < post[v] (v is still "open" when u is discovered). No back edges = no cycles = valid DAG.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run DFS on the directed graph. Inspect the post[] timestamps. For every edge (u, v) in the original graph, check: if post[u] < post[v], that is a back edge — a cycle exists and the graph is NOT a DAG. If all edges satisfy post[u] > post[v], the graph IS a DAG.

**REAL-WORLD EXAMPLE**
"Can a student complete all these courses, or do some courses form an impossible prerequisite loop?" or "Does this build dependency system have a circular dependency?"

**USE WHEN**
- Directed graph
- Need to verify if topological sort is even possible before attempting it

**DO NOT USE FOR**
- Undirected graphs — back edge identification differs

---

### Pattern 3.2 — Order tasks so that all dependencies come first (scheduling / prerequisites)
**Use: Topological Sort**

**WHY THIS ALGORITHM?**
Topological Sort produces a linear ordering of vertices such that for every directed edge u→v, vertex u appears before v. Internally, it sorts by decreasing DFS post-order — nodes that finish exploring last (after all their descendants) must be sources and come first in the order.

**HOW TO APPLY IT IN YOUR SOLUTION**
First verify the graph is a DAG (no cycles). Run Topological Sort on the directed acyclic graph. The output order[] lists all vertices from sources to sinks. Process tasks in the order given by order[] — all prerequisites of any task have already appeared earlier in the list.

**REAL-WORLD EXAMPLE**
"In what sequence should we compile these modules so no module depends on something not yet compiled?" or "What order should a student take these courses to always satisfy prerequisites?"

**USE WHEN**
- Graph MUST be a DAG — topological sort is undefined on cyclic graphs
- Edges u→v represent "u must come before v"

**DO NOT USE FOR**
- Cyclic graphs — topological sort is IMPOSSIBLE if any cycle exists
- Undirected graphs

---

### Pattern 3.3 — Find the longest/shortest path through a DAG
**Use: Topological Sort combined with Dynamic Programming**

**WHY THIS ALGORITHM?**
Once vertices are in topological order, every predecessor of a vertex has already been processed. This means we can safely relax edges in topological order — when we process vertex u, all paths leading into u are already finalized. This works for any edge weights, including negative, because DAGs have no cycles.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Topological Sort to get order[]. Initialize dist[s] = 0, all others = ∞ (or -∞ for longest path). Iterate through order[]: for each vertex u in topological order, for each neighbor v of u, update dist[v] if dist[u] + w(u,v) is better. Follow prev[] from the target to reconstruct the path.

**REAL-WORLD EXAMPLE**
"What is the minimum-cost sequence of tasks to complete a project?" or "What is the maximum reward achievable by completing tasks in dependency order?"

**USE WHEN**
- Directed Acyclic Graph
- Edge weights can be any value — even negative (no cycles means no negative cycle issue)

**DO NOT USE FOR**
- Graphs with cycles — use Bellman-Ford for shortest path with negative weights in cyclic graphs

---

## SECTION 4: Maximum Flow Problems
*Flow problems ask: given a network with capacity limits on edges, what is the maximum TOTAL VOLUME that can be sent from source s to sink t simultaneously? This is NOT about a single best path — it is about routing flow across potentially many paths at once.*

---

### Pattern 4.1 — Maximize total flow from s to t — integer edge capacities
**Use: Ford-Fulkerson**

**WHY THIS ALGORITHM?**
Ford-Fulkerson finds augmenting paths (routes from s to t with remaining capacity) and pushes as much flow as possible through each. It repeats until no augmenting path exists. By the max-flow min-cut theorem, when no augmenting path exists the flow is maximum. Runtime is O(mC) where C is the max flow value — this can be bad for large C.

**HOW TO APPLY IT IN YOUR SOLUTION**
Build a directed flow graph with positive integer edge capacities. Run Ford-Fulkerson with source s and sink t. Output C = total maximum flow value. Output flow[] tells you exactly how much flow goes through each edge in the optimal solution.

**REAL-WORLD EXAMPLE**
"What is the maximum number of cars per hour that can travel from the highway entrance to the exit?" or "What is the maximum data throughput from server s to client t?"

**USE WHEN**
- Directed graph
- Edge capacities are positive INTEGERS
- Maximum flow value C is not astronomically large (runtime is O(mC))

**DO NOT USE FOR**
- Non-integer capacities — Ford-Fulkerson may not terminate. Use Edmonds-Karp.
- When you want a SINGLE path, not total flow — use Dijkstra's or spanning tree methods
- Undirected graphs — must convert to directed first

---

### Pattern 4.2 — Maximize total flow from s to t — any capacities or large values
**Use: Edmonds-Karp**

**WHY THIS ALGORITHM?**
Edmonds-Karp is Ford-Fulkerson but it always uses BFS to find the SHORTEST augmenting path (fewest edges). This guarantees polynomial runtime O(n·m²) regardless of capacity values — no more risk of non-termination with real-valued or large-integer capacities.

**HOW TO APPLY IT IN YOUR SOLUTION**
Build a directed flow graph with positive edge capacities. Run Edmonds-Karp with source s and sink t. Outputs are identical to Ford-Fulkerson: flow[] per edge and C = total maximum flow value. Default to Edmonds-Karp when in doubt.

**REAL-WORLD EXAMPLE**
"Maximum bandwidth from data center s to user t where link speeds are fractional." or "Maximum items that can be shipped through this logistics network per day."

**USE WHEN**
- Directed graph
- Any positive edge capacities — integer or real-valued
- Need guaranteed polynomial runtime

**DO NOT USE FOR**
- When you need a SINGLE best path — flow algorithms compute total network volume across all paths

---

### Pattern 4.3 — Maximum bipartite matching — pair up two groups optimally
**Use: Edmonds-Karp (after building a flow network from the bipartite graph)**

**WHY THIS ALGORITHM?**
Bipartite matching reduces perfectly to max flow. The maximum number of valid pairings equals the maximum flow through a carefully constructed network. Each matching edge becomes a flow edge of capacity 1, a virtual super-source feeds all left-side nodes, and a virtual super-sink collects from all right-side nodes.

**HOW TO APPLY IT IN YOUR SOLUTION**
Build directed graph: add super-source s* → each left-side node with capacity 1. Add each right-side node → super-sink t* with capacity 1. Add all bipartite edges left→right with capacity 1. Run Edmonds-Karp from s* to t*. The output flow[] = 1 on matched edges. Total flow C = number of matched pairs.

**REAL-WORLD EXAMPLE**
"Assign students to dorm rooms such that maximum students get their preferred room." or "Match job applicants to job openings to maximize total assignments."

**USE WHEN**
- Two distinct groups of nodes with edges only between groups (no edges within same group)
- Want to maximize the number of pairings

**DO NOT USE FOR**
- Non-bipartite matching — that is a harder problem not covered in this course

---

## SECTION 5: Spanning Tree Problems
*Spanning tree problems ask for the cheapest or heaviest set of edges that connects ALL n vertices using exactly n-1 edges with no cycles. A spanning tree ALWAYS includes every single vertex.*

---

### Pattern 5.1 — Cheapest / minimum cost way to connect all nodes
**Use: Kruskal's Algorithm or Prim's Algorithm**

**WHY THIS ALGORITHM?**
Both algorithms build a Minimum Spanning Tree. Kruskal's sorts all edges by weight and greedily adds cheapest edges that don't form cycles. Prim's grows a tree from one vertex by always picking the cheapest edge connecting the tree to a new vertex. Both are correct with the same O(m log n) runtime — choose based on which output format you need.

**HOW TO APPLY IT IN YOUR SOLUTION**
For Kruskal's: pass in the undirected graph and edge weights → get edges[] list of n-1 MST edges. For Prim's: pass in the undirected graph and edge weights → get prev[] parent pointers. Convert either to an adjacency list if needed: "Build G_MST from edges[] in O(n+m)" or "Build G_MST from Prim's prev[] in O(n+m)".

**REAL-WORLD EXAMPLE**
"What is the cheapest set of cables to install so that every building is connected to the network?" or "Minimum cost to link all islands with bridges."

**USE WHEN**
- Undirected connected graph
- Edge weights present
- Want to connect ALL vertices at minimum total cost

**DO NOT USE FOR**
- Directed graphs
- When you only need one s→t path — spanning tree connects everything, which is more than needed

---

### Pattern 5.2 — Maximize the minimum edge weight on a single path (widest path / bottleneck path)
**Use: Kruskal's Algorithm with negated weights, then BFS**

**WHY THIS ALGORITHM?**
A key theorem: the unique s→t path in the Maximum Spanning Tree is the bottleneck-optimal path in the original graph. To get the Max Spanning Tree using Kruskal's (which builds a minimum), negate all weights. Then BFS finds the unique s→t path in the resulting tree.

**HOW TO APPLY IT IN YOUR SOLUTION**
Step 1: Create negated weight list: for each edge e, set w'(e) = -w(e). Step 2: Run Kruskal's Algorithm on the graph with negated weights. Step 3: Convert edges[] to adjacency list G_MST in O(n+m). Step 4: Run BFS from s on G_MST. Step 5: Follow prev[] from t back to s — that is the widest path.

**REAL-WORLD EXAMPLE**
The Georgia Tech Marathon problem: find the route from Klaus to Skiles where the NARROWEST street is as WIDE as possible, so the most runners can fit.

**USE WHEN**
- Undirected weighted graph
- Single source s and specific destination t
- Want to maximize the bottleneck (minimum width) edge on the chosen path

**DO NOT USE FOR**
- Maximum TOTAL flow across all paths — use Ford-Fulkerson or Edmonds-Karp
- Directed graphs — Kruskal's requires undirected graphs

---

### Pattern 5.3 — Need the spanning tree as an edge list (for further graph construction)
**Use: Kruskal's Algorithm**

**WHY THIS ALGORITHM?**
Kruskal's Algorithm outputs edges[] — a direct list of the n-1 edges in the spanning tree. This is the most natural output if you need to iterate over the tree's edges, compute properties on them, or build a new graph.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Kruskal's Algorithm. The edges[] output is ready to use. To build a new adjacency list graph from it: "Create adjacency list G_MST from Kruskal's edges[] output — this takes O(n+m)."

**USE WHEN**
- Need the MST as a list of edges
- Plan to iterate over or build from tree edges

**DO NOT USE FOR**
- When you need to trace paths via parent pointers — use Prim's Algorithm instead

---

### Pattern 5.4 — Need the spanning tree as parent pointers (for path tracing from root)
**Use: Prim's Algorithm**

**WHY THIS ALGORITHM?**
Prim's Algorithm outputs prev[] — the parent of each vertex in the spanning tree, identical in format to the output of Dijkstra's Algorithm or BFS. Following prev[v] back to the root traces any tree path, making it convenient for path reconstruction.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Prim's Algorithm. Use prev[v] to find each vertex's parent in the MST. To trace the path between two vertices in the MST, follow prev[] backwards from the target.

**USE WHEN**
- Need to trace paths within the spanning tree
- Prefer parent-pointer format (same as Dijkstra's output)

**DO NOT USE FOR**
- When you need an edge list — use Kruskal's Algorithm

---

## SECTION 6: Strongly Connected Component Problems
*SCC problems deal with directed graphs. The SCC algorithm decomposes the graph into maximal groups of mutual reachability and returns a DAG metagraph. KEY: the metagraph is ALWAYS a DAG, sorted in REVERSE topological order (sink SCC comes first).*

---

### Pattern 6.1 — Find all maximal groups of mutual reachability in a directed graph
**Use: Strongly Connected Components**

**WHY THIS ALGORITHM?**
The SCC algorithm (Kosaraju's) runs DFS twice — once on the reversed graph to establish a DFS finish order, and once on the original graph in reverse finish order to extract each SCC. The result G_SCC metagraph collapses every SCC into a single supernode and is guaranteed to be a DAG.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run SCC on the directed graph. The output G_SCC = (V_SCC, E_SCC) gives the DAG of SCCs. Each vertex in V_SCC represents one SCC. Use ccnum[v] from the final DFS output to know which SCC any original vertex v belongs to.

**REAL-WORLD EXAMPLE**
"Find all groups of cities where you can travel between any two cities using one-way roads." or "Identify all user clusters where everyone can message everyone else in a directed social network."

**USE WHEN**
- Directed graph with potential cycles
- Need to identify mutual reachability groups

**DO NOT USE FOR**
- Undirected graphs — just use DFS ccnum[] which is O(n+m) and simpler

---

### Pattern 6.2 — Simplify a directed cyclic graph into a DAG for further algorithm application
**Use: Strongly Connected Components**

**WHY THIS ALGORITHM?**
The SCC metagraph G_SCC is always a DAG, even if the original graph was full of cycles. Once you have G_SCC, you can safely apply any DAG algorithm: Topological Sort, Dijkstra's, Bellman-Ford, or dynamic programming.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run SCC. Work with G_SCC = (V_SCC, E_SCC) as your new DAG. Apply whatever algorithm you need to G_SCC. Use ccnum[v] to map answers back to original vertices.

**REAL-WORLD EXAMPLE**
"This road network has some two-way districts and some one-way connections. Determine the dependency order of districts." — first SCC to collapse districts, then Topological Sort on G_SCC.

**USE WHEN**
- Directed graph with cycles that prevent direct DAG algorithm application
- Need to apply Topological Sort or DAG-based DP to a cyclic graph

**DO NOT USE FOR**
- Graph is already a DAG — apply Topological Sort directly without needing SCC

---

### Pattern 6.3 — Find the source SCC — the group that can reach all other SCCs
**Use: Strongly Connected Components**

**WHY THIS ALGORITHM?**
In the metagraph G_SCC, the source SCC has no incoming edges from other SCCs. Since G_SCC is sorted in REVERSE topological order (sink first, source last), the source SCC appears LAST in V_SCC — or you can scan G_SCC for the node with no incoming edges.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run SCC. Iterate over V_SCC and E_SCC. The source SCC is the node in V_SCC with no incoming edges in E_SCC (no other SCC has an edge pointing to it). All original vertices with that ccnum value belong to the source SCC.

**REAL-WORLD EXAMPLE**
"Which group of servers can send commands to every other server, making it the master control group?"

**USE WHEN**
- Directed graph
- Need the "root" component that everything else depends on or is reachable from

---

### Pattern 6.4 — Find the sink SCC — the group that no edge leaves
**Use: Strongly Connected Components**

**WHY THIS ALGORITHM?**
In the metagraph G_SCC, the sink SCC has no outgoing edges to other SCCs. Since G_SCC is sorted SINK-FIRST (reverse topological order), the FIRST vertex in V_SCC (index 1) is always a sink SCC.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run SCC. The first vertex in V_SCC is a sink SCC. Alternatively, iterate over G_SCC and find any vertex in V_SCC whose adjacency list in E_SCC is empty (no outgoing edges to other SCCs).

**REAL-WORLD EXAMPLE**
"Which group of web pages, once you navigate into them, you can never leave?" or "Which financial institution receives money but never transfers out?"

**USE WHEN**
- Directed graph
- Need the terminal component(s) with no outgoing inter-SCC edges

---

## SECTION 7: Negative Weights and Negative Cycle Detection
*Negative weights require special handling. Dijkstra's FAILS with negative weights. Negative CYCLES (total weight < 0) make shortest paths undefined — you can keep looping to decrease cost infinitely.*

---

### Pattern 7.1 — Detect if a negative cycle exists reachable from a specific source s
**Use: Bellman-Ford**

**WHY THIS ALGORITHM?**
Bellman-Ford converges after exactly n-1 iterations because any simple path uses at most n-1 edges. If running the n-th iteration STILL decreases any distance, a path of length n exists — which must revisit a vertex — which means it contains a cycle — and since the distance decreased, that cycle has negative total weight.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Bellman-Ford from source s. After completion, compare iter[n-1][v] vs iter[n][v] for all vertices v. If iter[n][v] < iter[n-1][v] for any vertex v, a negative cycle reachable from s exists.

**REAL-WORLD EXAMPLE**
"Does this currency exchange network allow infinite profit by cycling through trades?" (check from a specific starting currency)

**USE WHEN**
- Directed or undirected graph
- Need to check only from source s, not the entire graph

**DO NOT USE FOR**
- Need to find negative cycles anywhere in the graph — use Floyd-Warshall instead

---

### Pattern 7.2 — Detect if ANY negative cycle exists anywhere in the entire graph
**Use: Floyd-Warshall**

**WHY THIS ALGORITHM?**
Floyd-Warshall computes all-pairs shortest paths. After completion, the diagonal dist[v][v] represents the shortest path from v back to itself. If this is negative, v lies on a negative cycle. This checks the entire graph at once, not just from one source.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Floyd-Warshall on the complete graph with all edge weights. After completion, check every entry dist[v][v] for all vertices v. If any dist[v][v] < 0, that vertex is on a negative cycle.

**REAL-WORLD EXAMPLE**
"Does this financial market contain ANY arbitrage opportunity anywhere, starting from any bank?"

**USE WHEN**
- Need to check entire graph for negative cycles
- Already need all-pairs distances anyway (no extra cost to check diagonal)

**DO NOT USE FOR**
- Only checking from one specific source — Bellman-Ford is faster at O(nm) vs O(n³)

---

## SECTION 8: Multi-Algorithm Combination Strategies
*Many exam problems require chaining two or more algorithms. You pre-process inputs, run a black box, transform outputs, run another black box. You CANNOT modify any algorithm's internals — only change what you pass in and how you use what comes out.*

---

### Pattern 8.1 — Widest single path from s to t — maximize the minimum edge weight along the route
**Use: Kruskal's Algorithm (negated weights) → BFS**

**WHY THIS ALGORITHM?**
The Maximum Spanning Tree has a provable theorem: the unique s→t path in the Max Spanning Tree is the bottleneck-optimal path in the original graph. Kruskal's gives the Min Spanning Tree, so negate weights to get the Max. Then BFS finds the unique s→t path in this tree.

**HOW TO APPLY IT IN YOUR SOLUTION**
1) Negate all weights: w'(e) = -w(e). 2) Run Kruskal's Algorithm with negated weights to get edges[]. 3) Convert edges[] to adjacency list G_MST in O(n+m). 4) Run BFS from s on G_MST. 5) Follow prev[] from t back to s — that is the widest path. The bottleneck is the minimum weight edge on this path.

**REAL-WORLD EXAMPLE**
Georgia Tech Marathon: route from Klaus to Skiles maximizing the minimum street width so runners can pass.

**USE WHEN**
- Undirected weighted graph
- Single source s and destination t

**DO NOT USE FOR**
- Max total flow across all paths — that requires Ford-Fulkerson or Edmonds-Karp

---

### Pattern 8.2 — Shortest path in a DAG — can handle any edge weights including negative
**Use: Topological Sort → edge relaxation in topological order**

**WHY THIS ALGORITHM?**
In a DAG, processing vertices in topological order guarantees that when you process vertex u, ALL paths into u are already finalized. You can relax edges in this order and get correct shortest paths without a priority queue — and negative edge weights are fine because there are no cycles.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run Topological Sort to get order[]. Initialize dist[s]=0, dist[v]=∞ for all other v. For each vertex u in order[] from left to right, for each neighbor v of u, if dist[u] + w(u,v) < dist[v] then update dist[v] and prev[v]. Follow prev[] from t to s for the path.

**REAL-WORLD EXAMPLE**
"Minimum cost to schedule manufacturing tasks in dependency order where some task transitions have cost savings (negative weights)."

**USE WHEN**
- Directed Acyclic Graph — absolutely no cycles
- Edge weights can be any value (even negative — no negative cycle risk in a DAG)

**DO NOT USE FOR**
- Any graph with cycles — use Bellman-Ford for negative weights, Dijkstra's for positive

---

### Pattern 8.3 — Shortest distance from ANY one of multiple source nodes to all other nodes
**Use: Dijkstra's Algorithm or BFS with a virtual super-source node**

**WHY THIS ALGORITHM?**
Instead of running BFS/Dijkstra n times from each source (O(n·(n+m)logn)), add one virtual super-source connected to every real source with weight 0. A single run from the super-source gives the shortest distance from any real source to every vertex — the virtual node's zero-weight edges don't affect distances.

**HOW TO APPLY IT IN YOUR SOLUTION**
Add virtual vertex s* to the graph. For each real source vertex v, add edge s*→v with weight 0. Run Dijkstra's Algorithm from s* (or BFS if unweighted). dist[v] = shortest distance from any real source to vertex v.

**REAL-WORLD EXAMPLE**
"Find the nearest hospital to every neighborhood on the map, given that there are 5 hospitals."

**USE WHEN**
- Multiple starting points that are all equally valid
- Want minimum distance to each node across all possible starts

**DO NOT USE FOR**
- When sources have different initial costs — adjust edge weights from s* accordingly

---

### Pattern 8.4 — Apply a DAG algorithm to a graph that has cycles
**Use: Strongly Connected Components → DAG algorithm on the metagraph**

**WHY THIS ALGORITHM?**
The SCC metagraph G_SCC is always a DAG. By collapsing each SCC into a supernode, we eliminate all cycles. Any algorithm that requires a DAG (Topological Sort, DAG-based DP) can then run on G_SCC instead.

**HOW TO APPLY IT IN YOUR SOLUTION**
Run SCC to get G_SCC = (V_SCC, E_SCC). Apply your DAG algorithm to G_SCC. Use ccnum[v] from the SCC output to map any original vertex to its SCC supernode and interpret results in terms of the original graph.

**REAL-WORLD EXAMPLE**
"Find the topological order of city districts in a road network that has some two-way districts (cycles)."

**USE WHEN**
- Directed graph with cycles
- Need to apply Topological Sort or DAG DP

**DO NOT USE FOR**
- Graph is already a DAG — Topological Sort directly, no SCC step needed

---

That's the full Pattern Recognition tab — all 8 sections, all 27 patterns, with every "Why", "How to Apply", "Real-World Example", "Use When", and "Do Not Use For" block included and cleanly formatted for copy/paste by another agent.