<!--markdownlint-disable-->
# 📚 Advanced Graphs (Advanced Beginner‑Friendly Master Guide)

This guide deepens six challenging LeetCode **Advanced Graph** problems with clear theory, step‑by‑step examples, line‑by‑line code breakdowns, pitfalls, complexity analysis, and advanced tips.

Each section includes  
1. **Theory**: Why the algorithm works.  
2. **Approach Walkthrough**: A small example you can trace.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary.  
4. **Pitfalls & Tips**: Common mistakes and best practices.  
5. **Complexity**: Time and space analysis.  
6. **Advanced Tips & Variants**: Further optimizations or related problems.

---

## 1. Network Delay Time  
Link: https://leetcode.com/problems/network-delay-time/

### Theory: Single‑Source Shortest Paths (Dijkstra)  
We model the network as a directed weighted graph. Dijkstra’s algorithm finds the shortest path from a single source to all nodes in O(E log V) time using a min‑heap.

### Approach Walkthrough  
```
times = [[2,1,1],[2,3,1],[3,4,1]], N=4, K=2
```
- Start at 2 (dist=0).  
- Visit neighbors 1 and 3 (dist=1 each).  
- From 3 to 4 (dist=2).  
- Max distance = 2.

### Code Breakdown  
```cpp
int networkDelayTime(vector<vector<int>>& times, int N, int K) {
    // 1) Build adjacency list
    vector<vector<pair<int,int>>> adj(N+1);
    for (auto &t : times)
        adj[t[0]].push_back({t[1], t[2]});

    const int INF = 1e9;
    vector<int> dist(N+1, INF);
    dist[K] = 0;                     // 2) Distance to source is 0

    // 3) Min‑heap of (distance, node)
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, K});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;  // 4) Skip outdated entries
        for (auto &[v, w] : adj[u]) {
            if (d + w < dist[v]) {  // 5) Relax edge
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }

    // 6) Compute the maximum distance
    int ans = 0;
    for (int i = 1; i <= N; ++i) {
        if (dist[i] == INF) return -1;
        ans = max(ans, dist[i]);
    }
    return ans;
}
```
1. Build graph.  
2. Initialize distances.  
3. Use a min‑heap for efficient extraction of the next closest node.  
4. Ignore heap entries that no longer match the best distance.  
5. Relax edges and update distances.  
6. Check reachability and return the maximum shortest path.

Pitfalls & Tips  
- Always check `d > dist[u]` after popping from the heap.  
- Use a large `INF` constant to initialize unreachable distances.

Complexity  
- Time: O(E log V)  
- Space: O(E + V)

Advanced Variants  
- Use Bellman–Ford for graphs with negative weights (O(V E)).  
- For dense graphs, switch to O(V²) array-based Dijkstra.

---

## 2. Reconstruct Itinerary  
Link: https://leetcode.com/problems/reconstruct-itinerary/

### Theory: Eulerian Path via Hierholzer’s Algorithm  
We treat tickets as directed edges in a graph that has an Eulerian path. Hierholzer’s algorithm traverses each edge exactly once, building the path in reverse.

### Approach Walkthrough  
```
tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
```
- Start at JFK, always picking the lexicographically smallest destination.  
- On backtrack, append airports to route.  
- Reverse route for final itinerary.

### Code Breakdown  
```cpp
vector<string> findItinerary(vector<vector<string>>& tickets) {
    // 1) Build sorted adjacency: src → multiset of dests
    unordered_map<string, multiset<string>> adj;
    for (auto &t : tickets)
        adj[t[0]].insert(t[1]);

    vector<string> route;
    function<void(const string&)> dfs = [&](const string& u) {
        auto &ms = adj[u];
        while (!ms.empty()) {
            // 2) Get the smallest destination
            string v = *ms.begin();
            ms.erase(ms.begin());  // 3) Remove used ticket
            dfs(v);                // 4) Recurse
        }
        route.push_back(u);       // 5) Append on backtrack
    };

    dfs("JFK");                   // 6) Start traversal
    reverse(route.begin(), route.end());
    return route;
}
```
1. Use a multiset to maintain sorted order.  
2–3. Extract and remove the smallest neighbor.  
4–5. Recurse and append after exhausting edges.  
6. Reverse to obtain correct itinerary.

Pitfalls & Tips  
- Erasing from a multiset invalidates only that iterator.  
- Ensure you run DFS until all edges are consumed.

Complexity  
- Time: O(E log E)  
- Space: O(E)

Advanced Variants  
- Use sorted vectors + index pointers instead of multiset for speed.  
- Apply similar logic to undirected Eulerian trails with paired edge removals.

---

## 3. Min Cost to Connect All Points  
Link: https://leetcode.com/problems/min-cost-to-connect-all-points/

