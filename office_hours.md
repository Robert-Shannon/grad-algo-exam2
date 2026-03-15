Here is the full content from all four Office Hours sessions, formatted in Markdown:

---

# 📓 Office Hours — Session Notes

---

## Session 1 — 02/22/2026
### Intro to Graphs: Adjacency Lists, Metagraphs & Operations
**Instructor:** TA Tim West

First graphs session. No homework out yet (HW released next day). Focus: foundational graph representations, adjacency list mechanics, metagraph definition, and what operations cost O(n+m). Black box algorithms deferred to future sessions.

---

### 📐 A Graph IS an Adjacency List — Not Two Separate Sets
**Type:** Theorem

**Statement:** G = (V, E) looks like two sets, but it is **ALWAYS** represented as **ONE combined adjacency list**. V and E are not stored separately.

> G = (V, E) → single adjacency list (array of linked lists)

Students often see the (V, E) notation and think V and E are separate data structures. They are not. The adjacency list encodes both vertices AND their edges in one unified structure. Each index in the array = a vertex. Each linked list at that index = the neighbors of that vertex.

> ⭐ **Exam Hint from Prof:** "If asked 'what is the data structure for a graph?' — the answer is an adjacency list, not 'a set of vertices and a set of edges.'"

- Could also be an adjacency matrix — same principle applies. One structure encodes both V and E.
- Tim's exact words: *"It looks like two sets, but it's always represented as one combined adjacency list."*

---

### ⚠️ Simple Graph Rules — What Is and Is NOT Allowed
**Type:** Mistakes

| Rule | Detail |
|---|---|
| ✗ Self-edges: edge from A to A | ✓ NOT allowed in simple graphs. All adjacency lists in this course are simple — no self-edges ever. |
| ✗ Multi-edges: two edges between the same pair of vertices in the same direction | ✓ NOT allowed in simple graphs. Duplicate edges (same u→v twice) are invalid. |
| ✗ Thinking opposing directed edges (u→v AND v→u) are multi-edges | ✓ **ALLOWED.** Having both u→v and v→u in a directed graph is valid — they are two different edges in opposite directions. This does NOT violate simple graph rules. |

---

### 📐 Undirected vs Directed: How Edges Are Encoded
**Type:** Theorem

**Statement:** Undirected edges are encoded **TWICE** in the adjacency list. Directed edges are encoded **ONCE** (outgoing only).

> `Undirected: edge {u,v} → entry in adj[u] AND entry in adj[v]`
> `Directed: edge u→v → entry in adj[u] ONLY`

In an undirected graph, edge AB appears in both A's linked list and B's linked list. This doubles the storage for edges. In a directed graph, only OUTGOING edges are stored — so u→v appears only in adj[u], not in adj[v]. This makes finding INCOMING edges harder in directed graphs.

> ⭐ **Exam Hint from Prof:** "MC trap: 'How many times does edge (A,B) appear in an undirected adjacency list?' — Answer: 2. In a directed adjacency list for A→B: 1 (only in A's list)."

- Consequence: For undirected G with m edges, the adjacency list has **2m** total entries.
- For directed G with m edges, the adjacency list has exactly **m** total entries.
- Finding all INCOMING edges of v in a directed graph requires scanning all other vertices' lists — O(n+m). This is why "looking backwards" is expensive and why we build G^R for SCC.
- Tim: *"The implications of outgoing edge encoding is going to be really important when we think about some of the definitions that require us to look at the outgoing edges only."*

---

### 📐 What Is a Metagraph?
**Type:** Theorem

**Statement:** The metagraph G_SCC is just **ANOTHER adjacency list**. It follows all the same simple graph rules (no self-edges, no multi-edges) and is **ALWAYS a DAG**.

> G_SCC = (V_SCC, E_SCC) → simple adjacency list + always a DAG

Students sometimes think the metagraph is a special structure. It isn't — it's just an adjacency list where each "vertex" in the array represents one SCC. Tim replaced vertex names with SCC numbers (1, 2, 3...) to emphasize that the context of which original vertices are in each SCC is tracked separately via `ccnum[]`, not by the metagraph structure itself.

> ⭐ **Exam Hint from Prof:** "The metagraph is ALWAYS a DAG — no cycles allowed. This is guaranteed by the definition of SCCs (if two SCCs had a cycle between them, they'd merge into one)."

