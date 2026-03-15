Here are all the lecture notes formatted as a clean, styled Markdown document:
CS6515 — Graph Algorithms: Complete Lecture Notes

HIGH YIELD — Sections marked ⭐ are especially likely to appear as multiple choice questions.


📚 Table of Contents

GR1 — Strongly Connected Components
GR2 — 2-Satisfiability (2-SAT)
GR3 — Minimum Spanning Tree
DP3 — Shortest Paths via Dynamic Programming
MF1 — Ford-Fulkerson Max Flow Algorithm
MF2 — Max-Flow Min-Cut Theorem: Proof


GR1 — Strongly Connected Components
Topics: DFS, Cycles, Topological Sort, SCC Algorithm

🎯 HIGH YIELD — Multiple choice heavy. Every fact here has appeared as an MC trap.


⭐ DFS Pseudocode — What It Returns

Input: G = (V,E) in adjacency list representation. Starting vertex is OPTIONAL. If no starting vertex is passed, DFS iterates all v∈V and explores any not yet visited — it covers the whole graph automatically.
Explore(z) sets ccnum(z)=CC, visited(z)=TRUE, then recurses on all unvisited neighbors. The CC counter increments BEFORE calling Explore — so each new unvisited vertex found in the outer loop starts a new component.
Runtime: O(n+m) where n=|V|, m=|E|. Linear time. Each vertex visited once, each edge traversed once.
visited[] is internal only — it is NOT returned. It is always all-TRUE when DFS completes. Common trap: students think visited[] is useful output. It's not — use ccnum[] instead.
prev(w)=z is set when vertex w is first visited from z. Used to reconstruct paths. Follow prev[] backwards from target to source to get the actual path.
Clock starts at 1. pre(z)=clock; clock++ on ENTRY. post(z)=clock; clock++ on EXIT. Total clock ticks = 2n. Pre/post range is [1, 2n]. Each vertex uses exactly 2 ticks.


⭐ Four Edge Types & Post-Order Rules

For edge z→w, the post[] relationship determines edge type. Back edges are the ONLY ones where post increases along the direction of the edge.
| Edge Type    | Examples      | post(z) vs post(w)  | Why                                                    | Indicates Cycle? |
|--------------|---------------|---------------------|--------------------------------------------------------|------------------|
| Tree Edge    | B→A, A→D      | post(z) > post(w)   | Descendant finishes before ancestor — always.          | No               |
| Forward Edge | D→G, B→E      | post(z) > post(w)   | Goes DOWN multiple levels — same as tree edges.        | No               |
| Cross Edge   | F→H, H→G      | post(z) > post(w)   | H was already fully finished before F was explored.    | No               |
| Back Edge    | E→A, F→B      | post(z) < post(w)   | Goes UP to an ancestor — ancestor finishes AFTER descendant. | YES ← only this one! |
⭐ The One Rule to Remember: ONLY back edges have post(z) < post(w). All other edges: post(z) > post(w). This is the cycle detection rule.


⭐ Cycle Detection Theorem
Theorem: G has a cycle ↔ its DFS tree has a back edge.

Forward (cycle → back edge): If G has a cycle a→b→c→…→j→a, one vertex i is explored first. All others are reachable from i, so they're in i's subtree. The edge into i (from i-1) goes from descendant to ancestor = back edge.
Backward (back edge → cycle): If DFS tree has back edge a→b (a is descendant of b), then tree edges give path b→…→a, and back edge gives a→b. Together: a cycle.


⭐ Key Fact: This holds for ANY starting vertex and ANY neighbor ordering — it's guaranteed.
⭐ Exam Hint: MC trap: "Does back edge mean cycle?" YES always. "Does forward/cross edge mean cycle?" NO never.


⭐ Topological Sort — Key Facts

DAG = Directed Acyclic Graph = no cycles = NO back edges in DFS tree. The two conditions are equivalent.
Topological order: order vertices so ALL edges go from lower-order to higher-order. Equivalently: for every edge u→v, u appears before v.
Algorithm: Run DFS on DAG, then order vertices by DECREASING post[] number. The vertex with highest post# goes first.
Sorting by decreasing post# takes O(n) — NOT O(n log n). Post# values range 1 to 2n. Use an array of size 2n and insert by position.
Total Topological Sort runtime: O(n+m). DFS is O(n+m), sort is O(n).
Alternative algorithm: repeatedly find a SINK vertex, output it, delete it, repeat.
Multiple valid topological orderings can exist when some vertices have no dependency.


