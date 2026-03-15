Here is all the text from the Algorithm Reference page, formatted in Markdown:

---

# 📚 Black Box Algorithm Reference

> Every algorithm is a **black box**. You pass in inputs. You use outputs. You **CANNOT** modify internal logic.

---

## DFS — Depth-First Search
**Category:** Traversal | **Complexity:** O(n + m)

Explores as deep as possible before backtracking. Produces timestamps and component IDs.

### Inputs
- Simple graph in adjacency list format
- Starting vertex — optional. If not provided, DFS runs from all vertices automatically.

### Outputs
- `ccnum[]` — connected component number for each vertex (reliable **ONLY** on undirected graphs)
- `prev[]` — parent vertex in DFS tree (nil for starting vertex or unreachable vertices)
- `pre[]` — pre-visit timestamp (clock value when DFS **ENTERED** this vertex)
- `post[]` — post-visit timestamp (clock value when DFS **FINISHED** this vertex and ALL its descendants)
- 🔒 `visited[]` — internal tracking only. NOT returned. Always all true when DFS completes.

### ✅ Use When
- Find connected components in undirected graph (group by `ccnum[]`)
- Detect cycles — back edge found = cycle exists
- Compute pre/post timestamps as basis for Topological Sort
- Compute pre/post timestamps for the SCC algorithm
- Explore all nodes reachable from a source

### ❌ Do NOT Use When
- Need shortest path — use BFS (unweighted) or Dijkstra's (weighted)
- Need weighted distances — DFS has no concept of edge weights
- Need reliable component IDs on a directed graph — use SCC instead

### 💡 Key Properties and Tricks
- Back edge rule: edge u→v where `post[u] < post[v]` means v is still open (ancestor of u) → **CYCLE exists**
- Highest `post[]` value node = belongs to a **SOURCE SCC** in directed graphs
- `ccnum[]` is **UNRELIABLE** on directed graphs — direction creates asymmetry DFS can't capture
- For any edge u→v in a DAG: `post[u] > post[v]` ALWAYS (this is the topological sort property)
- Interval nesting: if `[pre[v], post[v]]` is completely inside `[pre[u], post[u]]`, then v is a descendant of u
- Total clock ticks = 2n (n pre-visit ticks + n post-visit ticks, clock counts from 1 to 2n)

### DFS Edge Types in Directed Graphs

| Edge Type | Condition | Meaning |
|---|---|---|
| **Tree Edge / Forward Edge** | `post[u] > post[v]` | u is an ancestor of v in the DFS tree — normal exploration or a forward shortcut |
| **Back Edge** | `post[u] < post[v]` (v is still open when u is found) | u→v where v is an **ANCESTOR** of u — this creates a **CYCLE**. Only back edges indicate cycles. |
| **Cross Edge** | `post[u] < post[v]` (v is already closed, different subtree) | v was in a completely different branch that already finished — no cycle |

---

## BFS — Breadth-First Search
**Category:** Traversal | **Complexity:** O(n + m)

Explores level by level from source. Gives minimum hop count (unweighted shortest path).

### Inputs
- Simple graph in adjacency list format
- Starting vertex

### Outputs
- `dist[]` — unweighted distance (number of edges) from start to each vertex. ∞ for unreachable vertices.
- `prev[]` — parent vertex for path reconstruction. nil for starting vertex and unreachable vertices.

### ✅ Use When
- Unweighted shortest path — fewest edges from s to t
- Reachability check — is t reachable from s?
- Level-order exploration of a graph
- Used internally in Edmonds-Karp to find augmenting paths

### ❌ Do NOT Use When
- Edges have different weights — use Dijkstra's Algorithm
- Need pre/post timestamps — use Depth-First Search
- Need distance from multiple sources — add virtual super-source first

### 💡 Key Properties and Tricks
- `dist[t]` = minimum number of edges from s to t (hop count, not weighted distance)
- Follow `prev[t] → prev[prev[t]] → ...` until nil to reconstruct the shortest path
- BFS with unit weights = Dijkstra's Algorithm with all weights equal to 1
- O(n+m) vs Dijkstra's O((n+m)logn) — always prefer BFS when the graph is unweighted

---

## Topo Sort — Topological Sort
**Category:** Ordering | **Complexity:** O(n + m)

Orders all vertices of a DAG from sources to sinks. Requires NO cycles.

### Inputs
- Simple **DIRECTED ACYCLIC graph** in adjacency list format — graph **MUST** have no cycles

### Outputs
- `order[]` — list of ALL vertices in topological order, from sources to sinks. Indexed **NUMERICALLY** (1, 2, 3...), not by vertex name.
- All Depth-First Search outputs are also available: `ccnum[]`, `prev[]`, `pre[]`, `post[]`