- To find which original vertices belong to SCC metanode k: look up all v where `ccnum[v] == k`.
- The metagraph MAY initially have multi-edges between metanodes. Drop duplicates — only simple edges matter.
- Tim: *"What I want to emphasize is that we know the components from the connected component numbers. We don't get to keep the context of which exact vertices are in that meta-vertex just from observing it."*

---

### 🔄 Runtime of Common Graph Operations
**Type:** Facts

| # | Operation | Runtime | Notes |
|---|---|---|---|
| 1 | Build adjacency list from edge list | O(n+m) | Iterate over all n vertices and m edges once. |
| 2 | Reverse a directed graph G to get G^R | O(n+m) | Iterate over all edges and flip direction. |
| 3 | Build subgraph (remove/keep specific vertices or edges) | O(n+m) | Scan the adjacency list once and copy only relevant entries. |
| 4 | Convert MST edge list to adjacency list | O(n+m) | MST has n-1 edges. Building its adjacency list is O(n+m). |
| 5 | Look up `ccnum[v]` for a specific vertex v | O(1) | `ccnum[]` is an array indexed by vertex — direct lookup. |
| 6 | Find all vertices in SCC k (given `ccnum[]`) | O(n) | Scan all n vertices and collect those where `ccnum[v] == k`. |
| 7 | Find all INCOMING edges to vertex v in a directed graph | O(n+m) | Must scan all adjacency lists since only outgoing edges are stored. |

---

### 📊 Session Roadmap — What's Coming When
**Type:** Strategy

- **TODAY (02/22):** Graph basics only — adjacency lists, metagraphs, common operations. NO black boxes yet.
- **NEXT SESSION (03/01):** Black box deep-dive — DFS, BFS, SCC, Topo Sort use cases and output details.
- **FUTURE:** 2-SAT and Boolean formulas — how satisfiability maps to an SCC problem on an implication graph.
- **FUTURE:** Testing graph algorithms — strategies for verifying solutions on sparse, dense, DAG, cyclic, disconnected graphs.
- **FUTURE:** MST and Max Flow covered in dedicated future sessions.
- **HW note:** First graphs homework released the day after this session (02/23). Practice problems released simultaneously.

---

### 💡 Properties to Memorize from This Session

| Property | Detail | Exam? |
|---|---|---|
| G = (V,E) is ONE adjacency list, not two separate structures | Never say "store V separately from E." It's always one unified adjacency list. | ⭐ LIKELY ON EXAM |
| Undirected edge (u,v): stored TWICE — in adj[u] AND adj[v] | Undirected graph with m edges has **2m** total adjacency list entries. | ⭐ LIKELY ON EXAM |
| Directed edge u→v: stored ONCE — in adj[u] only (outgoing only) | Finding incoming edges requires scanning all lists — O(n+m). | ⭐ LIKELY ON EXAM |
| Opposing directed edges (u→v AND v→u) are ALLOWED in simple graphs | This is NOT a multi-edge. Only same-direction duplicates are forbidden. | ⭐ LIKELY ON EXAM |
| G_SCC metagraph is always a DAG + always a simple adjacency list | Drop duplicate meta-edges. G_SCC has no self-edges and no cycles. | ⭐ LIKELY ON EXAM |
| `ccnum[v]` maps original vertex to its SCC metanode: O(1) lookup | To find which vertices are in SCC k: scan all v where `ccnum[v]==k`, O(n). | ⭐ LIKELY ON EXAM |
| All basic graph transformations (reverse, subgraph, copy, MST→adj list): O(n+m) | These are never the bottleneck. If a step is "build G^R", count it as O(n+m). | ⭐ LIKELY ON EXAM |

---
---

## Session 2 — 03/01/2026
### DFS Behavior, CC NUM Semantics & Graph Manipulation
**Instructors:** TA Tim West (+ TAs Jamie McPeek, Aaron Miller)

TA-led office hours session. Topics: graph properties deep-dive, black box algorithm call-outs, 2-SAT / Boolean formulas, and testing strategies. Homework 5 wrapping up; MST problem coming next week. Max Flow and MST to be covered in future sessions.

---

### 📐 CC NUM: Semantics Differ by Graph Type
**Type:** Theorem

**Statement:** `ccnum[u] == ccnum[v]` means different things depending on whether the graph is directed or undirected.

