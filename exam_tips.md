Here is the complete Exam Tips page copied into markdown:

---

# 💡 Exam Tips — CS6515 Graph Algorithms

---

## 🧠 3 Questions to Ask for Every Exam Problem

| # | Question | Details |
|---|----------|---------|
| **1** | **What does the problem WANT?** | A single path? Total flow volume? A grouping of nodes? A linear ordering? Maximum or minimum of what exactly? |
| **2** | **What type is the GRAPH?** | Directed or undirected? Does it have edge weights? Can those weights be negative? Does it have cycles? |
| **3** | **What is the SCOPE?** | Single source or all pairs? One specific s to t, or from s to all vertices? Do you need intermediate-step data (iter table)? |

---

## 🎯 Keyword → Algorithm Mapping

| Keyword / Phrase | Algorithm |
|------------------|-----------|
| `'fewest edges / hops / steps / connections'` | **Breadth-First Search** — unweighted shortest path |
| `'shortest / minimum cost path'` with positive weights | **Dijkstra's Algorithm** — requires all weights ≥ 0 |
| `'shortest path'` with possible negative weights | **Bellman-Ford** — handles any weights, single source |
| `'shortest path between ALL pairs'` | **Floyd-Warshall** — O(n³) all-pairs DP |
| `'maximum flow / throughput / bandwidth / volume'` | **Ford-Fulkerson** (integers) or **Edmonds-Karp** (any) |
| `'find the minimum cut / bottleneck separating s from t'` | Run Edmonds-Karp to completion → BFS on final residual graph Gᶠ* → L = reachable from s → min cut edges = fully saturated edges from L to R in original graph |
| `'verify / check if a given flow is maximum'` | Build residual graph Gᶠ in O(n+m), run DFS from s. If t unreachable → flow IS maximum. If t reachable → augmenting path exists → NOT maximum. Total: O(n+m) |
| `'minimum spanning tree / cheapest to connect all'` | **Kruskal's Algorithm** or **Prim's Algorithm** |
| `'widest path / maximize bottleneck / maximize minimum width'` | Kruskal's (negated weights) → max spanning tree → BFS for path |
| `'topological order / dependency / must come before / prerequisite'` | **Topological Sort** — graph MUST be a DAG (no cycles) |
| `'strongly connected / mutual reachability / round trip / two-way reachable'` | **Strongly Connected Components** |
| `'cycle detection'` in directed graph | **Depth-First Search** — back edge in DFS = cycle exists |
| `'connected components / separate groups / islands'` | **Depth-First Search** on undirected — group by ccnum[] |
| `'reachability / can A reach B'` | **Breadth-First Search** (dist[B] ≠ ∞?) or **Depth-First Search** (same ccnum?) |
| `'negative weights'` | Bellman-Ford (single source) or Floyd-Warshall (all pairs) |
| `'negative cycle detection'` | Bellman-Ford: check iter[n] vs iter[n-1] \| Floyd-Warshall: check dist[v][v] < 0 |
| `'bipartite matching / pair up two groups'` | Build flow network → Edmonds-Karp (super source + super sink + capacity 1 edges) |
| `'source SCC / sink SCC'` | Strongly Connected Components → inspect G_SCC metagraph adjacency lists |

---

## ⚙️ Input Modification Tricks

**Minimum Spanning Tree → Maximum Spanning Tree**
Negate ALL edge weights: for each edge e, set w(e) = -w(e). Then run Kruskal's Algorithm normally. The minimum of the negated weights = maximum of the original weights.

**Undirected graph → Directed flow network**
Replace each undirected edge (u, v) with capacity c with TWO directed edges: u→v with capacity c, AND v→u with capacity c.

**Multiple sources → Single super-source**
Add a new virtual vertex s*. Add edge s*→v with weight 0 (or ∞ for flow) for every real source vertex v. Run algorithm from s*.

**Multiple sinks → Single super-sink**
Add a new virtual vertex t*. Add edge v→t* with weight 0 (or ∞ for flow) from every real sink vertex v.

**Reverse a graph to get G^R**
Flip every directed edge. An edge u→v becomes v→u. This is an O(n+m) operation. State: 'Reverse the graph to create G^R.'

**Build subgraph keeping only certain edges**
'Create copy of G called G' keeping only edges satisfying [condition].' — O(n+m). Just state it.

**Convert Kruskal's edges[] to a graph**
State: 'Convert Kruskal's edges[] output to adjacency list G_MST.' This is O(n+m).

**Convert Prim's prev[] to a graph**
State: 'Build adjacency list G_MST from Prim's prev[] output.' This is O(n+m).

**Construct min st-cut from a max flow**
After running Ford-Fulkerson/Edmonds-Karp: (1) build final residual graph Gᶠ*, (2) BFS/DFS from s on Gᶠ*, (3) L = all reachable vertices, (4) min cut edges = all original edges v→w where v∈L and w∈R. All such edges are fully saturated (flow = capacity). O(n+m) after max flow.

---

## 📝 Required Solution Format — What Graders Look For

**Part (a): Algorithm description**
Write in NARRATIVE paragraph form. NO pseudocode, NO code. Describe: (1) input modifications, (2) which algorithm to run and what you pass in, (3) which outputs you use, (4) output modifications, (5) what you return as the final answer.

