<!--markdownlint-disable-->
# 📚 Advanced Graphs

This guide covers six challenging LeetCode problems under the **Advanced Graphs** topic. For each problem, you’ll find:

1. Problem description  
2. Core algorithmic ideas  
3. Step‑by‑step approach  
4. Concise C++ code snippet with inline comments  
5. Complexity analysis  
6. Advanced tips & related variants  

---

## 1. Network Delay Time  
Link: https://leetcode.com/problems/network-delay-time/

### Description  
Given directed, weighted edges `times[i] = {u, v, w}`, representing travel time `w` from `u` to `v`, and a starting node `K`, compute the minimum time to reach all nodes. Return the maximum of shortest paths, or `-1` if any node is unreachable.

### Core Idea: Single‐Source Shortest Paths (Dijkstra)  
- Build adjacency list; run Dijkstra from `K`.  
- Track shortest distances `dist[]`.  
- Result = max(dist[1…N]) if all finite, else `-1`.

### Approach  
1. Initialize `dist` array to ∞; `dist[K] = 0`.  
2. Use min‐heap `(d,node)`.  
3. While heap not empty:  
   - Pop `(d,u)`; skip if `d > dist[u]`.  
   - For each `(v,w)` in adj[u]:  
     - If `d + w < dist[v]`, update and push.  
4. Compute max among `dist[1…N]`.

### C++ Snippet  
```cpp
int networkDelayTime(vector<vector<int>>& times, int N, int K) {
    vector<vector<pair<int,int>>> adj(N+1);
    for (auto &t: times)
        adj[t[0]].push_back({t[1], t[2]});
    const int INF = 1e9;
    vector<int> dist(N+1, INF);
    dist[K] = 0;
    priority_queue<pair<int,int>,
       vector<pair<int,int>>, greater<>> pq;
    pq.push({0, K});
    while (!pq.empty()) {
        auto [d,u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;
        for (auto &[v,w]: adj[u]) {
            if (d + w < dist[v]) {
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }
    int ans = 0;
    for (int i = 1; i <= N; ++i) {
        if (dist[i] == INF) return -1;
        ans = max(ans, dist[i]);
    }
    return ans;
}
```

Time: O(E log V)  
Space: O(E + V)

Advanced Tips:  
- For negative weights use Bellman–Ford (O(VE)); unlikely here.  
- If graph is dense (E ≈ V²), consider using a simple array + O(V²) Dijkstra.

---

## 2. Reconstruct Itinerary  
Link: https://leetcode.com/problems/reconstruct-itinerary/

### Description  
Given `tickets` as pairs `[from, to]`, reconstruct the itinerary starting at `"JFK"` that uses all tickets exactly once and is lexicographically smallest.

### Core Idea: Hierholzer’s Algorithm for Eulerian Path on Directed Graph  
- Build multimap/list sorted by destination.  
- Perform DFS:  
  - While `adj[u]` not empty, pick smallest `v`, remove edge, recurse.  
  - Append `u` to path on backtrack.  
- Reverse path yields itinerary.

### Approach  
1. Build `map<string, multiset<string>> adj`.  
2. Define `dfs(u)`:
   - While `adj[u]` nonempty,  
     - Extract smallest `v`, erase one instance, `dfs(v)`.  
   - `route.push_back(u)`.  
3. Call `dfs("JFK")`, then `reverse(route)`.

### C++ Snippet  
```cpp
vector<string> findItinerary(vector<vector<string>>& tickets) {
    unordered_map<string, multiset<string>> adj;
    for (auto &t: tickets)
        adj[t[0]].insert(t[1]);
    vector<string> route;
    function<void(const string&)> dfs = [&](const string& u) {
        auto &ms = adj[u];
        while (!ms.empty()) {
            string v = *ms.begin();
            ms.erase(ms.begin());
            dfs(v);
        }
        route.push_back(u);
    };
    dfs("JFK");
    reverse(route.begin(), route.end());
    return route;
}
```

Time: O(E log E) for multiset ops  
Space: O(E)

Advanced Tips:  
- Use priority_queue or sort adjacency vectors beforehand.  
- For undirected Eulerian trails, similar approach with care on edge removal.

---

## 3. Min Cost to Connect All Points  
Link: https://leetcode.com/problems/min-cost-to-connect-all-points/

### Description  
Given `points[i] = [xi, yi]`, connect all points with minimum total cost, where cost = Manhattan distance. This is the Minimum Spanning Tree (MST) problem.

### Core Idea: Kruskal’s or Prim’s MST  
- **Prim’s**: Grow tree from any start, maintain min‐heap of crossing edges.  
- **Kruskal’s**: Sort all edges by cost, union‐find to avoid cycles.