### ✅ Use When
- Schedule tasks that have prerequisite dependencies
- Process a DAG from sources to sinks in valid order
- Find a linear ordering where every edge u→v has u before v
- Use as a preliminary step for DAG-based dynamic programming

### ❌ Do NOT Use When
- Graph has any cycle — Topological Sort is **UNDEFINED** and will give wrong results
- Undirected graphs — topological ordering has no meaning

### 💡 Key Properties and Tricks
- `order[]` is indexed numerically 1..n, **NOT** by vertex name — iterate it as a regular array
- Reverse DFS post-order = topological order (this is how it works internally)
- Multiple valid topological orderings can exist when some vertices have no dependency between them
- `order[1]` is a source (no incoming edges); `order[n]` is a sink (no outgoing edges)
- If you try to topologically sort a graph with a cycle, the result will be incorrect — always verify DAG first

---

## SCC — Strongly Connected Components
**Category:** Decomposition | **Complexity:** O(n + m)

Finds maximal mutual-reachability groups. Output metagraph is ALWAYS a DAG.

### Inputs
- Simple directed graph in adjacency list format

### Outputs
- `G_SCC = (V_SCC, E_SCC)` — the metagraph in adjacency list format. This is **ALWAYS a DAG**.
- **CRITICAL:** Metagraph is sorted **SINK→SOURCE** (reverse topological order). `V_SCC[1]` = a **SINK SCC**.
- All Depth-First Search outputs from the **FINAL DFS run**: `ccnum[]`, `prev[]`, `pre[]`, `post[]`
- `ccnum[v]` = which SCC vertex v belongs to. All vertices with same ccnum are in the same SCC.

### ✅ Use When
- Find all strongly connected components in a directed graph
- Convert a cyclic directed graph into a DAG (the metagraph) for further processing
- Find source SCC: the group with no incoming edges in G_SCC
- Find sink SCC: the group with no outgoing edges in G_SCC — `V_SCC[1]` is always a sink SCC
- Map original vertices to their SCC group using `ccnum[]`

### ❌ Do NOT Use When
- Undirected graph — use Depth-First Search `ccnum[]` which is simpler and still O(n+m)

### 💡 Key Properties and Tricks
- `G_SCC` is **ALWAYS a DAG** regardless of cycles in the original graph
- `V_SCC[1]` = the FIRST vertex in the metagraph = a **SINK SCC** (because sorted reverse topo: sink→source)
- Source SCC = the SCC with **NO incoming edges** in E_SCC
- Sink SCC = the SCC with **NO outgoing edges** (empty adjacency list in G_SCC)
- `ccnum[v]` from the final DFS maps each original vertex to its SCC number
- Key insight for the algorithm: highest `post[]` in G^R (reversed graph) = sink SCC of original G

---

## Dijkstra's Algorithm
**Category:** Shortest Path | **Complexity:** O((n + m) log n)

Fastest single-source weighted shortest path. ALL weights must be non-negative.

### Inputs
- Simple graph in adjacency list format
- Starting vertex s
- List of **NON-NEGATIVE** edge weights — every weight must be ≥ 0

### Outputs
- `dist[]` — weighted shortest distance from s to each vertex. ∞ for unreachable vertices.
- `prev[]` — parent vertex for path reconstruction. nil for s and unreachable vertices.

### ✅ Use When
- Weighted shortest path with all non-negative edge weights
- Single-source: find shortest distance from s to all other vertices
- Fastest option when all weights are non-negative

### ❌ Do NOT Use When
- **ANY** edge weight is negative — Dijkstra's **WILL give WRONG answers**. Use Bellman-Ford.
- Need all-pairs distances — run Floyd-Warshall instead
- Need the `iter[][]` iteration table — use Bellman-Ford

### 💡 Key Properties and Tricks
- `prev[t]` traces the OPTIMAL shortest path backwards from t to s
- A vertex's `dist[]` value is **PERMANENTLY FINAL** once it is dequeued from the priority queue (greedy argument)
- This greedy finality **breaks** when negative weights exist — a later path through a negative edge could be shorter
- For widest path: use modified version with max-heap tracking min edge weight instead of sum

---

## Bellman-Ford
**Category:** Shortest Path | **Complexity:** O(nm)

Handles negative edge weights. Detects negative cycles. Slower than Dijkstra's.

### Inputs
- Simple graph in adjacency list format
- Starting vertex s
- List of edge weights — can include negative values

### Outputs
- `dist[]` — weighted shortest distance from s to each vertex after n-1 iterations. ∞ if unreachable.
- `prev[]` — parent vertex for path reconstruction. nil for s and unreachable vertices.
- `iter[][]` — `iter[i][v]` = distance from s to v after exactly i iterations (table from 0 to n)

