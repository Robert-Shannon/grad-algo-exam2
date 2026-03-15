31 patterns across 7 categories
🛤️ Category 1: Advanced Path Problems
7 patterns

Beyond basic shortest path — when the path has constraints like required stops, forbidden nodes, bounded hops, or a multiplicative objective instead of additive.


Pattern 1 — Shortest Path Through a Required Intermediate Node v
Pipeline: Dijkstra's from s → Dijkstra's from v → Add two distances | Runtime: O((n+m) log n)
Problem: Find the shortest path from s to t that is REQUIRED to pass through node v.
Why this works: Any s→v→t path has cost = dist(s,v) + dist(v,t). Since Dijkstra's finds shortest distances from a single source to ALL nodes, two runs give us both halves. The optimal forced path simply takes the best of each half independently.
Step-by-Step Solution Template:

Run Dijkstra's Algorithm from source s. Record dist_s[] — shortest from s to every vertex.
Run Dijkstra's Algorithm from required node v. Record dist_v[] — shortest from v to every vertex.
Answer = dist_s[v] + dist_v[t]. Reconstruct path by tracing prev[] from v to s (reversed) then prev[] from t back to v.
Runtime: O((n+m) log n) for each Dijkstra's run = O((n+m) log n) total.


⚠️ Gotchas: If the graph is directed, dist(v,t) requires Dijkstra's on the ORIGINAL graph from v — not a reverse graph run. Works for multiple required waypoints: chain Dijkstra's from each waypoint in order.
Example: Delivery truck must stop at the warehouse (v) on the way from depot (s) to customer (t). Minimize total distance.


Pattern 2 — Shortest Path With at Most k Edges (Hop-Constrained)
Pipeline: Bellman-Ford → Read iter[k][t] | Runtime: O(nm)
Problem: Find the minimum-cost path from s to t using no more than k edges.
Why this works: Bellman-Ford's iter[i][v] table records the shortest distance to v using at most i edges. This is its core definition — iter[k][t] is the exact answer. No other standard algorithm provides this intermediate iteration data.
Step-by-Step Solution Template:

Run Bellman-Ford from source s. This produces the full iter[i][v] table for i = 0 to n.
Read iter[k][t] directly from the table. This is the shortest s→t path using at most k edges.
If iter[k][t] = ∞, no path exists within k hops.
Runtime: O(nm) for the full Bellman-Ford run.


⚠️ Gotchas: You need the full iter table, not just the converged dist[] — this is a key reason to use Bellman-Ford over Dijkstra's even on positive-weight graphs. If k ≥ n-1, just read the final dist[t] — convergence guarantees it's the true shortest.
Example: Find the minimum-cost flight itinerary with at most k layovers.


Pattern 3 — Most Reliable Path (Maximize Product of Survival Probabilities)
Pipeline: Transform w'(e) = -log(p(e)) → Dijkstra's Algorithm → Exponentiate result | Runtime: O((n+m) log n)
Problem: Each edge has a probability p(e) of success. Find the path from s to t that MAXIMIZES the product of probabilities.
Why this works: Maximizing ∏p(e) is equivalent to maximizing ∑log(p(e)), which equals minimizing ∑(-log(p(e))). Taking -log transforms a multiplicative product into an additive sum, allowing standard Dijkstra's to find the optimal path.
Step-by-Step Solution Template:

For every edge e with probability p(e), compute w'(e) = -log(p(e)). Note: since 0 < p(e) ≤ 1, all w'(e) ≥ 0 — Dijkstra's is valid.
Run Dijkstra's Algorithm with the transformed weights w'.
dist[t] = the minimum sum of -log(p) = maximum log(probability). The actual max probability = e^(-dist[t]).
Follow prev[] to get the optimal path.
Runtime: O((n+m) log n).


⚠️ Gotchas: Never use Bellman-Ford here — all w'(e) ≥ 0 since p(e) ≤ 1. The transformation only works for independent edge probabilities.
Example: Network routing: find the path where the packet is most likely to arrive intact.


Pattern 4 — Longest Path in a DAG
Pipeline: Topological Sort → DP with maximize instead of minimize | Runtime: O(n+m)
Problem: Find the maximum-weight path from source s to every vertex in a DAG.
Why this works: Topological order ensures every predecessor of v is processed before v. We relax edges in that order, keeping the MAXIMUM running total instead of minimum. No cycles means longest path is well-defined. On general graphs with cycles, longest path is NP-hard.
Step-by-Step Solution Template:

Run Topological Sort on the DAG. Get order[].
Initialize dist[s] = 0, dist[v] = -∞ for all v ≠ s.
For each vertex u in topological order[], for each neighbor v: if dist[u] + w(u,v) > dist[v], update dist[v] = dist[u] + w(u,v) and prev[v] = u.
dist[t] = length of the longest path from s to t. Trace prev[] for the actual path.
Runtime: O(n+m) — Topological Sort is O(n+m), DP is O(n+m).


⚠️ Gotchas: Requires a DAG — longest path on cyclic graphs with positive weights is NP-hard. Alternative: negate all weights and find SHORTEST path, then negate the answer.
Example: Critical path method (CPM) in project scheduling: find the longest dependency chain.