> `Undirected: ccnum[u]==ccnum[v] ↔ u and v are mutually reachable`
> `Directed: ccnum[u]==ccnum[v] ↔ u and v are in the same STRONGLY connected component`

The `ccnum[]` array is indexed by vertex and returns the component number in O(1). For undirected graphs, same ccnum = connected (can reach each other in either direction). For directed graphs, same ccnum = strongly connected (mutual reachability — both u→v AND v→u exist). On directed graphs, DO NOT use `ccnum[]` for general reachability — use SCC instead.

> ⭐ **Exam Hint from Prof:** "Trap question: 'Are u and v in the same connected component in this directed graph?' — `ccnum[]` only tells you they are in the same SCC, not that a one-directional path exists between them."

- `ccnum[]` lookup is O(1) — index into the array directly.
- On directed graphs, `ccnum[]` from plain DFS is unreliable for connectivity. The SCC algorithm's `ccnum[]` output is what correctly identifies strongly connected groups.
- Key distinction that Tim explicitly called out as a common confusion point.

---

### 📐 DFS Starting Vertex — When It Matters and When It Doesn't
**Type:** Theorem

**Statement:** The starting vertex for DFS is **OPTIONAL**. Only pass one in when the specific starting point changes your answer. If you only need `ccnum[]`, skip it — the result is identical regardless of starting vertex.

> `ccnum[] output is invariant to DFS starting vertex choice`

DFS without a starting vertex runs from all vertices automatically. If your goal is connected component numbers (`ccnum[]`), the starting vertex is irrelevant — all components will be discovered and numbered regardless. If your goal is a specific s→t path or pre/post timestamps relative to a source, you must specify the starting vertex.

> ⭐ **Exam Hint from Prof:** "If you pass a starting vertex AND only use `ccnum[]`, that is fine but unnecessary. Lose no points — it just adds detail you didn't need."

- **Use cases that REQUIRE a starting vertex:** finding a specific s→t path, computing pre/post timestamps from a known source, running Bellman-Ford-style exploration from s.
- **Use cases where starting vertex is irrelevant:** finding all connected components, getting all `ccnum[]` values, general cycle detection across the whole graph.
- Tim: *"All other exploration through the graph is arbitrary — we only control the starting vertex."*

---

### ⚠️ DFS Exploration Order is NOT Deterministic
**Type:** Mistakes

| Mistake | Correction |
|---|---|
| ✗ Assuming DFS will take a specific path or include a specific edge | ✓ DFS exploration order is arbitrary beyond the starting vertex. Never write a solution that relies on DFS choosing a particular neighbor order or traversal path. |
| ✗ Trying to "steer" DFS to explore in a specific direction | ✓ Instead of manipulating DFS internals, **manipulate the INPUT GRAPH** so that the edge or path you need is the only valid option — an "unavoidable consequence of the input graph." Tim's exact words: *"Make it unavoidable, not directed."* |
| ✗ Assuming DFS textbook exercises reflect real DFS behavior | ✓ Textbook exercises enforce deterministic exploration (e.g., alphabetical order) only to make solutions consistent for grading. This is NOT guaranteed behavior of the DFS black box. |
| ✗ Writing a solution that depends on which subtree DFS explores after hitting a cycle | ✓ After a back edge is detected, which subtree DFS backtracks to next is arbitrary. Your correctness argument must hold for ALL possible exploration orders. |

---

### 📐 Advanced: Forcing DFS Order via Adjacency List
**Type:** Theorem

**Statement:** You CAN influence DFS exploration order by pre-ordering your adjacency list — but you must be **fully explicit** about this in your solution writeup. It is NOT implicit.

> `If adjacency list of v is ordered [u1, u2, u3], DFS explores u1 before u2 before u3`

TA Jamie McPeek clarified: DFS processes neighbors in the order they appear in the adjacency list. If you deliberately sort or arrange the adjacency list before passing it to DFS, you can control exploration order. This is how specifying a "start at vertex W" actually works internally. However, this is advanced, uncommon, and requires explicit justification in your solution.

> ⭐ **Exam Hint from Prof:** "If a problem seems to require a specific DFS order, look first for a graph transformation that makes the correct behavior unavoidable. Adjacency list ordering is a last resort and requires full explanation."