### Approach (Prim’s optimized O(N²))  
1. Track `inMST[]`; `minDist[]` = ∞, pick node 0 as start, `minDist[0] = 0`.  
2. For `i = 0…N−1`:
   - Pick `u` not in MST with min `minDist[u]`.  
   - Add cost, mark `inMST[u] = true`.  
   - For each `v` not in MST:  
     - `minDist[v] = min(minDist[v], dist(u,v))`.  
3. Sum of chosen `minDist[u]`.

### C++ Snippet  
```cpp
int minCostConnectPoints(vector<vector<int>>& P) {
    int n = P.size();
    vector<int> minDist(n, INT_MAX);
    vector<bool> inMST(n);
    minDist[0] = 0;
    int total = 0;
    for (int i = 0; i < n; ++i) {
        int u = -1, d = INT_MAX;
        for (int j = 0; j < n; ++j)
            if (!inMST[j] && minDist[j] < d)
                d = minDist[j], u = j;
        total += d;
        inMST[u] = true;
        for (int v = 0; v < n; ++v)
            if (!inMST[v]) {
                int w = abs(P[u][0] - P[v][0])
                      + abs(P[u][1] - P[v][1]);
                minDist[v] = min(minDist[v], w);
            }
    }
    return total;
}
```

Time: O(N²)  
Space: O(N)

Advanced Tips:  
- For large N, use Prim’s with a heap for O(E log V) at E = N².  
- Kruskal’s: generate all edges (N²), sort O(N² log N²) ≈ O(N² log N).

---

## 4. Swim in Rising Water  
Link: https://leetcode.com/problems/swim-in-rising-water/

### Description  
Given `grid[i][j]` as elevation, you start at `(0,0)` when water level = `grid[0][0]`. You can move 4‑ways, but can enter `(i,j)` only when water level ≥ `grid[i][j]`. Return minimum time `t` to reach `(N−1,N−1)`.

### Core Idea: Minimize Maximum Edge in Path  
- Model each move’s cost = max(current path cost, height at neighbor).  
- Run Dijkstra-like on grid: priority by minimal `time`.  
- Alternatively, binary search on time + BFS check.

### Approach (Dijkstra variant)  
1. `dist[r][c] = elevation[0][0]`; use min‐heap `(time, r, c)`.  
2. Pop `(t,r,c)`; for each neighbor `(nr,nc)`:  
   - `nt = max(t, grid[nr][nc])`.  
   - If `nt < dist[nr][nc]`, update & push.  
3. Return `dist[N−1][N−1]`.

### C++ Snippet  
```cpp
int swimInWater(vector<vector<int>>& A) {
    int N = A.size();
    vector<vector<int>> dist(N, vector<int>(N, INT_MAX));
    dist[0][0] = A[0][0];
    priority_queue<array<int,3>,
       vector<array<int,3>>, greater<>> pq;
    pq.push({dist[0][0], 0, 0});
    int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
    while (!pq.empty()) {
        auto [t,r,c] = pq.top(); pq.pop();
        if (r==N-1 && c==N-1) return t;
        if (t > dist[r][c]) continue;
        for (auto &d: dirs) {
            int nr=r+d[0], nc=c+d[1];
            if (nr<0||nr>=N||nc<0||nc>=N) continue;
            int nt = max(t, A[nr][nc]);
            if (nt < dist[nr][nc]) {
                dist[nr][nc] = nt;
                pq.push({nt,nr,nc});
            }
        }
    }
    return -1;
}
```

Time: O(N² log N)  
Space: O(N²)

Advanced Tips:  
- Binary search `t` from [0…maxH], BFS to check reachability in O(N² log H).  
- Union‑find + sort edges by height.

---

## 5. Alien Dictionary  
Link: https://leetcode.com/problems/alien-dictionary/

### Description  
Given lexicographically sorted `words[]` in an unknown language, derive character order. Return one valid order or `""` if impossible (cycle).

### Core Idea: Build Directed Graph + Topological Sort  
1. Compare adjacent words to find first differing char → edge `u→v`.  
2. Build graph and indegree.  
3. Kahn’s BFS or DFS with cycle detection for topo order.

### Approach  
1. Collect unique chars; initialize indegree=0.  
2. For `i=1…m-1`, compare `words[i-1]` & `words[i]`:  
   - On first mismatch `c1≠c2`, add edge `c1→c2`, indegree[c2]++.  
   - If prefix violation (`longer` before its prefix), return `""`.  