**Part (b): Correctness justification**
Explain WHY your approach is correct. How does the black box solve your specific problem? Why do your input/output modifications correctly map to the answer? This is informal — not a formal proof.

**Part (c): Runtime analysis**
Analyze EVERY step in Big-O notation. State known algorithm runtimes without justification. Give ONE final simplified Big-O answer at the end.

**NO pseudocode — serious point deduction**
Bullet points are fine. But writing code line-by-line in English = pseudocode = significant point loss. Write high-level narrative: 'Run Dijkstra's Algorithm from s using edge weights w...'

**Path reconstruction — don't forget it**
If the problem asks for the actual PATH (not just distance or cost): explicitly say 'Follow prev[] backwards from t to s in O(n) to reconstruct the path.'

**Graph operations have known runtimes**
Reversing a graph, copying a graph, building a subgraph, converting MST output to adjacency list — all are O(n+m). Just state the operation and the runtime.

---

## 🚫 Critical Mistakes to Avoid

**Modifying a black box algorithm**
You CANNOT change any algorithm's internals. No modifications, no inline tracking, no tweaked versions. Only change INPUTS before calling and use OUTPUTS after. Recreating an algorithm with modifications is the same as modifying it.

**Using Dijkstra's Algorithm with negative edge weights**
Dijkstra's WILL give wrong answers with negative weights. The greedy finality argument (a dequeued node's distance is final) breaks when negative edges exist. Always use Bellman-Ford when any weight could be negative.

**Using ccnum[] from DFS on a directed graph**
ccnum[] is unreliable on directed graphs. DFS may assign the same ccnum to nodes that aren't truly strongly connected. Use Strongly Connected Components to get correct groupings.

**Applying Topological Sort to a graph with cycles**
Topological Sort is undefined on cyclic graphs — it will give a wrong or meaningless result. Always verify the graph is a DAG first (no back edges in DFS), or use Strongly Connected Components to collapse cycles into a DAG first.

**Using Ford-Fulkerson with non-integer or large capacities**
Ford-Fulkerson's runtime is O(mC). With non-integer capacities or very large C, this may not terminate. Use Edmonds-Karp which guarantees polynomial O(nm²) runtime.

**Confusing max flow (total volume) with widest path (single route)**
Max flow = total throughput across ALL paths simultaneously. Widest path = ONE path with maximum bottleneck. They answer completely different questions. Flow → Ford-Fulkerson/Edmonds-Karp. Widest path → Max Spanning Tree + BFS.

**Forgetting to handle anti-parallel edges before max flow**
If original graph has both u→v and v→u, pre-process BEFORE running Ford-Fulkerson/Edmonds-Karp: replace one direction (e.g., v→u) with two-hop path v→x→u via auxiliary vertex x. Set both new edge capacities = original c_{vu}.

**Thinking backward residual edges mean reverse flow**
Backward edge w→v in Gᶠ with capacity f_{vw} does NOT mean flow goes w→v. It means: we can CANCEL up to f_{vw} units of existing flow on v→w. Cancellation, not reverse flow.

**Assuming s and t might not be in the spanning tree**
A spanning tree SPANS ALL n vertices by definition. s and t are always present in any spanning tree. The s→t path in the tree always exists and is unique.

**Running Kruskal's on a disconnected graph**
Kruskal's requires CONNECTED undirected input. Verify connectivity first with BFS/DFS in O(n+m). State this guarantee explicitly. Workaround: run Kruskal's on connected subgraphs separately then combine subtrees.

**Forgetting to convert MST output before using it as a graph**
Kruskal's edges[] and Prim's prev[] are not adjacency lists. To run BFS or DFS on the MST, first state: 'Convert edges[] to adjacency list G_MST in O(n+m).' Then run BFS/DFS on G_MST.

---

## ⏱️ Runtime Quick Reference Card

| Algorithm / Operation | Runtime |
|------------------------|---------|
| Depth-First Search | O(n + m) |
| Breadth-First Search | O(n + m) |
| Topological Sort | O(n + m) |
| Strongly Connected Components | O(n + m) |
| Dijkstra's Algorithm | O((n + m) log n) |
| Kruskal's Algorithm | O(m log n) |
| Prim's Algorithm | O(m log n) |
| Bellman-Ford | O(nm) |
| Floyd-Warshall | O(n³) |
| Ford-Fulkerson | O(mC) where C = maximum flow value |
| Edmonds-Karp | O(nm²) |
| Verify max flow (check if f is maximum) | O(n+m) — build residual graph + DFS from s |
| Construct min cut from max flow | O(n+m) — BFS on final residual graph after max flow |
| 2-SAT | O(n + m) |
| Graph reverse / copy / subgraph / MST conversion | O(n + m) |
| Single vertex or edge lookup | O(1) |
| Iterating all vertices | O(n) |
| Iterating all edges | O(m) |
| Path reconstruction following prev[] | O(n) |

---

There you go — the complete Exam Tips page fully transcribed into markdown! It covers all 5 major sections: the 3 core exam questions, keyword→algorithm mapping, input modification tricks, required solution format, critical mistakes to avoid, and the runtime quick reference card.