- Jamie: *"You would need to be explicit in your detailing of why and how you're doing that."*
- This technique was raised in the context of the SCC algorithm's second DFS — the post-order from the first DFS dictates the starting order of the second DFS, enforced via adjacency list ordering.
- For exam problems: if you use this technique, clearly state: *"Sort the adjacency list of G as follows: [describe ordering]. Then run DFS. This ensures..."*

---

### 📐 SCC Second DFS — Why Order Matters
**Type:** Theorem

**Statement:** The second DFS in Kosaraju's SCC algorithm must process vertices in **DECREASING post-order** from the first DFS. This ordering is what correctly identifies each SCC.

> `Second DFS starting order = reverse of first DFS finish order (post[] values, descending)`

Winston Bowen raised this question directly: the second DFS in SCC is NOT arbitrary — it relies on the post-order numbering from the first DFS. The vertex with the highest `post[]` value from the first run is the starting point of the second DFS. This is enforced by building the adjacency list for the second DFS in reverse post-order. Jamie confirmed: this is done via adjacency list ordering, not by modifying DFS itself.

> ⭐ **Exam Hint from Prof:** "When explaining SCC in an exam answer: 'Run DFS on G to compute `post[]` values. Then run DFS on G^R processing vertices in decreasing `post[]` order.' — The decreasing post-order IS the black box input you pass in."

- The first DFS on G gives `post[]` for all vertices.
- Sort vertices by decreasing `post[]` value. This sorted order becomes the adjacency list / exploration order for the second DFS on G^R.
- Each new `Explore()` call in the second DFS discovers exactly one SCC.
- You do NOT modify DFS — you only control what order you pass vertices as starting points.

---

### 📊 Topics Covered & Coming Up (Session Roadmap)
**Type:** Strategy

- **TODAY:** Graph properties deep dive — what properties to look for in graph problems (connectivity, directionality, cycles, weights).
- **TODAY:** Black box call-outs — extended use cases for each algorithm beyond the basics.
- **TODAY (Q&A):** Boolean formulas and 2-SAT — how Boolean satisfiability maps to a directed graph problem solvable with SCC.
- **TODAY (Q&A):** Testing with graphs — strategies for verifying your graph algorithms on different graph types (sparse, dense, DAG, cyclic, disconnected).
- **NEXT WEEK (HW):** Minimum Spanning Tree problem — Homework 5 wrapping up, MST problem assigned.
- **FUTURE SESSIONS:** MST algorithms (Kruskal's, Prim's) and Max Flow (Ford-Fulkerson, Edmonds-Karp) — not yet covered in depth.

---

### 💡 Properties to Memorize from This Session

| Property | Detail | Exam? |
|---|---|---|
| `ccnum[]` semantics: undirected → connectivity, directed → strong connectivity | Same ccnum on undirected = can reach each other. Same ccnum on directed = SCC (mutual reachability). NEVER use `ccnum[]` for one-way reachability on directed graphs. | ⭐ LIKELY ON EXAM |
| DFS starting vertex is optional — only required for s-specific queries | If you only need `ccnum[]`, don't specify a starting vertex. Only required for s→t paths and pre/post timestamps from a known source. | ⭐ LIKELY ON EXAM |
| DFS exploration order beyond starting vertex is arbitrary | Never write a solution that depends on DFS taking a specific path. Manipulate the graph to make the right behavior unavoidable instead. | ⭐ LIKELY ON EXAM |
| Force DFS order → pre-order the adjacency list (must justify explicitly) | You can control DFS exploration by ordering the adjacency list. This is how SCC's second DFS enforces post-order processing. Must be stated explicitly in solutions. | ⭐ LIKELY ON EXAM |
| SCC second DFS: process vertices in decreasing `post[]` order from first DFS | The second DFS on G^R must start from the vertex with the highest `post[]` value from the first run. This is the core correctness argument for Kosaraju's. | ⭐ LIKELY ON EXAM |
| 2-SAT reduces to SCC on an implication graph | A Boolean formula in 2-CNF is satisfiable iff no variable x and its negation ¬x are in the same SCC of the implication graph. | ⭐ LIKELY ON EXAM |

---
---

## Session 3 — 03/04/2026
### SCC Meta Graph, Universal Vertices & Exam Strategy
**Instructor:** Prof. Brito (Gerandy)