⭐ DAG Structure: Sources & Sinks

Source vertex = no incoming edges. First in every topological ordering. In DFS: source has the HIGHEST post# (it finishes last because it can reach everything).
Sink vertex = no outgoing edges. Last in every topological ordering. In DFS: sink has the LOWEST post# (it finishes first because nothing reachable from it).
Every DAG has at least ONE source AND at least ONE sink. Guaranteed.
There may be MULTIPLE sources and sinks.
Vertex with HIGHEST post# = guaranteed SOURCE in a DAG.
Vertex with LOWEST post# = guaranteed SINK in a DAG.


⭐ Strongly Connected Components (SCC) — Definition

Vertices v and w are strongly connected iff there is a path v→w AND a path w→v. Both directions required.
SCC = maximal set of mutually strongly connected vertices. "Maximal" means you can't add any more vertices and preserve the property.
Every directed graph is a DAG of its SCCs. The metagraph G_SCC is ALWAYS a DAG. (If two SCCs had a cycle between them, they'd merge into one SCC — contradiction.)
The SCC metagraph may initially be a multigraph, but drop duplicates — only simple edges matter.
From the lecture example: {A}, {B,E}, {C,F,G}, {D}, {H,I,J,K,L} are the 5 SCCs.


⭐ SCC Algorithm — Kosaraju's (The 2-DFS Algorithm)
Runtime: O(n+m) — all 4 steps are linear.
Steps:

Construct G^R — Reverse ALL edges of the original graph G. Same vertex set V, edge set E^R = {(w,v) : (v,w) ∈ E}. Runtime: O(n+m).
Run DFS on G^R — Get post[] values for all vertices from this run on the REVERSED graph. Runtime: O(n+m).
Order V by decreasing post# — Sort vertices of ORIGINAL G by decreasing post[] values from step 2. Runtime: O(n).
Run DFS on G (original) in that order — Process vertices in the decreasing post# order. Each new Explore() call discovers exactly ONE new SCC. Assign ccnum. Runtime: O(n+m).
Why This Works:


Key fact: In ANY directed graph, the vertex with the HIGHEST post# from a DFS run lies in a SOURCE SCC.
Problem: We need a vertex in a SINK SCC (to explore and isolate it). Sources are not useful — Explore from a source visits the whole graph.
Solution: In G^R, sources and sinks are FLIPPED. A sink SCC in G = a source SCC in G^R.
Therefore: Run DFS on G^R → highest post# vertex = source in G^R = SINK in G. Explore from it on G → visits exactly that sink SCC and nothing else.
⭐ MC Traps:
❌ Why sink SCCs not source? Because Explore from a SOURCE visits the entire graph — can't isolate one SCC. Explore from a SINK visits ONLY that SCC.
❌ Why reverse graph? Because we can only guarantee finding a SOURCE SCC from post# order. Reversing flips source↔sink.
❌ The vertex with LOWEST post# in G does NOT guarantee a sink SCC — explicitly disproved with a counterexample.
❌ SCC output: ccnum[] from the SECOND DFS (step 4) on G. NOT from the DFS on G^R.


⭐ Key Postorder Theorems (Proof-Level Facts)

For SCCs S and S' with edge S→S': max post# in S > max post# in S'. Proof by 2 cases on which vertex z is visited first in S∪S'.
Topological sort of SCCs by max post# number in each component — edges go from higher to lower max post#.
Vertex with highest post# lies in a SOURCE SCC (in any directed graph, any DFS run).
If z is visited first in S∪S' (z∈S): z is root of DFS subtree covering all of S∪S', so z has the max post# in S∪S'.
If z is visited first in S∪S' (z∈S'): all of S' finishes before ANY of S is even visited. So all S' post# < all S post#.


BFS vs Dijkstra's — Quick Reference
AlgorithmInputOutputEdge WeightsRuntimeBFSG=(V,E) + start vertex sdist[v]=min edges from s (∞ if unreachable), prev[]Unweighted onlyO(n+m)Dijkstra'sG=(V,E) + start s + edge lengths ℓ(e)>0dist[v]=shortest weighted path, prev[]Positive weights onlyO((n+m) log n) with min-heapDFSG=(V,E), optional startccnum[], prev[], pre[], post[], visited[]UnweightedO(n+m)