### ✅ Use When
- Negative edge weights are present and correct shortest path is needed
- Detect a negative cycle reachable from source s
- Need the `iter[i][v]` table — distances at each intermediate iteration step
- DP on graphs with an 'at most k edges' constraint (use `iter[k][v]`)

### ❌ Do NOT Use When
- All weights positive and speed matters — Dijkstra's is O((n+m)logn) vs Bellman-Ford O(nm)
- Need all-pairs — Floyd-Warshall is better than running Bellman-Ford n times

### 💡 Key Properties and Tricks
- Run n iterations total. If `iter[n][v] < iter[n-1][v]` for any v → **NEGATIVE CYCLE** reachable from s
- `iter[i][v]` = D(i,v) = shortest path from s to v using **AT MOST i edges**
- Base case: `iter[0][s] = 0`, `iter[0][v] = ∞` for all v ≠ s
- Any simple path uses at most n-1 edges, so convergence in n-1 rounds is guaranteed if no negative cycle

---

## Floyd-Warshall
**Category:** All-Pairs Shortest Path | **Complexity:** O(n³)

Shortest path between every pair of vertices. Handles negative weights. Detects all negative cycles.

### Inputs
- Simple graph in adjacency list format
- List of edge weights — can include negative values

### Outputs
- `dist[][]` — `dist[u][v]` = shortest weighted path from u to v. ∞ if unreachable.
- `iter[][][]` — `iter[k][u][v]` = shortest path from u to v using only vertices {1..k} as intermediates

### ✅ Use When
- Need shortest distances between **ALL pairs** of vertices
- Negative weights present (but no negative cycles for meaningful shortest paths)
- Detect **ANY** negative cycle in the entire graph — check if `dist[v][v] < 0` after run
- Need all-pairs reachability (transitive closure)

### ❌ Do NOT Use When
- Only one source — use Dijkstra's (O((n+m)logn)) or Bellman-Ford (O(nm)) which are much faster
- Very large or sparse graphs — O(n³) becomes prohibitive

### 💡 Key Properties and Tricks
- **Negative cycle detection:** check every `dist[v][v]` after the run. If `dist[v][v] < 0`, v is on a negative cycle.
- `iter[0][u][v]` = direct edge weight, or ∞ if no direct edge
- At iteration k: decide whether vertex k is useful as an intermediate between u and v
- For dense graphs (m ≈ n²): Floyd-Warshall O(n³) ≈ Bellman-Ford × n O(n²m) — comparable

---

## Kruskal's Algorithm
**Category:** Spanning Tree | **Complexity:** O(m log n)

Builds Minimum Spanning Tree by adding cheapest non-cycle edges. Returns an edge list.

### Inputs
- Simple **CONNECTED UNDIRECTED** graph in adjacency list format
- List of edge weights

### Outputs
- `edges[]` — list of exactly n-1 edges forming the Minimum Spanning Tree

### ✅ Use When
- Find a Minimum Spanning Tree
- Find a **Maximum** Spanning Tree — negate all weights first, then run Kruskal's
- Solve widest path problem — negate weights, get max ST, BFS for s→t path
- Need MST output as a **list of edges** for further processing

### ❌ Do NOT Use When
- Directed graphs — Kruskal's requires undirected graphs
- Disconnected graphs (MST undefined — would give a spanning forest)
- Need MST as parent pointers — use Prim's Algorithm instead

