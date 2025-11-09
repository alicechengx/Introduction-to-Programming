# Graph Algorithm Selection Guide

## I. Core Decision Dimensions

### 1. Query Scope
- **Single-source query**: Only need to start from one or a few nodes
- **Global query**: Need to start from all/many nodes

### 2. Graph Properties
- **Directed Acyclic Graph (DAG)**: Has topological ordering
- **General graph**: May contain cycles

### 3. Scale & Performance Requirements
- Number of nodes N, edges M
- Whether bitwise optimization is needed

---

## II. Algorithm Decision Tree

### Scenario 1: Single-source / Few Sources Query

```
Problem: Which nodes are reachable from node s?
         Does a path exist from s to t?
```

**✅ Use: Regular DFS/BFS**

```cpp
// DFS version
bool visited[MAXN];
void dfs(int u) {
    visited[u] = true;
    for (int v : graph[u]) {
        if (!visited[v]) dfs(v);
    }
}

// BFS version
void bfs(int start) {
    queue<int> q;
    q.push(start);
    visited[start] = true;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : graph[u]) {
            if (!visited[v]) {
                visited[v] = true;
                q.push(v);
            }
        }
    }
}
```

**Time Complexity**: O(N + M)  
**Space Complexity**: O(N)  
**Use Cases**:
- Single-source shortest path (BFS for unweighted graphs)
- Connectivity checking
- Path searching
- Occasional reachability queries

---

### Scenario 2: Global Query + General Graph

```
Problem: How many nodes can each node reach? (Graph may contain cycles)
```

**✅ Use: DFS/BFS + Memoization (without bitset)**

```cpp
vector<int> reachable[MAXN];
bool visited[MAXN];
bool in_stack[MAXN];  // Detect cycles

void dfs(int u) {
    if (visited[u]) return;
    visited[u] = true;
    in_stack[u] = true;
    
    reachable[u].push_back(u);
    
    for (int v : graph[u]) {
        if (in_stack[v]) continue;  // Skip if cycle detected
        dfs(v);
        for (int x : reachable[v]) {
            reachable[u].push_back(x);
        }
    }
    
    in_stack[u] = false;
}
```

**Time Complexity**: O(N × M)  
**Why not bitset**: General graphs lack good processing order, bitset advantage is minimal  
**Use Cases**:
- Small to medium graphs (N ≤ 5000)
- Graphs with potential cycles
- Need to store actual reachable nodes (not just count)

---

### Scenario 3: Global Query + DAG + Large Scale

```
Problem: How many nodes can each node reach? (DAG, N ≤ 30000)
```

**✅ Use: Topological Sort + Bitset**

```cpp
bitset<MAXN> reachable[MAXN];
int indegree[MAXN];

void topoSort() {
    queue<int> q;
    for (int i = 1; i <= n; i++) {
        if (indegree[i] == 0) q.push(i);
        reachable[i][i] = 1;
    }
    
    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : graph[u]) {
            reachable[v] |= reachable[u];  // O(N/64)
            if (--indegree[v] == 0) q.push(v);
        }
    }
}
```

**Time Complexity**: O(M × N/64)  
**Key Advantages**:
- Topological order ensures each node is processed once
- Bitset optimizes set merge by 64x
- 64x constant optimization is the key to AC!

**Use Cases**:
- Transitive closure on DAG
- Large-scale reachability statistics
- Dependency analysis

---

### Scenario 4: Global Query + DAG + Path Information

```
Problem: Longest/shortest path from each node to others?
```

**✅ Use: Topological Sort + DP**

```cpp
int dp[MAXN];  // dp[u] = longest path from u

void topoSort() {
    queue<int> q;
    for (int i = 1; i <= n; i++) {
        if (indegree[i] == 0) q.push(i);
    }
    
    while (!q.empty()) {
        int u = q.front(); q.pop();
        for (int v : graph[u]) {
            dp[v] = max(dp[v], dp[u] + 1);
            if (--indegree[v] == 0) q.push(v);
        }
    }
}
```

**Use Cases**:
- Longest/shortest path in DAG
- Critical path
- Project scheduling

---

## III. Quick Decision Table

| Scenario | Graph Type | Scale | Queries | Best Algorithm |
|----------|-----------|-------|---------|----------------|
| Single-node reachability | Any | Any | 1 | **DFS/BFS** |
| Single-source shortest path | Unweighted | Any | 1 | **BFS** |
| Global reachability | General | Small(N≤5K) | N | **DFS + Memo** |
| Global reachability | DAG | Large(N≤30K) | N | **Topo + Bitset** |
| Path length | DAG | Any | N | **Topo + DP** |
| Strongly connected components | Directed | Any | 1 | **Tarjan/Kosaraju** |
| Transitive closure | DAG | Large | N² | **Topo + Bitset** |
| Transitive closure | General | Small | N² | **Floyd** |

---

## IV. When to Use Bitset

**Must satisfy ALL:**

1. ✅ **Set operations needed** (union, intersection, count)
2. ✅ **Large scale data** (N ≥ 10000)
3. ✅ **Time-critical** (normal approach will TLE)
4. ✅ **DAG or good processing order**