Pattern 5 — Shortest Path Avoiding a Specific Set of Forbidden Nodes
Pipeline: Remove forbidden nodes from graph → Dijkstra's / BFS on reduced graph | Runtime: O((n+m) log n)
Problem: Find shortest path from s to t that does NOT pass through any node in a forbidden set F.
Why this works: Removing forbidden nodes and all their incident edges gives a subgraph where any remaining path is guaranteed to avoid F. Running shortest path on this subgraph directly gives the constrained answer.
Step-by-Step Solution Template:

Build G' = subgraph of G keeping only vertices NOT in F, and only edges between non-forbidden vertices. This is O(n+m).
If s or t is in F, no valid path exists.
Run Dijkstra's Algorithm (weighted) or BFS (unweighted) from s on G'. dist[t] = constrained shortest path.
Runtime: O((n+m) log n) for weighted, O(n+m) for unweighted.


⚠️ Gotchas: If the forbidden set changes per query, this approach requires re-running for each query. For forbidden EDGES (not nodes), simply omit those edges when building G'.


Pattern 6 — Diameter of a Graph (The Longest Shortest Path Between Any Pair)
Pipeline: Floyd-Warshall → max over all pairs dist[u][v] | Runtime: O(n³)
Problem: Find the maximum shortest-path distance between any two vertices. This is the "width" of the graph.
Why this works: Floyd-Warshall computes all-pairs shortest paths in one pass. The diameter is simply the maximum value in the dist[][] table (excluding ∞ entries for disconnected pairs).
Step-by-Step Solution Template:

Run Floyd-Warshall on the graph. Get dist[u][v] for all pairs.
Iterate over all pairs (u,v): diameter = max { dist[u][v] : dist[u][v] ≠ ∞ }.
If any dist[v][v] < 0 after the run, a negative cycle exists — diameter is undefined.
Runtime: O(n³) for Floyd-Warshall + O(n²) scan = O(n³).


⚠️ Gotchas: On unweighted graphs, run BFS from every vertex O(n·(n+m)) — faster for sparse graphs. On directed graphs, diameter is NOT symmetric: dist(u,v) ≠ dist(v,u). On disconnected graphs, the diameter is only meaningful within connected components.


Pattern 7 — Shortest Path in Graph With 0 and 1 Edge Weights Only
Pipeline: Model as BFS with deque (0-cost edges go to front, 1-cost to back) | Runtime: O(n+m)
Problem: All edge weights are either 0 or 1. Find the shortest weighted path from s to t.
Why this works: Standard BFS gives hop count (unweighted). Dijkstra's gives weighted shortest path but costs O((n+m) log n). With only 0/1 weights, a deque-BFS runs in O(n+m): push to front for 0-cost edges (no distance increase), push to back for 1-cost edges. This is essentially Dijkstra's on two priority levels.
Step-by-Step Solution Template:

Initialize dist[s] = 0, all others = ∞. Use a deque (double-ended queue).
Push s to the deque front.
While deque is non-empty: pop from front. For each neighbor v: if dist[u] + w(u,v) < dist[v], update dist[v]. If w(u,v) = 0, push v to FRONT; if w(u,v) = 1, push v to BACK.
dist[t] = shortest weighted path. Runtime: O(n+m).
In a black-box framework: use BFS after splitting 1-weight edges with a virtual intermediate node.


⚠️ Gotchas: This only works for {0,1} weights. For general small integers, use Dial's algorithm or Dijkstra's. The virtual-node approach is the black-box-safe version: split every weight-1 edge (u,v) into u→x→v with two weight-0 edges.


🌊 Category 2: Flow Network Reductions
5 patterns

Many problems that look nothing like flow can be REDUCED to max flow by clever graph construction. The key insight: max flow = maximum matching, maximum routing, minimum cut. Build the right network, run Edmonds-Karp, read off the answer.


Pattern 8 — Maximum Bipartite Matching
Pipeline: Build flow network (super-source + super-sink + unit capacities) → Edmonds-Karp | Runtime: O(n·m²)
Problem: Given two groups L and R with edges between them, find the maximum set of pairings such that each node appears in at most one pair.
Why this works: Bipartite matching = max flow in a carefully constructed network. Each potential match becomes a capacity-1 pipe. Flow conservation ensures each node participates in at most one match. The maximum flow equals the maximum matching.
Step-by-Step Solution Template:

Add virtual super-source s* and super-sink t*.
Add directed edge s*→v with capacity 1 for every node v in left group L.
Add directed edge v→t* with capacity 1 for every node v in right group R.
For each original edge (u,v) between L and R, add directed edge u→v with capacity 1.
Run Edmonds-Karp from s* to t*. Output C = maximum matching size. flow[u→v] = 1 means u is matched to v.
Runtime: O(n·m²) for Edmonds-Karp.


⚠️ Gotchas: The graph now has n_L + n_R + 2 vertices and m + n_L + n_R edges. Unit capacities are critical — if you use ∞, the matching constraint breaks. Max matching ≠ perfect matching. A perfect matching requires C = |L| = |R|.