### 💡 Key Properties and Tricks
- For **MAXIMUM Spanning Tree**: negate every weight w(e) → -w(e), run Kruskal's, get max spanning tree
- To use `edges[]` output for further algorithms: "Convert Kruskal's `edges[]` to adjacency list G_MST in O(n+m)"
- A skipped edge means both its endpoints are already connected in the tree — existing path is at least as wide
- Always outputs exactly n-1 edges for a connected graph
- Widest path = max spanning tree (negate → Kruskal's) → BFS in the resulting tree

---

## Prim's Algorithm
**Category:** Spanning Tree | **Complexity:** O(m log n)

Builds Minimum Spanning Tree by growing from a root. Returns parent pointers.

### Inputs
- Simple **CONNECTED UNDIRECTED** graph in adjacency list format
- List of edge weights

### Outputs
- `prev[]` — parent of each vertex in the MST. Starting vertex (chosen arbitrarily) has `prev = nil`.

### ✅ Use When
- Find a Minimum Spanning Tree
- Need MST as **parent pointers** for path tracing (same format as Dijkstra's `prev[]`)
- Same runtime as Kruskal's — choose based on which output format fits your next step

### ❌ Do NOT Use When
- Directed graphs
- Need MST as an edge list — use Kruskal's Algorithm instead

### 💡 Key Properties and Tricks
- Convert `prev[]` to adjacency list: "Build G_MST from Prim's `prev[]` output in O(n+m)"
- Starting vertex is chosen arbitrarily and has `prev = nil`
- `prev[]` output format is identical to Dijkstra's and BFS — trace paths the same way
- Choose Prim's over Kruskal's when you specifically want parent pointers rather than edge list

---

## Ford-Fulkerson
**Category:** Max Flow | **Complexity:** O(mC)

Maximizes total flow from s to t by finding and augmenting paths. Requires integer capacities.

### Inputs
- Simple **CONNECTED DIRECTED** graph in adjacency list format
- Positive **INTEGER** edge capacities
- Source vertex s
- Sink vertex t

### Outputs
- `flow[]` — amount of capacity used on each edge in the optimal max flow solution
- `C` — the total maximum flow value from s to t

### ✅ Use When
- Maximize total throughput / flow from s to t
- Edge capacities are positive integers
- Maximum flow value C is small enough that O(mC) is acceptable
- Bipartite matching problems (after graph construction)

### ❌ Do NOT Use When
- Non-integer or very large capacities — O(mC) can be exponential. Use Edmonds-Karp.
- Undirected graph — convert to directed first by replacing each edge with two directed edges
- When you want a single best path — Ford-Fulkerson computes total flow across **ALL paths simultaneously**

### 💡 Key Properties and Tricks
- **Flow conservation law:** at every non-source, non-sink vertex, flow in = flow out
- For undirected input: replace edge (u,v, capacity c) with two directed edges u→v and v→u each with capacity c
- **Max-flow min-cut theorem:** max flow value = minimum total capacity of any cut separating s from t
- O(mC) runtime can be exponential if C is large — always prefer Edmonds-Karp for safety

---

## Edmonds-Karp
**Category:** Max Flow | **Complexity:** O(nm²)

Max flow using BFS for augmenting paths. Polynomial time. Preferred for any capacities.

### Inputs
- Simple **CONNECTED DIRECTED** graph in adjacency list format
- Positive edge capacities — integer or real-valued
- Source vertex s
- Sink vertex t

### Outputs
- `flow[]` — amount of capacity used on each edge in the optimal max flow solution
- `C` — the total maximum flow value from s to t

### ✅ Use When
- Maximize total flow from s to t with any positive capacities
- Non-integer or large capacity values where Ford-Fulkerson may not terminate
- Need guaranteed polynomial runtime regardless of capacity values
- Default choice when max flow is needed

### ❌ Do NOT Use When
- Single path problems — both flow algorithms compute total network volume, not one route

### 💡 Key Properties and Tricks
- Edmonds-Karp = Ford-Fulkerson + BFS augmenting paths. The BFS guarantee makes runtime polynomial.
- O(nm²) is independent of capacity values — safe for any input
- Outputs are identical to Ford-Fulkerson — use either interchangeably in solutions
- When in doubt between Ford-Fulkerson and Edmonds-Karp: **choose Edmonds-Karp**

---

## 2-SAT
**Category:** Satisfiability | **Complexity:** O(n + m)

Solves 2-CNF Boolean satisfiability by reduction to SCC. Returns a satisfying assignment or NO.

### Inputs
- A Boolean formula in CNF with at most 2 literals per clause
- Backed by n variables (at most 2n literals) and m clauses

### Outputs
- `assignments[]` — true/false for each variable in a satisfying assignment. Returns `'NO'` if unsatisfiable.
- All SCC outputs are also available: `ccnum[]`, `G_SCC`, metagraph structure.

### ✅ Use When
- Determine if a 2-CNF formula is satisfiable
- Encode a graph or assignment problem as a 2-CNF formula to check feasibility
- Find a valid assignment when one exists

### ❌ Do NOT Use When
- Formula has clauses with 3 or more literals — use **only for 2-CNF** (k-SAT with k≥3 is NP-complete)
- Need **all** satisfying assignments — 2-SAT returns one valid assignment only

### 💡 Key Properties and Tricks
- **Reduction:** clause (α∨β) → implication edges ā→β and β̄→α in the implication graph
- **UNSAT** iff any variable xᵢ and its negation x̄ᵢ appear in the **same SCC**
- If satisfiable: take sink SCC S, set S=T and S̄=F, remove both, repeat — greedy assignment
- Key fact: S is a sink SCC ↔ its negation S̄ is a source SCC (when all xᵢ, x̄ᵢ in different SCCs)
- Runtime is O(n+m) because it is just SCC on the implication graph (2n vertices, 2m edges)

---

There you go! All 12 algorithms from the Algorithm Reference page have been extracted and formatted into clean Markdown. Each algorithm includes its complexity, category, inputs, outputs, when to use it, when not to use it, and key properties/tricks. The DFS section also includes a formatted table for the edge types.