Pre-Exam 2 session. Class average on Exam 1 was ~80%. Prof. noted Exam 2 is historically more accessible and students typically recover confidence on the graphs unit.

---

### 📐 Critical Identity: Reversing vs. Meta Graph Order
**Type:** Theorem

**Statement:** You can reverse the original graph BEFORE or AFTER building the SCC meta graph — you get the **SAME result** either way.

> `meta(G^R) = (meta(G))^R`

The SCC meta graph of the reversed graph equals the reversed SCC meta graph of the original graph. This means you never need to worry about which order you reverse — both paths lead to the same structure.

> ⭐ **Exam Hint from Prof:** "Great multiple-choice trap: 'Do you reverse the whole graph or half?' The answer: it does not matter. Reverse before or after SCC — same result."

- If asked to find SCCs of G^R, you can just take G_SCC and reverse all its edges — O(n+m), no second SCC run needed.
- This identity is also why Kosaraju's algorithm works: running DFS on G^R is equivalent to running DFS on the reversed meta-structure.

---

### 🎯 The "Universal Vertex" Problem (Linear Time)
**Type:** Problem

**Problem Statement:** Find all vertices v in a directed graph such that v can reach EVERY other vertex AND every other vertex can reach v. This is a two-directional reachability requirement — v must be the "hub" of the entire graph.

**❌ Brute Force (Too Slow — Do Not Use)**

Approach: BFS from every vertex, check if all n vertices are visited. Repeat on reverse graph. Intersect results. Runtime: **O(n · (n+m))** — quadratic. Prof explicitly said: "that sounds too much" and this is NOT the intended solution.

**✅ Optimal Solution — O(n+m)**

1. Run **Strongly Connected Components** to build G_SCC = (V_SCC, E_SCC).
2. Run **Topological Sort** on G_SCC. Get topological order V1, V2, ..., Vk (source to sink).
3. **Forward pass:** Track which metanodes can reach ALL other metanodes looking forward in topo order. A metanode Vi can reach everything forward if and only if Vi is the **UNIQUE SOURCE** in the subgraph induced by {Vi, Vi+1, ..., Vk}.
4. **Backward pass:** Reverse G_SCC. Run the same procedure. Find all metanodes that can be reached from everything looking backward — i.e., unique source in the reversed subgraph.
5. **Intersection:** Vertices that satisfy BOTH conditions are the universal vertices. Map back to original vertices using `ccnum[]`.

**Runtime:** O(n+m) — SCC is O(n+m), Topo Sort is O(n+m), both passes are O(n_SCC + m_SCC) = O(n+m).

**Key Insight:** In a DAG with topological order V1...Vn: Vi can reach Vj (j > i) IF AND ONLY IF Vi is the unique source in the sub-DAG starting from Vi. This transforms a reachability question into a simple structural check.

> ⚠️ **Gotchas:**
> - An isolated vertex is BOTH a source and a sink simultaneously — it satisfies neither direction of the universal requirement.
> - Prof's hint: "If you have a unique source, you MUST be able to reach everything from that source."
> - This problem was assigned as homework — prof said: "It is NOT my intention to give this in exam time." May appear as a concept question, not a full derivation.
> - The SCC step is critical: compress all cycles first, then work on the resulting DAG where reachability is clean and transitive.

---

### 📐 Key DAG Theorem (Stated in Lecture)
**Type:** Theorem

**Statement:** Let G be a directed acyclic graph with topological order V1, V2, ..., Vn. Then: Vi reaches Vj (for j > i) **if and only if** Vi is the unique source in the subgraph induced by vertices {Vi, Vi+1, ..., Vn}.

This is a structural characterization of reachability in DAGs. Instead of running BFS to check if Vi can reach Vj, you just check: is Vi the only node with no incoming edges in the suffix of the topological order? If yes — it reaches everything after it.

This converts an O(n+m) BFS reachability check into a single structural inspection of the DAG — enabling the linear-time universal vertex algorithm above.

> ⭐ **Exam Hint from Prof:** "If an exam question gives you a DAG and asks about reachability, think about this theorem. 'Can vertex V reach all others?' ↔ 'Is V the unique source in the DAG?'"

---

### ⚠️ Common Mistakes Prof Flagged
**Type:** Mistakes