**Typical Applications:**
- DAG transitive closure
- Subset DP optimization
- Bitmask DP
- Large-scale reachability analysis

---

## V. Practice Problem Categories

### Use DFS/BFS
- Number of islands
- Maze problems
- Bipartite graph detection
- Shortest path in unweighted graph

### Use DFS + Memoization
- Medium-scale graph reachability
- Memoized search
- Tree DP

### Use Topo + Bitset
- **This problem: DAG reachable node counting**
- Course scheduling (dependency)
- Transitive closure in DAG
- Large-scale inheritance queries

### Use Topo + DP
- Longest path in DAG
- Project scheduling (critical path)
- Optimization with dependencies

---

## VI. Memory Trick

```
Single query → DFS/BFS
Global + cycles → Add memoization
Global + no cycles → Topological sort
Huge data → Bitset boost
```

---

## VII. Performance Comparison (N=30000, M=30000)

| Algorithm | Time Complexity | Actual Operations | Result |
|-----------|----------------|-------------------|--------|
| Naive DFS×N | O(N²) | 9×10⁸ | ❌ TLE |
| DFS + Memo (no bitset) | O(N×M) | 9×10⁸ | ❌ TLE |
| Topo + normal set | O(M×N) | 9×10⁸ | ❌ TLE |
| **Topo + Bitset** | O(M×N/64) | **1.4×10⁷** | ✅ AC |
| **DFS + Memo + Bitset** | O(M×N/64) | **1.4×10⁷** | ✅ AC |

Key: **64x optimization** makes the impossible possible!

---

## VIII. Bitset Fundamentals

### What is Bitset?

A bitset is a fixed-size sequence of bits that can be manipulated efficiently.

```cpp
bitset<100> bs;    // 100 bits, initially all 0
bs[5] = 1;         // Set bit 5 to 1
bs.set(10);        // Set bit 10 to 1
bs.count();        // Count number of 1s
bs1 |= bs2;        // Bitwise OR: O(N/64) instead of O(N)
```

### Why 64x Faster?

```cpp
// Normal set merge: O(N)
for (int x : reachable[v]) {
    reachable[u].insert(x);
}

// Bitset merge: O(N/64), one CPU instruction handles 64 bits
reachable[u] |= reachable[v];  // Single bitwise operation!
```

### Common Bitset Operations

```cpp
bitset<N> bs;

bs.set();           // Set all bits to 1
bs.reset();         // Set all bits to 0
bs.set(pos);        // Set bit at pos to 1
bs.reset(pos);      // Set bit at pos to 0
bs.flip();          // Flip all bits
bs.count();         // Count 1s
bs.any();           // Check if any bit is 1
bs.none();          // Check if no bit is 1
bs.test(pos);       // Check if bit at pos is 1

// Bitwise operations (O(N/64))
bs1 & bs2;          // AND
bs1 | bs2;          // OR
bs1 ^ bs2;          // XOR
~bs1;               // NOT
```

---

## IX. Why Topological Sort Works Better than DFS Memoization

While both have the same time complexity O(M×N/64), topological sort has advantages:

### Topological Sort Advantages
1. **Clear ordering**: Processes strictly by dependency, logic is clearer
2. **Space friendly**: Uses queue, small stack depth
3. **More intuitive**: Propagates reachability from "end" to "start"

### DFS Memoization Advantages
1. **Shorter code**: No need to maintain indegree and queue
2. **Flexible**: Can process partial nodes

---

## X. Real Implementation Tips

### Tip 1: Always Check for DAG
```cpp
// If not sure if graph is DAG, detect cycles first
bool hasCycle = false;
bool in_stack[MAXN];

void checkCycle(int u) {
    visited[u] = true;
    in_stack[u] = true;
    for (int v : graph[u]) {
        if (in_stack[v]) {
            hasCycle = true;
            return;
        }
        if (!visited[v]) checkCycle(v);
    }
    in_stack[u] = false;
}
```

### Tip 2: Bitset Size Must Be Compile-time Constant
```cpp
// ✅ Correct
bitset<30005> reachable[30005];

// ❌ Wrong - n is runtime variable
bitset<n> reachable[n];
```

### Tip 3: Handle Duplicate Edges
```cpp
// Method 1: Use set to avoid duplicates
set<pair<int,int>> edges;

// Method 2: Just ignore, doesn't affect correctness
// Duplicate edges only slightly increase operations
```

---

## XI. Interview Quick Reference

**Interviewer asks: "Given a DAG, find reachable nodes for all nodes"**

**Your thought process (say out loud):**

1. "This is a global query problem on a DAG"
2. "For small N, DFS memoization works"
3. "But if N is large (~30000), I need topological sort + bitset"
4. "The key insight is: process nodes in topological order, so when we process node u, all its descendants are already computed"
5. "Using bitset gives us 64x speedup on set operations"
6. "Time complexity: O(M × N/64), which is acceptable for N=30000"

**This shows you understand:**
- Problem categorization
- Complexity analysis
- Optimization techniques
- Trade-offs between approaches