Pattern 9 — Maximum Number of Edge-Disjoint Paths from s to t
Pipeline: Set all capacities to 1 → Edmonds-Karp | Runtime: O(n·m²)
Problem: Find the maximum number of paths from s to t such that no two paths share any edge.
Why this works: By Menger's theorem, the maximum number of edge-disjoint s→t paths equals the maximum flow when every edge has capacity 1. Each unit of flow represents one independent path.
Step-by-Step Solution Template:

For each edge in the original graph, set its capacity to 1. For undirected edges, add two directed edges (u→v and v→u) each with capacity 1.
Run Edmonds-Karp from s to t on this unit-capacity network.
Output C = maximum number of edge-disjoint paths.
To recover actual paths: trace which edges carry flow = 1 and extract connected paths from s to t.
Runtime: O(n·m²).


⚠️ Gotchas: "Edge-disjoint" ≠ "vertex-disjoint." Two paths can share intermediate vertices but not edges. The minimum edge cut size = C (by max-flow min-cut theorem) = minimum number of edges to remove to disconnect s from t.


Pattern 10 — Maximum Number of Vertex-Disjoint Paths from s to t
Pipeline: Split each vertex into v_in → v_out (capacity 1) → Edmonds-Karp on expanded graph | Runtime: O(n·m²) on expanded graph
Problem: Find the maximum number of paths from s to t such that no two paths share any intermediate vertex.
Why this works: Vertex capacity is enforced by splitting each vertex v into v_in and v_out with a capacity-1 edge between them. Any path through v must use this bottleneck edge, so at most one path can pass through v.
Step-by-Step Solution Template:

For every vertex v (except s and t), create two nodes v_in and v_out. Add directed edge v_in→v_out with capacity 1.
For every original edge (u,v): add directed edge u_out→v_in with capacity ∞. For undirected edges, also add v_out→u_in.
Source s: use s_out. Sink t: use t_in.
Run Edmonds-Karp from s_out to t_in. Output C = maximum vertex-disjoint paths.
Runtime: O(n·m²).


⚠️ Gotchas: The split graph has 2n vertices and m + n edges. s and t are NOT split (or their internal edge has capacity ∞). Vertex-disjoint is strictly harder than edge-disjoint — there are always at least as many edge-disjoint paths as vertex-disjoint.


Pattern 11 — Multi-Source Multi-Sink Maximum Flow
Pipeline: Add virtual super-source s* and super-sink t* → Edmonds-Karp | Runtime: O(n·m²)
Problem: Multiple sources {s1, s2, ...} produce flow, multiple sinks {t1, t2, ...} consume flow. Find the maximum total flow.
Why this works: By adding a virtual super-source connected to every real source with ∞ capacity, and a virtual super-sink connected from every real sink with ∞ capacity, the problem reduces to a standard single-source single-sink max flow.
Step-by-Step Solution Template:

Add virtual super-source s*. For each real source si, add directed edge s*→si with capacity ∞.
Add virtual super-sink t*. For each real sink tj, add directed edge tj→t* with capacity ∞.
Run Edmonds-Karp from s* to t*. Total max flow C = total throughput across all sources and sinks.
Runtime: O(n·m²) on the expanded graph.


⚠️ Gotchas: If each source has a production limit L_i, set capacity of s*→si = L_i instead of ∞. If each sink has a consumption limit, similarly bound t_j→t*. The ∞ capacity edges model unlimited production/consumption.


Pattern 12 — Minimum Cut (Find the Bottleneck Separating s from t)
Pipeline: Edmonds-Karp → BFS on residual graph → Partition reachable/unreachable | Runtime: O(n·m²)
Problem: Find the minimum-capacity set of edges whose removal disconnects s from t.
Why this works: After running max flow to completion, the residual graph captures which edges have remaining capacity. BFS from s on the residual graph finds all vertices reachable from s. Cut edges are those going from a reachable vertex to a non-reachable vertex in the original graph.
Step-by-Step Solution Template:

Run Edmonds-Karp from s to t. Get flow[] and the final residual graph.
Run BFS from s on the RESIDUAL graph (only traverse edges with remaining capacity > 0). Let S = set of vertices reachable from s.
The min cut edges = all original edges (u,v) where u ∈ S and v ∉ S. These are exactly the edges saturated by the max flow.
Min cut capacity = sum of capacities of cut edges = C (the max flow value).
Runtime: O(n·m²) for Edmonds-Karp + O(n+m) for BFS = O(n·m²).


⚠️ Gotchas: The min cut is NOT just the saturated edges — it's saturated edges crossing from S to T (not T to S). For undirected graphs, edges in both directions must be considered. Max-flow min-cut theorem: min cut capacity = max flow value always.


🧩 Category 3: SCC as a Preprocessing Step
5 patterns

Strongly Connected Components is almost never the FINAL answer — it is a preprocessing tool that converts a messy cyclic directed graph into a clean DAG. Once you have the DAG metagraph G_SCC, you can apply Topological Sort, shortest path, BFS, or any DAG algorithm.