| Mistake | Correction |
|---|---|
| ✗ Reversing only part of the graph | ✓ Always reverse the ENTIRE graph. `meta(G^R) = (meta(G))^R`, so reverse before or after SCC — same result. |
| ✗ Using O(n²) brute force BFS for universal vertex | ✓ The intended solution is O(n+m) using SCC + Topo Sort. BFS from every vertex is too slow and won't get full credit. |
| ✗ Forgetting that isolated vertices are both sources AND sinks | ✓ An isolated vertex (no edges) trivially satisfies "is a source" and "is a sink" — but it cannot reach anything else. Account for this in the universal vertex check. |
| ✗ Treating the brute-force (BFS from every vertex + reverse) as the answer | ✓ Prof acknowledged this solution exists and works, but called it "O(n²) — too much." The O(n+m) SCC-based solution is what is expected. |

---

### 📊 Exam 2 Strategy (Prof's Direct Words)
**Type:** Strategy

Exam 2 is historically **MORE ACCESSIBLE** than Exam 1. The graphs unit is where students recover confidence.

The correlation: because Exam 1 is always rough, Prof's team calibrates Exam 2 to be friendlier. The subject itself (graphs) is genuinely more approachable.

The universal vertex homework problem was intentionally hard and time-consuming. It is **NOT** expected to be solved under exam conditions in 1-2 hours.

The **POINT** of the homework was: think about all the little properties (unique source ↔ reaches everything, reverse symmetry, isolated vertex = source + sink) — **THOSE properties are what will appear in exam questions.** Memorize the tricks, not the full derivation.

TAs have been giving extra office hours — Prof encouraged using those and sharing feedback.

---

### 💡 Properties to Memorize from This Session

| Property | Detail | Exam? |
|---|---|---|
| `meta(G^R) = (meta(G))^R` | SCC meta graph of reverse = reverse of SCC meta graph. Reverse before or after — same result. | ⭐ LIKELY ON EXAM |
| Unique source in G_SCC ↔ that SCC can reach all other SCCs | If an SCC has no incoming edges, every vertex in the original graph is reachable from it. | ⭐ LIKELY ON EXAM |
| Vi reaches all of {Vi+1,...,Vn} ↔ Vi is the unique source of that suffix | In a DAG with topo order, reachability-forward = being the unique source looking ahead. | ⭐ LIKELY ON EXAM |
| Isolated vertex = source AND sink simultaneously | A vertex with no edges trivially satisfies both properties but reaches/is-reached-by nothing. | ⭐ LIKELY ON EXAM |
| BFS from every vertex = O(n²) — too slow | When a problem asks for linear time, O(n²) BFS brute force will not get full credit. | — |
| Universal vertex algorithm: SCC → Topo Sort → forward pass ∩ backward pass | The O(n+m) approach: find vertices that are unique sources both forward and backward in the topo-sorted meta graph. | ⭐ LIKELY ON EXAM |

---
---

## Session 4 — 03/08/2026
### Kruskal's vs Prim's, Union-Find & Path Compression
**Instructor:** TA Tim West

Pre-Exam 2 session (March 8). Exam 2 starts mid-next-week. HW6 and HW7 due tonight. 4 graph walkthrough videos posted on Ed. Week 9 content quiz opening tomorrow. MST and Flow suggested problems + videos releasing Thursday. Main focus: Kruskal's and Prim's black box details, with walkthrough of Union-Find path compression from problem 5.2B.

---

### 📊 Logistics
**Type:** Strategy

- HW6 and HW7 due tonight. Tim strongly recommends the **WRITTEN homework** for grader feedback — best way to understand what the team is looking for.
- 4 graph walkthrough videos posted (Graphs Suggested Problems post + Ed Resources). Tim chose what he considers the more interesting problems in the set.
- Week 9 starts tomorrow: Graphs content quiz opens (graded). Another coding assignment on graphs. MST and Flow suggested problems + walkthrough videos Thursday.
- Exam 2 is about 1.5 weeks away. Topics covered today **ARE on Exam 2**.

---

### 📐 Kruskal's — Must Be Connected
**Type:** Theorem

**Statement:** Kruskal's **REQUIRES** a guaranteed connected input graph. Cannot safely run on a disconnected graph in this class.

> `Input: simple, CONNECTED, undirected graph + edge weights`

