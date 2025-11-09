# 🕸️ Graph Theory Summary

## 1. Basic Concepts
- A **graph** is defined as \( G = (V, E) \), where  
  \( V \) = set of vertices (nodes)  
  \( E \) = set of edges (connections between nodes)
- **Directed graph** vs **Undirected graph**
- **Weighted** vs **Unweighted** graphs
- **Adjacency matrix** and **Adjacency list** representations

---

## 2. Core Algorithms

### 🔹 BFS (Breadth-First Search)
- Explores nodes level by level  
- Finds the **shortest path** in an unweighted graph  
- Time complexity: **O(V + E)**  

### 🔹 DFS (Depth-First Search)
- Explores as deep as possible before backtracking  
- Used for **connectivity**, **cycle detection**, and **topological sorting**  
- Time complexity: **O(V + E)**

### 🔹 Shortest Path Algorithms
| Algorithm | Description | Handles Negative Weights? |
|------------|--------------|---------------------------|
| **Dijkstra** | Greedy algorithm using a priority queue | ❌ |
| **Bellman-Ford** | Dynamic programming approach | ✅ |
| **Floyd-Warshall** | All-pairs shortest paths | ✅ |

### 🔹 Minimum Spanning Tree (MST)
- Connects all vertices with minimum total edge weight  
- **Kruskal’s Algorithm** (Union-Find)  
- **Prim’s Algorithm** (Greedy expansion)

### 🔹 Topological Sort
- Applicable only to **DAGs (Directed Acyclic Graphs)**  
- Implemented via DFS or Kahn’s algorithm (using in-degree)

---

## 3. Advanced Topics
- **Strongly Connected Components (SCC)** — Tarjan / Kosaraju algorithms  
- **Bipartite Graphs** — checking 2-colorability  
- **Eulerian Path / Circuit** — visiting every edge once  
- **Hamiltonian Path** — visiting every vertex once (NP-hard)  
- **Network Flow** — Ford–Fulkerson / Edmonds–Karp / Dinic

---

## 4. Common Problem Patterns
| Problem Type | Example Use | Notes |
|---------------|-------------|-------|
| Connectivity | Counting connected components | DFS / BFS |
| Shortest Path | Road network, graph traversal | Dijkstra |
| Graph Cloning | Copying a graph structure | BFS / DFS |
| Topological Order | Course schedule problems | DAGs only |
| MST | Minimizing connection cost | Kruskal / Prim |

---

## 5. LeetCode Practice Examples
| Problem | Algorithm |
|----------|------------|
| 200 — Number of Islands | DFS / BFS |
| 133 — Clone Graph | DFS / BFS |
| 207 — Course Schedule | Topological Sort |
| 743 — Network Delay Time | Dijkstra |
| 1631 — Path With Minimum Effort | Dijkstra + Binary Search |

---

## 6. Summary
> Graph theory is about **relationships between objects**.  
> Mastering traversal (DFS/BFS) and modeling techniques opens the door to most algorithmic problems.

---

✨ *End of Graph Theory Summary*