Pattern 13 — Topological Ordering of a Directed Graph That Has Cycles
Pipeline: SCC → Topological Sort on G_SCC metagraph | Runtime: O(n+m)
Problem: A directed graph has cycles (so Topological Sort would fail directly). Find a valid ordering of the SCCs from sources to sinks.
Why this works: SCCs represent groups where internal order doesn't matter for inter-group dependencies. The metagraph G_SCC is always a DAG. Topological Sort on G_SCC gives the dependency order of the groups.
Step-by-Step Solution Template:

Run Strongly Connected Components on the directed graph. Get G_SCC = (V_SCC, E_SCC) and ccnum[] mapping.
G_SCC is already sorted SINK→SOURCE (reverse topological order). Reverse V_SCC order to get SOURCE→SINK topological order.
Alternatively, run Topological Sort on G_SCC directly to get order[] on the metanodes.
Map any original vertex v to its metanode using ccnum[v].
Runtime: O(n+m) for SCC + O(n_SCC + m_SCC) for Topo Sort = O(n+m).


⚠️ Gotchas: You're ordering SCCs, not individual vertices. Vertices WITHIN an SCC are all mutually reachable — their internal order is irrelevant for inter-SCC dependencies. If you need individual vertex ordering within each SCC, you need additional processing.


Pattern 14 — Shortest Path in a Directed Graph With Cycles (Condensation Approach)
Pipeline: SCC → Build weighted G_SCC → Dijkstra's / Bellman-Ford on G_SCC | Runtime: O((n+m) log n)
Problem: Find shortest path from s to t in a directed graph where SCCs may exist.
Why this works: Within an SCC, any vertex can reach any other — the internal cost is a constant that can be precomputed. Edges between SCCs are then processed by treating each SCC as a single supernode.
Step-by-Step Solution Template:

Run SCC. Get G_SCC and ccnum[].
Build weighted G_SCC: for each inter-SCC edge (u,v) in original graph where ccnum[u] ≠ ccnum[v], add edge ccnum[u]→ccnum[v] with weight w(u,v) to G_SCC.
Run Dijkstra's Algorithm on G_SCC from ccnum[s] to ccnum[t].
Add internal traversal costs within the source and destination SCCs if needed.
Runtime: O(n+m) for SCC + O((n+m) log n) for Dijkstra's = O((n+m) log n).


⚠️ Gotchas: If t is in the same SCC as s, the answer is the internal traversal cost within that SCC. Multiple edges between the same pair of SCCs should be reduced to the minimum-weight one.


Pattern 15 — Can s Reach t in a Directed Graph? (Directed Reachability)
Pipeline: SCC → BFS/DFS on G_SCC from ccnum[s] | Runtime: O(n+m)
Problem: Given a directed graph (possibly cyclic), determine if there exists a path from s to t.
Why this works: If s and t are in the same SCC, s can trivially reach t. Otherwise, check if ccnum[t] is reachable from ccnum[s] in the DAG metagraph G_SCC.
Step-by-Step Solution Template:

Run SCC. If ccnum[s] == ccnum[t], return YES (same SCC = mutual reachability).
Otherwise, run BFS from ccnum[s] on G_SCC.
If ccnum[t] is reached by BFS (dist[ccnum[t]] ≠ ∞), return YES. Otherwise NO.
Runtime: O(n+m) for SCC + O(n_SCC + m_SCC) for BFS = O(n+m).


⚠️ Gotchas: This is faster than running BFS on the original graph only when you need to answer many reachability queries. For a single query, just DFS from s on the original directed graph — O(n+m) without needing SCC.
Example: In a computer network with one-way data links, can data from router A reach router B?


Pattern 16 — Find the Unique SCC That All Other SCCs Can Reach (Universal Sink SCC)
Pipeline: SCC → Find sink in G_SCC (V_SCC[1] or empty out-edges) | Runtime: O(n+m)
Problem: Find the SCC(s) in the condensation graph that have no outgoing edges — every path eventually ends here.
Why this works: In G_SCC (a DAG), sink nodes have no outgoing edges. G_SCC is sorted SINK→SOURCE, so V_SCC[1] is always a sink.
Step-by-Step Solution Template:

Run SCC. Get G_SCC = (V_SCC, E_SCC).
V_SCC[1] is a sink SCC (first in reverse-topo order = first sink).
To find ALL sink SCCs: iterate V_SCC and collect every metanode with an empty adjacency list in E_SCC.
All original vertices v with ccnum[v] = sink metanode index are in a sink SCC.
Runtime: O(n+m) for SCC + O(n_SCC + m_SCC) scan = O(n+m).


⚠️ Gotchas: There may be multiple sink SCCs if the condensation DAG has multiple sinks. If the condensation DAG has exactly ONE sink SCC, then ALL vertices in the graph can reach it.


Pattern 17 — Minimum Edges to Add to Make Entire Graph Strongly Connected
Pipeline: SCC → Count source SCCs and sink SCCs in G_SCC → Answer = max(sources, sinks) | Runtime: O(n+m)
Problem: A directed graph is not strongly connected. What is the minimum number of edges to add to make it strongly connected?
Why this works: In G_SCC (a DAG), source SCCs and sink SCCs are the "endpoints" that prevent strong connectivity. To connect everything, we need to add edges linking sinks to sources. The minimum is max(number of sources, number of sinks).
Step-by-Step Solution Template:

Run SCC to get G_SCC.
Count P = number of source SCCs in G_SCC (no incoming inter-SCC edges).
Count Q = number of sink SCCs in G_SCC (no outgoing inter-SCC edges).
Minimum edges to add = max(P, Q). Special case: if already 1 SCC, answer = 0.
Runtime: O(n+m) for SCC + O(n_SCC + m_SCC) for counting = O(n+m).


⚠️ Gotchas: If |V_SCC| = 1, the graph is already strongly connected — answer is 0. The pairing works by connecting a sink to a source in a cycle, which is why max(P,Q) suffices rather than P+Q.


🔀 Category 4: Graph Transformation Tricks
4 patterns

The most powerful technique: transform the graph into a different graph that a standard black-box can solve directly. The key is that the new graph's structure maps directly to your problem's constraints.


Pattern 18 — Multi-Source Shortest Path (Many Valid Starting Points)
Pipeline: Add virtual super-source s* with 0-weight edges to all real sources → Dijkstra's / BFS from s* | Runtime: O((n+m) log n)
Problem: Multiple starting nodes are all valid. Find the shortest distance from ANY source to every other vertex.
Why this works: Running Dijkstra's from each source separately costs O(k·(n+m) log n). One virtual super-source s* connected to all real sources with 0-weight edges reduces this to a single O((n+m) log n) run. The 0-weight edges don't change distances.
Step-by-Step Solution Template:

Create virtual node s*. Add edge s*→v with weight 0 for every real source v.
Run Dijkstra's Algorithm from s* (or BFS if unweighted).
dist[v] for any vertex v = minimum distance from any real source to v.
prev[v] traces the path back. The first real source encountered going back from s* = the closest source.
Runtime: O((n+m) log n).


⚠️ Gotchas: For flow problems, add s*→si with capacity equal to si's supply limit. For BFS (unweighted), just add all sources to the initial BFS queue with distance 0 — no virtual node needed. The virtual node trick is mainly needed for weighted graphs.


Pattern 19 — Backward Reachability (Which Nodes Can Reach t?)
Pipeline: Reverse all edges to get G^R → BFS / Dijkstra's from t on G^R | Runtime: O((n+m) log n)
Problem: Find all vertices that can reach a specific target t (instead of all vertices reachable FROM a source).
Why this works: In G^R (all edges reversed), a path from u to t in G becomes a path from t to u in G^R. BFS/Dijkstra's from t on G^R gives exactly the set of vertices that could reach t in the original graph.
Step-by-Step Solution Template:

Build G^R by reversing all directed edges. This is O(n+m).
Run BFS from t on G^R.
All vertices with dist[v] ≠ ∞ in G^R = all vertices that can reach t in the original G.
For weighted "shortest path leading to t": run Dijkstra's on G^R from t. dist[v] = shortest path from v to t in original G.
Runtime: O(n+m) to reverse + O((n+m) log n) for Dijkstra's = O((n+m) log n).