Some sources say Kruskal's can run on disconnected graphs and output a spanning forest. In **THIS CLASS**: keep it simple — Kruskal's input must be connected. You must guarantee connectivity from the problem statement or verify it (run BFS/DFS first) before calling Kruskal's.

> ⭐ **Exam Hint from Prof:** "If a problem gives a potentially disconnected graph and asks for an MST, state how you guarantee connectivity before calling Kruskal's."

- Valid workaround (Tim mentioned): build connected subgraphs separately, run Kruskal's on each, then combine the resulting subtrees into a final solution.
- Output format: `edges[]` is a list of edges [E1, E2, ..., E_{n-1}]. **NOT parent pointers.** Key difference from Prim's.

---

### 📐 Kruskal's vs Prim's Output — Critical Difference
**Type:** Theorem

**Statement:** Kruskal's output = `edges[]` (edge list). Prim's output = `prev[]` (parent pointers). **NOT interchangeable** without conversion.

> `Kruskal's: edges[] = [E1, E2, ..., E_{n-1}]`
> `Prim's: prev[v] = parent of v in MST`

To traverse the MST after Kruskal's, you must first convert `edges[]` to an adjacency list in O(n+m). With Prim's, you can trace paths directly via `prev[]`.

> ⭐ **Exam Hint from Prof:** "Exam trap: using Kruskal's `edges[]` directly as a graph. Always state: 'Convert Kruskal's `edges[]` to adjacency list G_MST in O(n+m)' before running BFS/DFS."

- Prim's `prev[]` is identical in format to Dijkstra's `prev[]` and BFS `prev[]` — trace paths the same way.

---

### 🔄 Union-Find Internals (How Kruskal's Works)
**Type:** Facts

1. **Kruskal's is GREEDY:** sorts all edges lightest to heaviest, adds each if it doesn't create a cycle. For each edge (v,w): if v and w are in different components → add. Same component → skip (cycle).
2. **Union-Find is INTERNAL to Kruskal's** — not a separate black box you call. Tim: *"that is more of a data structure topic."* You do not implement it for exams. The cycle check is abstracted as `c(v) ≠ c(w)`.
3. **Path compression:** always attach a new vertex to the root with the highest existing tree depth. When merging, shallower root becomes child of deeper root. Keeps the union-find tree flat for efficiency.
4. **Example (5.2B):** edges chosen by weight order. {A,B} (w=1), {F,G} (w=1), extend to {F,G,D}, {F,G,D,H}. Skip any edge that creates a cycle. At weight 3: one edge creates cycle — skip. At weight 4: take one, skip the cycle-former. At weight 5: would form cycle — skip.

---

### ⚠️ Common Mistakes
**Type:** Mistakes

| Mistake | Correction |
|---|---|
| ✗ Running Kruskal's on a possibly disconnected graph | ✓ Kruskal's requires **CONNECTED** input. Verify with BFS/DFS in O(n+m) first or guarantee from problem statement. |
| ✗ Using Kruskal's `edges[]` output directly as a graph for BFS/DFS | ✓ Always state: "Convert Kruskal's `edges[]` to adjacency list G_MST in O(n+m)" before traversing. |
| ✗ Treating Union-Find as a separate black box to call | ✓ Union-Find is **INTERNAL** to Kruskal's. You never call it separately. Cycle check is handled inside Kruskal's. |

---

### 💡 Properties to Memorize

| Property | Detail | Exam? |
|---|---|---|
| Kruskal's requires CONNECTED input graph | Verify or guarantee connectivity before calling Kruskal's. | ⭐ LIKELY ON EXAM |
| Kruskal's output = `edges[]` (list of n−1 edges) | NOT parent pointers. Convert to adjacency list in O(n+m) before BFS/DFS on MST. | ⭐ LIKELY ON EXAM |
| Prim's output = `prev[]` (parent pointers, same format as Dijkstra's) | Trace paths directly via `prev[]`. No conversion needed. | ⭐ LIKELY ON EXAM |
| Union-Find is internal to Kruskal's — not a separate black box | Cycle check abstracted as `c(v) ≠ c(w)`. Handled inside the algorithm. | — |

---

All 4 office hours sessions have been fully extracted and formatted. The content covers sessions from **02/22/2026 through 03/08/2026**, spanning foundational graph representations, DFS/SCC deep dives, exam strategy, and MST algorithm details.