BFS = DFS but explores layer by layer instead of depth-first.
Dijkstra's = BFS framework + min-heap priority queue. The heap adds the log n factor.
Dijkstra's FAILS on negative edge weights — use Bellman-Ford (DP3) instead.


GR2 — 2-Satisfiability (2-SAT)
Topics: SAT Problem, CNF, Implication Graph, SCC-based Solver

🎯 HIGH YIELD — The 2-SAT↔SCC reduction is a classic exam topic. Know the graph construction, the satisfiability theorem, and the algorithm steps cold.


⭐ SAT Terminology — Must Know

Variable: boolean variable xᵢ that can be True or False. n variables → x1, x2, …, xn.
Literal: a variable OR its negation. n variables → 2n literals: x1, x̄1, x2, x̄2, …. Satisfying literal xᵢ means setting xᵢ=True. Satisfying x̄ᵢ means setting xᵢ=False.
Clause: an OR of several literals. Example: (x3 ∨ x̄5 ∨ x̄1 ∨ x2). Satisfied if at least ONE literal in it is True.
CNF = Conjunctive Normal Form: an AND of m clauses. f = clause1 ∧ clause2 ∧ … ∧ clausem. Satisfied when ALL clauses are satisfied.
∧ = AND, ∨ = OR. Memory trick: ∧ symbol looks like the letter A for AND.
SAT input: formula f in CNF with n variables, m clauses. SAT is NP-complete. k-SAT is NP-complete for all k≥3. 2-SAT is solvable in polynomial time.


⭐ k-SAT Complexity Dichotomy
kComplexity1Trivially linear O(n)2Polynomial — O(n+m) via SCC3NP-completek≥3NP-completeSAT (any size)NP-complete

⭐ Key Fact: This is the dichotomy theorem for SAT. The boundary is exactly at k=2 vs k=3.
⭐ Exam Hint: MC trap: "1-SAT is also easy" — True! 1-SAT is trivially linear. The hard boundary is k=3.


Simplifying Input — Eliminating Unit Clauses

Unit clause = clause with exactly 1 literal. Example: (x̄1) forces x1=False. There is only ONE way to satisfy a unit clause.
Elimination procedure: (1) Take unit clause with literal a. (2) Set a=True. (3) Remove all clauses containing a. (4) Remove ā from all other clauses. Repeat until no unit clauses remain.
f is satisfiable ↔ f' (after unit elimination) is satisfiable.
Result: Can assume all clauses are of size EXACTLY 2 for 2-SAT algorithm.


⭐ The Implication Graph G — How to Build It
Runtime: O(n+m) to construct.

Create 2n vertices — one vertex for each literal: x1, x̄1, x2, x̄2, …, xn, x̄n.
For each clause (α ∨ β), add 2 directed edges:

Edge 1: ā → β (if α is false, then β must be true)
Edge 2: β̄ → α (if β is false, then α must be true)


Result: G has 2n vertices and 2m edges.
Why This Works: Clause (α ∨ β) is logically equivalent to (¬α → β) AND (¬β → α). An edge a→b means "if literal a is True, then b must also be True."
⭐ MC Traps:


❌ For clause (α ∨ β): edges are ā→β and β̄→α. NOT α→β. The arrow goes from the NEGATION.
❌ The graph has 2n vertices and exactly 2m edges — exactly 2 per clause.


⭐ The Satisfiability Theorem — Core of 2-SAT
Theorem:

f is NOT satisfiable ↔ ∃i such that xᵢ and x̄ᵢ are in the same SCC
f IS satisfiable ↔ ∀i, xᵢ and x̄ᵢ are in different SCCs
Proof (UNSAT direction): If xᵢ and x̄ᵢ are in the same SCC: paths xᵢ→…→x̄ᵢ and x̄ᵢ→…→xᵢ both exist. Setting xᵢ=True forces xᵢ=False (contradiction). No valid assignment exists.