### Theory: Minimum Spanning Tree (Prim’s/Kruskal’s)  
Model points as a complete graph with edge weights = Manhattan distances. An MST minimizes total cost.

### Approach Walkthrough  
```
points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
```
- Start from point 0.  
- Greedily add the nearest new point not in the MST.  
- Update candidate distances, repeat until all points included.

### Code Breakdown  
```cpp
int minCostConnectPoints(vector<vector<int>>& P) {
    int n = P.size();
    vector<int> minDist(n, INT_MAX);
    vector<bool> inMST(n, false);
    minDist[0] = 0;    // 1) Start at node 0
    int total = 0;

    for (int i = 0; i < n; ++i) {
        // 2) Pick nearest non‑MST point
        int u = -1, d = INT_MAX;
        for (int j = 0; j < n; ++j) {
            if (!inMST[j] && minDist[j] < d) {
                d = minDist[j]; u = j;
            }
        }
        total += d;        // 3) Add its cost
        inMST[u] = true;   // 4) Mark included

        // 5) Update distances to the MST
        for (int v = 0; v < n; ++v) {
            if (!inMST[v]) {
                int w = abs(P[u][0]-P[v][0]) + abs(P[u][1]-P[v][1]);
                minDist[v] = min(minDist[v], w);
            }
        }
    }
    return total;
}
```
1. Initialize distances, include first node.  
2. Linear scan to find the next closest point.  
3–4. Add cost and mark inclusion.  
5. Relax edges to update candidate costs.

Pitfalls & Tips  
- For large n, using a heap yields O(E log V) but E ≈ n² can be expensive.  
- Kruskal’s algorithm requires generating and sorting all edges (O(n² log n²)).

Complexity  
- Time: O(n²)  
- Space: O(n)

Advanced Variants  
- Use a binary heap for an O(n² log n) Prim’s.  
- Delaunay triangulation reduces candidate edges to O(n).

---

## 4. Swim in Rising Water  
Link: https://leetcode.com/problems/swim-in-rising-water/

### Theory: Minimize the Maximum Elevation Along the Path  
We need a path whose maximum elevation is minimized. Dijkstra’s algorithm applies with the path cost defined as the maximum elevation seen so far.

### Approach Walkthrough  
```
grid =
0 2
1 3
```
- Start at (0,0) with cost=0.  
- Move to (1,0): cost = max(0,1)=1.  
- Move to (1,1): cost = max(1,3)=3.  
- Best arrival time = 3.

### Code Breakdown  
```cpp
int swimInWater(vector<vector<int>>& A) {
    int N = A.size();
    vector<vector<int>> dist(N, vector<int>(N, INT_MAX));
    dist[0][0] = A[0][0];   // 1) Initial time
    // Min‑heap of (time, r, c)
    priority_queue<array<int,3>, vector<array<int,3>>, greater<>> pq;
    pq.push({dist[0][0], 0, 0});
    int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};

    while (!pq.empty()) {
        auto [t, r, c] = pq.top(); pq.pop();
        if (r == N-1 && c == N-1) return t;   // 2) Reached target
        if (t > dist[r][c]) continue;         // 3) Skip stale
        for (auto &d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr<0||nr>=N||nc<0||nc>=N) continue;
            // 4) New time = max(current time, neighbor elev.)
            int nt = max(t, A[nr][nc]);
            if (nt < dist[nr][nc]) {
                dist[nr][nc] = nt;
                pq.push({nt, nr, nc});
            }
        }
    }
    return -1;
}
```
1. Initialize with starting elevation.  
2. Return immediately when reaching target.  
3. Ignore outdated heap entries.  
4. Propagate the maximum elevation along each path.

Pitfalls & Tips  
- Binary search + BFS is an alternative: O(N² log maxH).  
- Union‑find on sorted cells can yield O(N² log N²).

Complexity  
- Time: O(N² log N)  
- Space: O(N²)

---

## 5. Alien Dictionary  
Link: https://leetcode.com/problems/alien-dictionary/

### Theory: Topological Sort on Character Graph  
We infer ordering by comparing adjacent words for their first differing character, forming directed edges. A topological sort yields a valid character order or detects cycles.

### Approach Walkthrough  
```
words = ["wrt","wrf","er","ett","rftt"]
```
- Edges: w→e, r→t, t→f.  
- Topo sort (e,r,t,w,f) or other valid orders.