⚠️ Gotchas: The path in G^R is the REVERSE of the actual path in G. To reconstruct s→t path in G, trace prev[] in G^R from s back, then reverse the path. This is already used internally in SCC (Kosaraju's algorithm runs DFS on G^R).


Pattern 20 — Layered Graph for State-Dependent Path Problems
Pipeline: Build k-layer copy of graph → Dijkstra's / BFS on layered graph | Runtime: O(k·(n+m) log(kn))
Problem: A path's cost depends on some state that changes as you travel (e.g., "you can use a discount coupon exactly once," or "you have k lives remaining").
Why this works: States turn the problem from a pure graph search into a search on (node, state) pairs. By making k copies of the graph — one per state — we encode state transitions as edges BETWEEN layers. Dijkstra's on the layered graph finds the optimal (node, state) path.
Step-by-Step Solution Template:

Identify the state space with k possible states (e.g., k=2 for "coupon used" vs "coupon not used").
Create k copies of the graph: layer 0 = state 0, layer 1 = state 1, ..., layer k-1 = state k-1.
For edges that DON'T trigger a state change: add the edge within the same layer.
For edges that DO trigger a state change (e.g., using a coupon on edge e): add a cross-layer edge from layer i to layer i+1 with the modified cost.
Run Dijkstra's from (s, initial_state) to (t, any_state). Take the minimum over all final states.
Runtime: O(k·(n+m) log(kn)).


⚠️ Gotchas: The layered graph has k·n vertices and up to k·m + (transition edges) total edges. State must be discrete and finite for this to work. Continuous states require different DP approaches.
Example: "Use a coupon for half-price on exactly one edge. Find the cheapest path from s to t."


Pattern 21 — Shortest Path on a Graph With Per-Vertex Costs (Not Per-Edge)
Pipeline: Split each vertex into v_in and v_out → Dijkstra's on expanded graph | Runtime: O((n+m) log n)
Problem: Costs are associated with visiting VERTICES, not traversing edges. Find the minimum-cost path from s to t.
Why this works: Vertex costs don't fit standard shortest-path algorithms directly. By splitting each vertex v into v_in and v_out with an internal edge of weight cost(v), we convert vertex costs into edge costs.
Step-by-Step Solution Template:

For each vertex v with cost c(v): create v_in and v_out. Add directed edge v_in→v_out with weight c(v).
For each original edge (u,v): add directed edge u_out→v_in with weight 0.
Source: start from s_in (or s_out if you don't pay for s). Destination: t_out.
Run Dijkstra's from s_in to t_out on the expanded graph.
dist[t_out] = minimum vertex-cost path from s to t.
Runtime: O((n+m) log n) on the 2n-vertex expanded graph.


⚠️ Gotchas: This is also the pattern for vertex-disjoint paths (set c(v) = capacity 1). Edge costs can coexist: add edge weights directly to the u_out→v_in edges instead of 0.


🌲 Category 5: Spanning Tree Combinations
3 patterns

The MST is not just about minimum cost — it encodes optimal bottleneck properties. Combined with BFS/DFS traversal of the tree, it answers a surprising range of path optimization questions.


Pattern 22 — All-Pairs Widest Path (Bottleneck for Every Pair u,v)
Pipeline: Kruskal's (negated) → max spanning tree → For each query (u,v): BFS in MST | Runtime: O(m log n + n² query cost)
Problem: Preprocess the graph so that for ANY pair (u,v), you can quickly answer: what is the widest (maximum bottleneck) path between them?
Why this works: The max spanning tree has a key theorem: for ANY pair (u,v), the unique path between them in the max spanning tree IS the globally widest path. Build the MST once, then answer each query by finding the path in the tree.
Step-by-Step Solution Template:

Negate all edge weights. Run Kruskal's Algorithm to get max spanning tree edges[].
Build adjacency list G_MST from edges[] in O(n+m).
For each query (u,v): run BFS from u on G_MST. Follow prev[] from v back to u. The minimum-weight edge on this tree path = the widest path bottleneck in the original graph.
Total runtime: O(m log n) for Kruskal's + O(n+m) per query.
For all n² pairs: O(m log n) preprocessing + O(n·(n+m)) for all queries.


⚠️ Gotchas: Remember to un-negate the edge weight when reading the bottleneck value. The MST gives the widest PATH (single source to single destination bottleneck). This is different from "widest spanning tree" (minimum max edge across all spanning trees).


Pattern 23 — Minimum Bottleneck Spanning Tree (Same as MST)
Pipeline: Kruskal's Algorithm (standard) | Runtime: O(m log n)
Problem: Find a spanning tree that minimizes the MAXIMUM edge weight used (minimize the worst edge in the tree).
Why this works: A surprising theorem: the Minimum Spanning Tree IS the Minimum Bottleneck Spanning Tree. The maximum-weight edge in ANY MST is at most as large as the maximum-weight edge in any other spanning tree. Kruskal's naturally avoids heavy edges, minimizing the bottleneck.
Step-by-Step Solution Template:

Run Kruskal's Algorithm normally (no negation needed).
The resulting MST has the minimum possible maximum-edge-weight among all spanning trees.
The bottleneck = the weight of the heaviest edge in the MST.
Runtime: O(m log n).


⚠️ Gotchas: This is NOT the same as the widest single path problem. Minimum bottleneck SPANNING TREE minimizes the worst edge across the whole tree. Widest PATH minimizes the worst edge on one specific route. The MST might not give the minimum-bottleneck PATH between two specific vertices — use the negation trick for that.


Pattern 24 — Check if a Specific Edge Must Be in EVERY Minimum Spanning Tree
Pipeline: Kruskal's Algorithm → Check if removing that edge disconnects the MST | Runtime: O(m log n)
Problem: Given an edge e, determine if e appears in ALL possible MSTs.
Why this works: An edge e is in every MST if and only if it is the unique minimum-weight edge crossing some cut. If removing e from the MST disconnects it and e has strictly smaller weight than every other edge crossing that cut, then e is mandatory.
Step-by-Step Solution Template:

Run Kruskal's to get one MST.
Check if edge e is in the MST. If not, it's definitely not in every MST.
If e IS in the MST: temporarily remove e from G_MST. Run BFS/DFS to find the two components C1 and C2.
Check if any other edge between C1 and C2 has the SAME weight as e. If yes, e could be swapped out — it's not mandatory. If all other crossing edges are strictly heavier, e is in every MST.
Runtime: O(m log n) for Kruskal's + O(n+m) for BFS = O(m log n).


⚠️ Gotchas: Weight ties are the key case: if another edge has the same weight, it's an alternative, and e is NOT in every MST. This test is valid because Kruskal's can swap equal-weight edges freely.


🧮 Category 6: DP on Graphs (DAG + Relaxation)
3 patterns

Graph DP problems require topological order to ensure subproblems are solved before they are needed. The combination is always: (1) establish ordering via Topo Sort or Bellman-Ford iterations, (2) fill DP table using that ordering.


Pattern 25 — Count the Number of Shortest Paths from s to t
Pipeline: Dijkstra's Algorithm → DP count pass in order of dist[] | Runtime: O((n+m) log n)
Problem: Findyou didnt fucking finish.31 steps31 stepsYou're right, I cut off mid-pattern 25. Let me finish it.Created a planJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolI now have all the data. Let me also get the full text of the extra patterns collected separately.Javascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolJavascript toolI now have every piece of data I need. Let me also get the full text of pattern r15 (Check if edge must be in every MST) and r14 (Min bottleneck ST) tails:Javascript toolJavascript toolI now have everything. Let me write the complete finish of the markdown.DoneMy bad — here's the complete finish, picking up exactly where I cut off:
Pattern 25 — Count the Number of Shortest Paths from s to t
Pipeline: Dijkstra's Algorithm → DP count pass in order of dist[] | Runtime: O((n+m) log n)
Problem: Find both the shortest distance from s to t AND the number of distinct shortest paths achieving that distance.
Why this works: Dijkstra's gives the shortest distance and a valid prev[]. To COUNT all shortest paths, process vertices in order of their dist[] values (the "topological order" of the shortest path DAG). For each vertex, count[v] = sum of count[u] for all u where dist[u] + w(u,v) = dist[v].
Step-by-Step Solution Template:

Run Dijkstra's Algorithm from s. Get dist[] for all vertices.
Initialize count[s] = 1, count[v] = 0 for all v ≠ s.
Process vertices in increasing order of dist[v] (i.e., the order Dijkstra's dequeued them).
For each vertex u in that order, for each neighbor v: if dist[u] + w(u,v) == dist[v], then count[v] += count[u].
count[t] = total number of shortest paths from s to t.
Runtime: O((n+m) log n).


⚠️ Gotchas: This creates a DAG of shortest-path edges (only edges that lie on a shortest path). DP on this DAG counts paths. If dist[v] can be updated multiple times in Dijkstra's, only add to count[v] from vertices u with the finalized shortest distance.
Example: How many equally fast routes exist from s to t? Useful for load balancing across multiple optimal paths.


Pattern 26 — Shortest Path With a Fixed Number of Allowed Negative Edges
Pipeline: Build layered graph (k+1 layers for 0..k negative edges used) → Dijkstra's on layered graph | Runtime: O(k·(n+m) log(kn))
Problem: A graph has both positive and negative edges. Find the shortest path from s to t where you may use AT MOST k negative-weight edges.
Why this works: Dijkstra's fails with negative edges. A layered graph tracks how many negative edges have been used as a state. Positive edges stay within a layer; negative edges advance to the next layer. Dijkstra's on this layered graph is valid because all same-layer edges are positive.
Step-by-Step Solution Template:

Create k+1 layers (layer i = "used exactly i negative edges so far").
For positive-weight edges (u,v): copy edge within each layer — (u,i)→(v,i) with weight w(u,v).
For negative-weight edges (u,v): add cross-layer edge (u,i)→(v,i+1) with weight w(u,v).
Alternative cleaner approach: negate the negative edges and use layered Dijkstra's where crossing a layer uses a formerly-negative edge for free.
Run Dijkstra's from (s, layer 0). Answer = min over all layers of dist[(t, layer i)].
Runtime: O(k·(n+m) log(kn)).


⚠️ Gotchas: If k ≥ number of negative edges, just use Bellman-Ford directly. The layered graph approach is most useful when k is small (k=1 or k=2).
Example: Find the shortest delivery route where you can use at most one "express discount" road (negative cost) along the way.


Pattern 27 — Longest Path in General Graph With No Positive Cycles (But With Negative Edges)
Pipeline: Bellman-Ford with negated weights → Check for negative cycles in negated graph | Runtime: O(nm)
Problem: Find the longest weighted path from s to t in a graph that may have negative edges but no positive-weight cycles.
Why this works: Negate all edge weights. A longest path in the original = shortest path in the negated graph. Bellman-Ford handles negative weights. A positive cycle in the original = a negative cycle in the negated graph — use Bellman-Ford's cycle detection to verify the problem is well-defined.
Step-by-Step Solution Template:

Negate all edge weights: w'(e) = -w(e).
Run Bellman-Ford on the negated graph from s.
If Bellman-Ford detects a negative cycle (in the negated graph), a positive cycle exists in the original — longest path is undefined (unbounded).
Otherwise, dist[t] in the negated graph = -(longest path from s to t in original). Answer = -dist[t].
Runtime: O(nm).


⚠️ Gotchas: On a DAG, use Topological Sort + DP instead — O(n+m) is much faster. Detecting a positive cycle means the problem is unbounded — report "no finite longest path."
Example: Maximum profit path through a trading network where some trades have costs (negative profit) but no sequence of trades makes unlimited profit.


🔎 Category 7: Detection Problems (Verify Properties First, Then Solve)
4 patterns

Many problems require first verifying a property (is this a DAG? does a negative cycle exist? is the graph connected?) before applying the main algorithm. These are two-phase pipelines: detect, then act.


Pattern 28 — Verify Graph is a DAG, Then Apply Topological DP
Pipeline: DFS (check back edges) → Topological Sort + DP if no back edges | Runtime: O(n+m)
Problem: A problem requires DAG-based dynamic programming, but you're not certain the input is a DAG.
Why this works: Topological Sort gives wrong results on cyclic graphs. DFS detects cycles via back edges in O(n+m). If a cycle exists, report error or switch algorithms. If no cycles found, Topological Sort is safe.
Step-by-Step Solution Template:

Run DFS on the directed graph. Check all post[] values.
For every edge (u,v) in the original graph: if post[u] < post[v], a back edge exists → graph HAS a cycle → cannot apply DAG algorithms.
If no back edges found → graph is a DAG → run Topological Sort, then apply DP.
Note: DFS output (pre[], post[], order[]) can be reused for Topological Sort — no need to run DFS twice. Topological Sort already includes all DFS outputs.
Runtime: O(n+m) — DFS/Topo Sort combined.


⚠️ Gotchas: Just run Topological Sort — it subsumes DFS. If the graph has a cycle, Topological Sort's result will be invalid. You can verify the output to detect this. Alternatively, use Kahn's algorithm — if the queue empties before all vertices are processed, a cycle exists.
Example: Check if a build dependency system is free of circular dependencies before computing a valid build order.


Pattern 29 — Detect Negative Cycle, Then Find Shortest Path or Report Infeasibility
Pipeline: Bellman-Ford (n iterations) → Check iter[n] vs iter[n-1] → Report or return dist[] | Runtime: O(nm)
Problem: Find shortest path from s to t on a graph that MAY have negative edges. If a negative cycle reachable from s exists, report that shortest path is undefined.
Why this works: A negative cycle makes shortest paths undefined — you can loop forever to decrease cost. Bellman-Ford detects this in the n-th iteration. If no negative cycle: dist[] from round n-1 is the correct answer.
Step-by-Step Solution Template:

Run Bellman-Ford from s. Execute n iterations (one extra beyond the standard n-1).
Compare iter[n][v] vs iter[n-1][v] for all vertices v.
If ANY iter[n][v] < iter[n-1][v]: a negative cycle reachable from s exists. Shortest path to those vertices is -∞. Report infeasibility.
If no change in round n: dist[t] = iter[n-1][t] = correct shortest path. Follow prev[] for the path.
Runtime: O(nm) — n iterations of Bellman-Ford.


⚠️ Gotchas: Only vertices v where iter[n][v] < iter[n-1][v], or vertices reachable FROM those vertices, have -∞ shortest paths. To find which vertices are downstream of the negative cycle, run DFS from all detected vertices in round n.
Example: Currency arbitrage detection: find the cheapest exchange path, or report that infinite profit is possible via a cyclic arbitrage.


Pattern 30 — All-Pairs Connectivity Check (Is Graph Strongly Connected?)
Pipeline: SCC → Check if |V_SCC| == 1 | Runtime: O(n+m)
Problem: Determine if every vertex in a directed graph can reach every other vertex (strongly connected).
Why this works: A directed graph is strongly connected if and only if it has exactly one SCC containing all n vertices. The SCC algorithm computes this directly.
Step-by-Step Solution Template:

Run Strongly Connected Components.
Check if |V_SCC| == 1 (only one metanode in the metagraph).
If yes: the graph is strongly connected — every vertex can reach every other.
If no: the number of SCCs is |V_SCC| > 1. Vertices in different SCCs cannot mutually reach each other.
Runtime: O(n+m).


⚠️ Gotchas: For UNDIRECTED graphs: just check if DFS from any vertex visits all n vertices — O(n+m) without needing SCC. If the graph is not strongly connected, the SCCs and G_SCC show exactly which groups are isolated from each other.
Example: Is this network resilient enough that any server can communicate with any other server?


Pattern 31 — Find All Vertices on Any Negative Cycle
Pipeline: Bellman-Ford (n iterations) → DFS from all updated vertices in round n | Runtime: O(nm)
Problem: Identify every vertex that lies on a negative-weight cycle (not just detect that one exists).
Why this works: Bellman-Ford's n-th iteration identifies vertices whose distance STILL decreases — these are directly affected by a negative cycle. But vertices reachable FROM those vertices also have -∞ shortest paths. DFS from all identified vertices finds all affected vertices.
Step-by-Step Solution Template:

Run Bellman-Ford for n iterations. Track which vertices v have iter[n][v] < iter[n-1][v] — call this set CYCLED.
Build a list of all vertices in CYCLED.
Run DFS from every vertex in CYCLED on the original directed graph.
All vertices visited by this DFS are "tainted" — they can reach or be reached from a negative cycle and have -∞ shortest path from s.
Runtime: O(nm) for Bellman-Ford + O(n+m) for DFS = O(nm).


⚠️ Gotchas: A vertex v may not be ON the cycle but still have -∞ shortest path if it is DOWNSTREAM from the cycle (reachable from the cycle). This is often followed by a modified shortest path that reports -∞ for tainted vertices.
Example: In a financial network, identify all assets whose value could be driven to -∞ by an arbitrage loop.


That's all 31 patterns across all 7 categories — complete. Sorry for cutting it short the first time.