3. Topo‐sort:
   - Enqueue zero‐indegree chars.  
   - Dequeue `u`, append to result; decrement neighbors’ indegree, enqueue zeros.  
4. If result length < unique chars, cycle → `""`.

### C++ Snippet  
```cpp
string alienOrder(vector<string>& words) {
    unordered_map<char, vector<char>> adj;
    unordered_map<char,int> indeg;
    for (auto &w: words)
        for (char c: w)
            indeg.emplace(c,0);
    for (int i=1;i<words.size();++i) {
        auto &A=words[i-1], &B=words[i];
        int L=min(A.size(), B.size()), j=0;
        while (j<L && A[j]==B[j]) ++j;
        if (j==L && A.size()>B.size()) return "";
        if (j<L) {
            adj[A[j]].push_back(B[j]);
            indeg[B[j]]++;
        }
    }
    queue<char> q;
    for (auto &p: indeg) if (p.second==0) q.push(p.first);
    string res;
    while (!q.empty()) {
        char u=q.front(); q.pop();
        res.push_back(u);
        for (char v: adj[u]) {
            if (--indeg[v]==0) q.push(v);
        }
    }
    return res.size()==indeg.size() ? res : "";
}
```

Time: O(N × L + V + E)  
Space: O(V + E)

Advanced Tips:  
- DFS‐based topo with color marking can detect cycles on the fly.  
- Multiple valid orders exist—use lexicographically sorted initial zero‐indegree queue for smallest.

---

## 6. Cheapest Flights Within K Stops  
Link: https://leetcode.com/problems/cheapest-flights-within-k-stops/

### Description  
Given `flights[i] = {u, v, w}`, find the cheapest price from `src` to `dst` with at most `K` stops. Return `-1` if no route.

### Core Idea: BFS‐like (Level‐by‐Level) or Modified Bellman–Ford  
- Use DP over stops: `dp[i][u]` = min cost to reach `u` in ≤ i stops. Or  
- Use BFS with state `(node, cost, stops)` and a min‐heap or simple queue.

### Approach (Bellman–Ford variant)  
1. Initialize `dist[]` to ∞; `dist[src] = 0`.  
2. Loop `i = 0…K`:  
   - Copy `tmp = dist`.  
   - For each `(u,v,w)`:  
     - If `dist[u] + w < tmp[v]`, update `tmp[v]`.  
   - `dist = tmp`.  
3. Return `dist[dst] == ∞ ? -1 : dist[dst]`.

### C++ Snippet  
```cpp
int findCheapestPrice(int n, vector<vector<int>>& flights,
                      int src, int dst, int K) {
    const int INF = 1e9;
    vector<int> dist(n, INF), tmp;
    dist[src] = 0;
    for (int i = 0; i <= K; ++i) {
        tmp = dist;
        for (auto &f: flights) {
            int u=f[0], v=f[1], w=f[2];
            if (dist[u] < INF && dist[u] + w < tmp[v]) {
                tmp[v] = dist[u] + w;
            }
        }
        dist.swap(tmp);
    }
    return dist[dst] == INF ? -1 : dist[dst];
}
```

Time: O(K × E)  
Space: O(N)

Advanced Tips:  
- Using a min‐heap with `(cost, node, stops)` and pruning repeated states can be faster on sparse graphs.  
- Watch out for off‐by‐one in stop counting (K stops means K+1 edges).

---

## 🔍 Algorithmic Paradigms  

| Problem                                | Paradigm                    |
|----------------------------------------|-----------------------------|
| Network Delay Time                     | Dijkstra’s SSSP             |
| Reconstruct Itinerary                  | Eulerian Path (Hierholzer) |
| Min Cost to Connect All Points         | MST (Prim/Kruskal)          |
| Swim in Rising Water                   | Minimize Max Edge (Dijkstra)|
| Alien Dictionary                       | Topological Sort            |
| Cheapest Flights Within K Stops        | DP / Bellman–Ford variant   |

---

## 🚀 Advanced Tips & Variants  
- **Multi‐Source SSSP**: For `networkDelayTime` with multiple start nodes, initialize heap with all sources.  
- **Eulerian Trails**: Practice directed vs. undirected; check existence conditions (in/out degrees).  
- **MST on Geometric Graphs**: For `connect all points`, consider Delaunay triangulation to prune edges.  
- **Binary Search + DSU**: Alternative for `swimInWater`: sort cells, union when submerged.  
- **Cycle Detection in Lexicographic Graphs**: For `alienDictionary`, detect prefix conflicts early.  
- **K‐Stop Shortest Path**: Mix BFS layers and distance array; trade off between BFS and DP.

Happy exploring advanced graph algorithms!  