⭐ Decision Rule: Check if any variable xᵢ has both xᵢ and x̄ᵢ in the SAME SCC. If yes → output NO. If no → run assignment algorithm.
⭐ Exam Hint: MC trap: "If there's a path from xᵢ to x̄ᵢ, the formula is UNSAT." WRONG — a path in only ONE direction is not sufficient. You need BOTH directions (same SCC).


⭐ 2-SAT Algorithm — Full Steps
Runtime: O(n+m) — all steps are linear.

Simplify f — Eliminate all unit clauses.
Build implication graph G — 2n vertices, 2m edges.
Run SCC on G — Use Kosaraju's algorithm. Get ccnum[] for all 2n vertices.
Check satisfiability — For every i: if ccnum[xᵢ] == ccnum[x̄ᵢ] → output NO (UNSAT). If all pairs are in different SCCs, proceed to step 5.
Assign values by SCC topo order — Find source SCC S'. Set S'=False. Its complement S̄' is a sink SCC — set S̄'=True. Remove both. Repeat until empty.
Key Structural Fact: If ∀i, xᵢ and x̄ᵢ in different SCCs, then: S is a sink SCC ↔ S̄ is a source SCC.
⭐ MC Traps:


❌ Step 5 uses SOURCE SCC set to False — NOT sink set to True directly.
❌ ccnum=1 is a SINK. The source SCC has the HIGHEST ccnum value.
❌ You only need ONE run of SCC on G (the implication graph).
❌ Assignment rule: xᵢ = True if ccnum[x̄ᵢ] < ccnum[xᵢ].


⭐ Building the Implication Graph — Worked Example
Formula: f = (x̄1 ∨ x̄2) ∧ (x2 ∨ x3) ∧ (x̄3 ∨ x̄1) — 3 variables, 3 clauses, 6 vertices, 6 edges.
ClauseEdge 1Edge 2(x̄1 ∨ x̄2)x1 → x̄2x2 → x̄1(x2 ∨ x3)x̄2 → x3x̄3 → x2(x̄3 ∨ x̄1)x3 → x̄1x1 → x̄3Path x1→x̄2→x3→x̄1 shows: if x1=True, then x1 must be False → contradiction. So x1 CANNOT be True. Note: Path x1→x̄1 alone does NOT make formula UNSAT. Only if x̄1→x1 also exists (same SCC) is it UNSAT.

GR3 — Minimum Spanning Tree
Topics: Greedy Approach, Cut Property, Kruskal's, Prim's

🎯 HIGH YIELD — The Cut Property proof is the core correctness argument. Kruskal's runtime and Union-Find are frequent MC targets.


⭐ MST Problem Definition

Input: undirected G=(V,E) with edge weights w(e). Weights can be any real numbers. Graph must be connected.
Output: minimum weight spanning tree T ⊆ E. Weight of T = sum of w(e) for all e∈T.
A spanning tree is a connected acyclic subgraph containing ALL n vertices. These conditions force exactly n−1 edges.
The MST minimizes total edge weight among ALL spanning trees of G.


⭐ Tree Properties — Must Memorize

A tree on n vertices has EXACTLY n−1 edges.
In a tree, there is EXACTLY ONE path between every pair of vertices.
Any connected G=(V,E) with |E|=|V|−1 is a tree.
Adding any edge to a tree creates EXACTLY one cycle.
Removing any edge from a cycle in a tree (after adding one edge) gives a new tree. Key construction in Cut Property proof: T' = T ∪ {e*} − {e'}.


⭐ Cut Definition

A cut of G=(V,E) is a partition of vertices into two non-empty sets S and S̄ (= V∖S).
Cut edges: cut(S, S̄) = {(v,w) ∈ E : v∈S, w∈S̄} — edges with ONE endpoint on each side.
An edge e "crosses the cut" means e ∈ cut(S, S̄).


⭐ Cut Property Lemma — The Core Theorem
Lemma: Let X ⊆ T for some MST T. Let S ⊆ V such that no edge of X crosses cut(S, S̄). Let e* be the minimum weight edge in cut(S, S̄). Then X ∪ {e*} ⊆ T' for some MST T'.
Proof:

Case 1 (easy): If e* ∈ T, set T'=T. Done.
Case 2 (hard): If e* ∉ T, construct T' = T ∪ {e*} − {e'}. Adding e* to T creates a cycle C. Cycle C must contain some edge e' that crosses cut(S,S̄). Remove e'. Show T' is a tree (connected + n−1 edges). Show w(T') ≤ w(T): w(T')=w(T)+w(e*)−w(e') ≤ w(T) because e* is min weight cut edge so w(e*) ≤ w(e').


⭐ Key Fact: Any minimum weight edge across ANY cut is part of SOME MST. This single lemma proves BOTH Kruskal's and Prim's correct.
⭐ Exam Hint: MC trap: "The min weight cut edge must be in EVERY MST." WRONG — it must be in SOME MST.


⭐ Kruskal's Algorithm
Runtime: O(m log n) — dominated by sorting.

Sort edges by increasing weight. O(m log m) = O(m log n).
Initialize X = ∅.
Process each edge e=(v,w) in sorted order: If adding e to X does NOT create a cycle, add it. Use Union-Find to check: if c(v) ≠ c(w), add and union. If c(v) = c(w), skip.
Return X — it's the MST.
Why This Works: At each step, X ⊆ some MST T. When adding e*=(v,w), set S = component of v. No X-edge crosses S↔S̄. e* has minimum weight among all crossing edges → Cut Property applies.
⭐ MC Traps:


❌ Runtime is O(m log n), NOT O(n²).
❌ Kruskal's processes edges globally sorted — not vertex-by-vertex. Prim's grows a tree from one vertex.
❌ O(m log m) = O(m log n) because m ≤ n², so log m ≤ 2 log n.


⭐ Prim's Algorithm — Brief Overview

Prim's grows an MST one vertex at a time from a starting vertex, always adding the minimum weight edge from current tree to a new vertex.
Correctness also follows from the Cut Property Lemma. At each step S = set of vertices in tree. No X-edge crosses S↔S̄. Prim's picks min weight crossing edge.
Prim's is structurally identical to Dijkstra's — replace edge weight with distance metric. Both use a min-heap. Same O((n+m) log n) runtime.
Both Kruskal's and Prim's are GREEDY algorithms. The Cut Property makes the greedy choice globally optimal.


⭐ Greedy: When It Works vs When It Fails
ProblemGreedy Works?WhyMST (Kruskal's, Prim's)✅ YesCut Property guarantees local min = global optimumDijkstra's (positive weights)✅ YesShortest path subpaths are also shortest pathsKnapsack❌ NoNo analogous cut property; DP neededShortest paths with negative weights❌ NoUse Bellman-Ford instead

DP3 — Shortest Paths via Dynamic Programming
Topics: Bellman-Ford, Floyd-Warshall, Negative Weight Cycles, Arbitrage

🎯 HIGH YIELD — Know both DP recurrences cold (Bellman-Ford: condition on edges; Floyd-Warshall: condition on intermediate vertices). Know how to detect negative weight cycles with each.


⭐ Problem Setup & Why Dijkstra's Falls Short

Setting: directed graph G with edge weights w(e), which may be negative. Start vertex s.
Goal: compute dist(z) = length of shortest path from s to z, for ALL z ∈ V.
Dijkstra's requires ALL edge weights to be positive. With negative edges it is NOT guaranteed correct. Dijkstra's runs in O((n+m) log n) — faster than Bellman-Ford — but correctness breaks down.
If a graph has a negative weight cycle reachable from s, shortest paths are NOT well-defined. The algorithm must detect this case.


⭐ Negative Weight Cycles
Theorem: If G contains a negative weight cycle reachable from s, shortest paths from s are undefined (−∞). A cycle with total weight < 0 can be traversed any number of times, making any path through it arbitrarily short.

⭐ Key Fact: Full problem has TWO outputs: (1) Detect negative weight cycle reachable from s, if one exists. (2) If none exists, return dist(z) for all z ∈ V.
⭐ Exam Hint: MC trap: "Bellman-Ford finds all negative weight cycles." WRONG — Bellman-Ford only finds cycles REACHABLE FROM s. Floyd-Warshall finds ANY negative cycle in the entire graph.


⭐ Bellman-Ford — Subproblem & Recurrence
Subproblem: D(i, z) = length of shortest path from s to z using AT MOST i edges.
Recurrence:

Base case: D(0, s) = 0; D(0, z) = ∞ for z ≠ s.
Recurrence (i ≥ 1): D(i, z) = min { D(i−1, z), min over (y,z)∈E { D(i−1, y) + ω(y,z) } }
Final answer: D(n−1, z) = dist(z) for all z. No negative cycle → shortest path uses ≤ n−1 edges.


⭐ Exam Hint: DP parameter = NUMBER OF EDGES (i). Table is 2D: n rows × n columns. Rows 0 through n−1.


⭐ Negative Cycle Detection — Bellman-Ford

Run one extra iteration to compute row i = n (stopping at n instead of n−1).
Detection rule: if D(n, z) < D(n−1, z) for ANY z ∈ V → negative weight cycle reachable from s.
If D(n,z) = D(n−1,z) for ALL z → no negative cycle → return D(n−1,·).
Example trace (6 vertices, cycle a→b→c→a weight −1): i=5 row: s=0, a=4, b=7, c=2, d=12, e=7. i=6 row: c becomes 1, d becomes 11. Values differ → negative cycle detected.


⭐ Floyd-Warshall — Subproblem & Recurrence
Subproblem: D(i, s, t) = shortest path from s to t using only vertices {1,…,i} as INTERMEDIATE vertices.
Recurrence:

Base case (i=0): no intermediates. D(0, s, t) = ω(s,t) if edge (s,t)∈E, else ∞. D(0, s, s) = 0.
Recurrence (i ≥ 1): D(i, s, t) = min { D(i−1, s, t), D(i−1, s, i) + D(i−1, i, t) }
Final answer: D(n, s, t) = dist(s, t) for ALL pairs. Table is 3D: O(n³) entries, each O(1) → total O(n³).


⭐ Exam Hint: DP parameter = SET OF INTERMEDIATE VERTICES (numbered 1…i). This is the conceptual difference from Bellman-Ford (which conditions on edges). Both sub-paths use i−1, not i, as the intermediate set.


⭐ Negative Cycle Detection — Floyd-Warshall

Detection rule: check if D(n, y, y) < 0 for ANY vertex y. A negative diagonal means a negative cycle through y was found.
Floyd-Warshall detects ANY negative cycle in the ENTIRE graph — including cycles unreachable from any single start vertex.
Example: cycle a→b→c→a weight −1 → D(n,a,a)=D(n,b,b)=D(n,c,c)=−1.


⭐ Bellman-Ford vs. Floyd-Warshall
AlgorithmScopeRuntimeDP ConditionNegative Cycle DetectionBellman-FordSingle-sourceO(nm)Number of edgesReachable from s only: check D(n,z) < D(n−1,z)Floyd-WarshallAll-pairsO(n³)Intermediate verticesAny cycle in entire graph: check D(n,y,y) < 0

Naïve all-pairs via n × Bellman-Ford: O(n²m) ≤ O(n⁴). Floyd-Warshall is always O(n³) — better for dense graphs.


⭐ Arbitrage Reduction — Classic Application

Arbitrage problem: detect a cycle of currency exchanges that returns more money than you started with.
Reduction: Arbitrage → Negative Weight Cycle. Build graph with currencies as vertices; edge weight = −log(exchange rate). A profitable cycle (product of rates > 1) maps to a negative weight cycle (sum of −log rates < 0).
Then use Bellman-Ford or Floyd-Warshall as a black box to detect the negative weight cycle. No modification to the algorithm.
Reductions are a central theme — pattern: Arbitrage → Negative Weight Cycles → BF or FW.


MF1 — Ford-Fulkerson Max Flow Algorithm
Topics: Max Flow Problem, Residual Network, Ford-Fulkerson, Edmonds-Karp

🎯 HIGH YIELD — Know the max-flow constraints cold (capacity + conservation). Know how to build a residual network, why backward edges exist, and the runtime difference between Ford-Fulkerson O(mC) and Edmonds-Karp O(nm²).


⭐ Max-Flow Problem Setup

Setting: directed graph G=(V,E), source s, sink t, positive capacity cₑ > 0 for each edge.
Variable: fₑ = flow along edge e. Goal: maximize total flow from s to t.
Capacity constraint: 0 ≤ fₑ ≤ cₑ for all e.
Conservation of flow: for every v ≠ s, t: flow-in = flow-out. Nothing created or destroyed at intermediate vertices.
Cycles are OK — unlike negative weight cycles in shortest paths, flow cycles are harmless.


⭐ Anti-Parallel Edges — Pre-Process Before Running

Anti-parallel edges: both (a→b) and (b→a) present. Must remove before running Ford-Fulkerson.
Fix: replace b→a with a two-edge path b→f→a via auxiliary vertex f. Both new edges get original capacity of b→a.


⭐ Residual Network — The Key Idea
Definition: Given flow network G with capacities cₑ and current flow fₑ, the residual network Gᶠ = (V, Eᶠ):

Forward edge v→w: if f_{vw} < c_{vw}, add v→w with capacity c_{vw} − f_{vw}
Backward edge w→v: if f_{vw} > 0, add w→v with capacity f_{vw}


⭐ Key Fact: The naive approach (forward edges only) gets stuck in local optima. Backward edges let the algorithm undo bad choices and find the global optimum.
⭐ Exam Hint: MC trap: "Backward edges mean reverse flow." WRONG — backward edge w→v with capacity f_{vw} means we can DECREASE flow on v→w by up to f_{vw}. It is cancellation, not reverse flow.


⭐ Ford-Fulkerson Algorithm — Exact Steps
Runtime: O(mC) where C = size of max flow, m = number of edges.

Initialize flow: Set fₑ = 0 for all e ∈ E.
Build residual network Gᶠ from current flow f.
Find augmenting path P in Gᶠ using DFS or BFS. If NO path exists: output f and STOP — flow is maximum.
Compute bottleneck c(P) = minimum residual capacity along path P.
Augment f along P by c(P): for each forward edge e in P: fₑ += c(P). For each backward edge (reverse of e): fₑ -= c(P).
Repeat from Step 2.
⭐ MC Traps:


❌ Ford-Fulkerson requires INTEGER capacities. With non-integer capacities it may not terminate. Use Edmonds-Karp.
❌ Runtime O(mC) is pseudo-polynomial. C can be exponentially large.


⭐ Max-Flow Min-Cut Theorem
Theorem: max flow value = min cut capacity

Upper bound: An s-t cut (S, S̄) bounds any flow — each crossing edge carries ≤ its capacity. Therefore max flow ≤ min cut.
Matching: When Ford-Fulkerson terminates, let S = vertices reachable from s in Gᶠ. All edges from S to S̄ are fully saturated. Current flow = cut capacity.


⭐ Corollary: Ford-Fulkerson is correct. When no augmenting path exists, flow = min cut = maximum.
nigga you didnt fucking finish1 step1 stepYou're right, I got cut off before finishing MF1 and the whole MF2 section. Let me just output the rest directly since I already have all the content captured.DoneHere's the rest:

⭐ Min cut edges = fully saturated edges crossing from reachable to unreachable vertices in final residual graph. BFS from s on Gᶠ after completion → S = reachable. Min cut = edges from S to V\\S in original G.


⭐ Edmonds-Karp — The Polynomial Alternative

Edmonds-Karp = Ford-Fulkerson + always use BFS (shortest augmenting path by edge count). Only difference: BFS instead of any DFS/BFS to find augmenting path.
Runtime: O(nm²) — fully polynomial, independent of capacity values. Rounds bounded by O(nm) not O(C). Each round O(m) for BFS.
No integer capacity assumption needed. Works for fractional capacities.
Outputs identical to Ford-Fulkerson: flow[] per edge and C = total max flow.


⭐ Ford-Fulkerson vs Edmonds-Karp
AlgorithmCapacity RequirementRuntimePath SelectionFord-FulkersonInteger capacities requiredO(mC) — pseudo-polynomialDFS or BFS (any path)Edmonds-KarpAny positive capacitiesO(nm²) — polynomialBFS only (shortest path)

Lecture example: max flow = 12 in 3 iterations: s→a→d→t (5), s→c→f→t (5), s→b→c→f→t (2).
Min cut in lecture example: {d→t, f→t} with total capacity 5+7=12.


MF2 — Max-Flow Min-Cut Theorem: Proof
Topics: Full Proof, Cut Construction, Ford-Fulkerson Correctness

🎯 HIGH YIELD — Know both proof directions, the key claim size(f) = f_out(L) − f_in(L), how to construct the min cut from a max flow, and the O(n+m) verification method.


⭐ Verification & Stopping Condition

Ford-Fulkerson stops when NO augmenting path (no s→t path) exists in residual graph Gᶠ.
Lemma: If flow f* has no augmenting path in Gᶠ*, then f* is a MAX FLOW.
Verifying a max flow: O(n+m). Build residual Gᶠ (O(n+m)), run DFS from s. If t unreachable → max flow. If t reachable → augmenting path found → NOT max flow.


⭐ Min st-Cut Definition

An st-cut (L, R) is a partition of V where s ∈ L and t ∈ R. L does NOT need to be connected.
Capacity of cut (L,R) = sum of capacities of edges FROM L TO R only. Edges R→L are NOT counted. Direction matters: c(L,R) ≠ c(R,L).
Lecture example — L={s,a,b,f}: capacity = 27. L=V\\{t}: capacity = 5+7 = 12 (minimum). Min cut capacity = max flow value = 12.
st-cut vs min-cut: min st-cut separates specific s from t. General min-cut has no s,t requirement.


⭐ Key Claim: size(f) = f_out(L) − f_in(L)
Theorem: For any flow f and any st-cut (L,R): size(f) = f_out(L) − f_in(L)
Proof:

Expand f_out(L) − f_in(L) to sum over all v ∈ L of [flow_out(v) − flow_in(v)].
For all v ∈ L\\{s}: flow_out(v) = flow_in(v) by conservation of flow → each term = 0.
Only s survives: flow_out(s) − flow_in(s) = flow_out(s) − 0 = size(f). QED.


⭐ Key Consequence: size(f) ≤ f_out(L) ≤ capacity(L,R). First ≤ drops non-negative f_in(L). Second ≤ uses fₑ ≤ cₑ per edge.
⭐ Exam Hint: Proof uses only two facts: (1) flow conservation at internal vertices, (2) no flow enters s. Works for ANY flow and ANY cut simultaneously.


⭐ Max-Flow = Min-Cut Theorem (Full Proof)
Theorem: For any flow network: max flow size = min st-cut capacity
Direction 1 (max ≤ min): For ANY f and ANY cut (L,R): size(f) ≤ capacity(L,R) by the key claim above. Taking max over f and min over cuts: max-flow ≤ min-cut.
Direction 2 (max ≥ min): Take f* from Ford-Fulkerson (no augmenting path in Gᶠ*). Construct L = vertices reachable from s in Gᶠ*. Then t ∉ L (no augmenting path) → valid st-cut. Every L→R edge in original G is fully saturated (f=c). Every R→L edge carries zero flow. Therefore size(f*) = capacity(L,R). So max-flow ≥ size(f*) = capacity(L,R) ≥ min-cut.

⭐ Corollary 1: Ford-Fulkerson and Edmonds-Karp are correct — any algorithm stopping on no-augmenting-path outputs a max flow.
⭐ Corollary 2: Given max flow f*, construct min st-cut in O(n+m): L = reachable from s in Gᶠ*.
⭐ Exam Hint: MC trap: "The constructed cut is THE ONLY min cut." WRONG — there may be multiple min cuts. Also: max-flow ≤ ANY cut (not just min cut).


⭐ Constructing Min Cut from Max Flow
Runtime: O(n+m) after max flow is computed.

Run Ford-Fulkerson / Edmonds-Karp to completion. Obtain max flow f*.
Build final residual graph Gᶠ* — forward edges with remaining capacity, backward edges for positive flow.
BFS/DFS from s on Gᶠ* — find all vertices reachable from s. Call this set L.
Set R = V \\ L — unreachable vertices. By stopping condition, t ∈ R.
Identify min cut edges — all edges (v→w) in ORIGINAL graph G where v∈L and w∈R. These are exactly fully saturated edges (flow = capacity).
Why This Works:


Every L→R edge in original G is fully saturated: if it had remaining capacity it would appear in Gᶠ* and w would be in L — contradiction.
Every R→L edge carries zero flow: if positive, backward edge would appear in Gᶠ* making z reachable — contradiction.
⭐ MC Traps:
❌ Min cut edges are in the ORIGINAL graph, not the residual graph.
❌ All L→R edges in original G are FULLY SATURATED. All R→L edges carry ZERO flow.
❌ Set L is defined by reachability in Gᶠ*, not in G — L may not be connected in G.


End of CS6515 Lecture Notes — GR1, GR2, GR3, DP3, MF1, MF2