### Code Breakdown  
```cpp
string alienOrder(vector<string>& words) {
    unordered_map<char, vector<char>> adj;
    unordered_map<char, int> indeg;
    // 1) Initialize indegree for all chars
    for (auto &w : words)
        for (char c : w)
            indeg.emplace(c, 0);

    // 2) Build edges by comparing adjacent words
    for (int i = 1; i < words.size(); ++i) {
        auto &A = words[i-1], &B = words[i];
        int L = min(A.size(), B.size()), j = 0;
        while (j < L && A[j] == B[j]) ++j;
        if (j == L && A.size() > B.size()) return "";
        if (j < L) {
            adj[A[j]].push_back(B[j]);
            indeg[B[j]]++;
        }
    }

    // 3) Kahn’s BFS
    queue<char> q;
    for (auto &p : indeg)
        if (p.second == 0) q.push(p.first);
    string res;
    while (!q.empty()) {
        char u = q.front(); q.pop();
        res.push_back(u);
        for (char v : adj[u]) {
            if (--indeg[v] == 0) q.push(v);
        }
    }
    return (res.size() == indeg.size()) ? res : "";
}
```
1. Record every unique character.  
2. Compare each pair of words to add one directed edge.  
3. Perform BFS-based topological sort.

Pitfalls & Tips  
- Handle invalid prefix cases (`"abc","ab"`).  
- Multiple valid orders exist; use a priority queue for lex smallest.

Complexity  
- Time: O(N × L + V + E)  
- Space: O(V + E)

---

## 6. Cheapest Flights Within K Stops  
Link: https://leetcode.com/problems/cheapest-flights-within-k-stops/

### Theory: Bellman–Ford Variant or Constrained BFS  
We want the cheapest cost with ≤ K stops. Bellman–Ford iterated K+1 times updates reachable distances.

### Approach Walkthrough  
```
n=3, flights=[[0,1,100],[1,2,100],[0,2,500]], src=0, dst=2, K=1
```
- After 1 iteration: dist = [0,100,500]  
- After 2 iterations: dist[2] updated to 200 via 0→1→2.  
- Return 200.

### Code Breakdown  
```cpp
int findCheapestPrice(int n, vector<vector<int>>& flights,
                      int src, int dst, int K) {
    const int INF = 1e9;
    vector<int> dist(n, INF), tmp;
    dist[src] = 0;                   // 1) Start with source cost 0

    for (int i = 0; i <= K; ++i) {
        tmp = dist;                  // 2) Copy previous distances
        for (auto &f : flights) {
            int u = f[0], v = f[1], w = f[2];
            if (dist[u] < INF && dist[u] + w < tmp[v]) {
                tmp[v] = dist[u] + w; // 3) Relax edge within K stops
            }
        }
        dist.swap(tmp);              // 4) Commit updates
    }
    return dist[dst] == INF ? -1 : dist[dst];
}
```
1. Initialize distances.  
2. Copy before each relaxation phase.  
3. Relax every edge once per allowed stop.  
4. Update the main distances array.

Pitfalls & Tips  
- Ensure you copy `dist` → `tmp` each round to prevent using more than K edges.  
- BFS with state `(node, cost, stops)` and a min‑heap is an alternative.

Complexity  
- Time: O(K × E)  
- Space: O(N)

---

## 🔧 Expanded Helper Functions  
```cpp
// Disjoint Set Union (for MST / connectivity)
struct DSU {
    vector<int> p, r;
    DSU(int n): p(n), r(n,0){ iota(p.begin(),p.end(),0); }
    int find(int x){ return p[x]==x? x: p[x]=find(p[x]); }
    bool unite(int a,int b){
        a=find(a); b=find(b);
        if(a==b) return false;
        if(r[a]<r[b]) swap(a,b);
        p[b]=a; if(r[a]==r[b]) r[a]++;
        return true;
    }
};

// 4‑direction vectors for grid BFS/DFS
int dirs4[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
```

---

## 🔍 Deep Dive into Paradigms

- **Dijkstra’s Algorithm**: greedy SSSP with a min‑heap.  
- **Hierholzer’s for Eulerian Path**: build paths on backtrack.  
- **Prim’s MST (Dense vs. Sparse trade‑offs)**: O(V²) vs. O(E log V).  
- **Minimize Maximum Edge**: Dijkstra variant with max cost accumulation.  
- **Topological Sort**: Kahn’s BFS and cycle detection.  
- **Bellman–Ford Variant**: constrained relaxations for K‑stop shortest path.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Challenges  
- **Multi‑Source Dijkstra** (multiple starting nodes)  
- **Directed Euler Tours** with repeated edges  
- **Steiner Tree / k‑MST** variants  
- **Grid Minimum Maximum Path** via binary search + DSU  
- **Course Schedule III** (maximize courses taken)

### Advanced Applications  
- Real‑time routing with dynamic edge weights.  
- Network flow reductions for multi‑commodity problems.  
- Graph pattern matching and motif finding.

### Debugging Checklist  
1. Validate graph construction (adjacency lists/edge lists).  
2. Confirm priority queue comparator ordering.  
3. Protect against stale heap entries (`d > dist[u]`).  
4. Check base cases: unreachable nodes or cycles.  
5. Profile for dense vs. sparse graph performance.

Happy exploring these advanced graph algorithms!  