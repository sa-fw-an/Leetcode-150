<!--markdownlint-disable-->
# 📚 Graphs (Advanced Beginner‑Friendly Master Guide)

This guide elevates thirteen classic LeetCode **Graphs** problems with beginner‑friendly theory, step‑by‑step examples, line‑by‑line code breakdowns, pitfalls, expanded helpers, paradigm deep dives, and practical next steps.

Each problem section includes:
1. **Theory**: Why this algorithm works on graphs.  
2. **Approach Walkthrough**: A small, concrete example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: Detailed explanation of each code section.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.

After the problems, you’ll find:
- **🔧 Expanded Helper Functions**  
- **🔍 Deep Dive into Graph Paradigms**  
- **🚀 Next Steps to Mastery**

---

## 1. Number of Islands  
Link: https://leetcode.com/problems/number-of-islands/

### Theory: DFS Flood‑Fill  
Treat the grid as an undirected graph where each land cell (`'1'`) connects to its 4 neighbors. Each island is a connected component. Depth‑first search (DFS) marks all reachable land.

### Approach Walkthrough  
Grid:
```
1 1 0
0 1 0
1 0 1
```
- Visit (0,0): land → count=1 → DFS marks (0,0),(0,1),(1,1).  
- Continue; skip marked.  
- Visit (2,0): land → count=2 → marks only itself.  
- Visit (2,2): land → count=3.

### Code Breakdown  
```cpp
int numIslands(vector<vector<char>>& grid) {
    int m = grid.size(), n = grid[0].size(), count = 0;
    function<void(int,int)> dfs = [&](int r, int c) {
        // 1) Boundary & water check
        if (r<0||r>=m||c<0||c>=n||grid[r][c]!='1') return;
        grid[r][c] = '0';               // 2) Mark visited
        // 3) Recurse in 4 directions
        dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1);
    };
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (grid[i][j] == '1') {
                ++count;                // 4) New island found
                dfs(i, j);              // 5) Flood‑fill it
            }
        }
    }
    return count;                       // 6) Total islands
}
```
1. Guard against out‑of‑bounds or water.  
2. Mark as visited by flipping to `'0'`.  
3. Explore all neighbors.  
4. Increment count when new land found.  
5. Flood‑fill clears entire component.  
6. Return the final count.

#### Pitfalls & Tips  
- Modifying the input grid is in-place; clone if needed.  
- Use iterative stack if recursion depth is a concern.

---

## 2. Max Area of Island  
Link: https://leetcode.com/problems/max-area-of-island/

### Theory: DFS with Area Accumulation  
Similar to Number of Islands, but DFS returns area of each component. Track the maximum.

### Approach Walkthrough  
Grid:
```
1 1 0
1 0 0
0 0 1
```
- DFS at (0,0) covers three cells → area=3.  
- DFS at (2,2) covers one cell → area=1.  
- Max area = 3.

### Code Breakdown  
```cpp
int maxAreaOfIsland(vector<vector<int>>& g) {
    int m = g.size(), n = g[0].size(), best = 0;
    function<int(int,int)> dfs = [&](int r, int c) {
        // 1) Invalid or water
        if (r<0||r>=m||c<0||c>=n||g[r][c]==0) return 0;
        g[r][c] = 0;                    // 2) Mark visited
        // 3) Count this cell + neighbors
        return 1
             + dfs(r+1,c) + dfs(r-1,c)
             + dfs(r,c+1) + dfs(r,c-1);
    };
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            best = max(best, dfs(i,j)); // 4) Track maximum
        }
    }
    return best;                       // 5) Largest island size
}
```
1. Check boundaries and water.  
2. Mark visited to avoid repeats.  
3. Sum up area recursively.  
4. Compare each component’s area.  
5. Return the maximum.

#### Pitfalls & Tips  
- Reset grid or use visited set for repeated queries.  
- Combine area logic with flood‑fill to avoid extra passes.

---

## 3. Clone Graph  
Link: https://leetcode.com/problems/clone-graph/

### Theory: DFS/BFS with Node Mapping  
Each node in an undirected graph must be cloned exactly once. Use a hash map from original to cloned nodes. Traversal (DFS/BFS) ensures all neighbors are cloned.

### Approach Walkthrough  
Single node 1 with neighbors [2,4]:  
- Visit 1 → clone 1.  
- Visit neighbor 2 → clone 2, link to clone 1.  
- Continue until all nodes cloned.

### Code Breakdown  
```cpp
Node* cloneGraph(Node* node) {
    if (!node) return nullptr;           // 1) Empty graph
    unordered_map<Node*, Node*> mp;      // original → clone
    function<Node*(Node*)> dfs = [&](Node* cur) {
        if (mp.count(cur)) return mp[cur]; 
                                          // 2) Return existing clone
        Node* copy = new Node(cur->val);  
        mp[cur] = copy;                   // 3) Register clone
        for (auto* nei : cur->neighbors) {
            copy->neighbors.push_back(dfs(nei)); 
                                          // 4) Clone neighbors
        }
        return copy;                      // 5) Return this clone
    };
    return dfs(node);                     // 6) Trigger DFS
}
```
1. Handle null input.  
2. Avoid re‑cloning by checking map.  
3. Create and register clone before recursing.  
4. Recursively clone and attach neighbors.  
5. Return the cloned node.  
6. Invoke DFS from the entry node.

#### Pitfalls & Tips  
- Choose DFS or BFS consistently.  
- Use iterative BFS if recursion depth is high.

---

## 4. Walls and Gates  
Link: https://leetcode.com/problems/walls-and-gates/

### Theory: Multi‑Source BFS  
All gates (0) act as BFS sources. Propagate distances outward in waves. This fills each empty room (INF) with its minimum distance to any gate.

### Approach Walkthrough  
Grid:
```
INF  -1  0
INF INF INF
INF  -1 INF
```
- Start BFS from all 0‑cells.  
- Level 1 updates neighbors to 1, then level 2 to 2, etc.

### Code Breakdown  
```cpp
void wallsAndGates(vector<vector<int>>& rooms) {
    int m = rooms.size(), n = rooms[0].size();
    queue<pair<int,int>> q;
    // 1) Enqueue all gates
    for (int i = 0; i < m; ++i)
        for (int j = 0; j < n; ++j)
            if (rooms[i][j] == 0) q.push({i,j});

    int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
    // 2) BFS from all gates simultaneously
    while (!q.empty()) {
        auto [r,c] = q.front(); q.pop();
        for (auto &d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            // 3) Skip if invalid or not INF
            if (nr<0||nr>=m||nc<0||nc>=n
             || rooms[nr][nc] != INT_MAX) continue;
            rooms[nr][nc] = rooms[r][c] + 1; 
                                          // 4) Update distance
            q.push({nr,nc});            // 5) Enqueue for next level
        }
    }
}
```
1. Multi-source BFS initialization.  
2. Expand layer by layer.  
3. Only update INF cells.  
4. Assign distance = parent + 1.  
5. Continue until queue empty.

#### Pitfalls & Tips  
- Use `INT_MAX` for empty rooms, not `-1` or `0`.  
- Avoid scanning for gates within BFS loop.

---

## 5. Rotting Oranges  
Link: https://leetcode.com/problems/rotting-oranges/

### Theory: BFS with Time Levels  
All rotten oranges (2) are BFS sources. Each minute is a BFS layer. Fresh oranges adjacent to rotten become rotten next minute.

### Approach Walkthrough  
Grid:
```
2 1 1
1 1 0
0 1 1
```
- Minute 0: rotten at (0,0).  
- Minute 1: rot neighbors (0,1),(1,0).  
- Continue until all rot or no fresh remain.

### Code Breakdown  
```cpp
int orangesRotting(vector<vector<int>>& g) {
    int m = g.size(), n = g[0].size(), fresh = 0, minutes = 0;
    queue<pair<int,int>> q;
    // 1) Initialize queue & count fresh
    for (int i=0; i<m; ++i) {
        for (int j=0; j<n; ++j) {
            if (g[i][j] == 2) q.push({i,j});
            else if (g[i][j] == 1) fresh++;
        }
    }

    int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
    // 2) BFS minute by minute
    while (!q.empty() && fresh) {
        int sz = q.size(); minutes++;
        for (int k = 0; k < sz; ++k) {
            auto [r,c] = q.front(); q.pop();
            for (auto &d : dirs) {
                int nr = r + d[0], nc = c + d[1];
                // 3) Rot adjacent fresh
                if (nr<0||nr>=m||nc<0||nc>=n|| g[nr][nc] != 1) 
                    continue;
                g[nr][nc] = 2;
                fresh--;
                q.push({nr,nc});
            }
        }
    }
    return fresh ? -1 : minutes;          // 4) Check if any fresh remain
}
```
1. Enqueue initial rotten and count fresh.  
2. Loop while fresh remain.  
3. Spread rot to neighbors.  
4. Return -1 if unreachable fresh, else minutes elapsed.

#### Pitfalls & Tips  
- Count fresh before BFS to stop early.  
- Track levels with queue size.

---

## 6. Pacific Atlantic Water Flow  
Link: https://leetcode.com/problems/pacific-atlantic-water-flow/

### Theory: Reverse Reachability  
Instead of from each cell checking reach to oceans, start BFS/DFS from ocean borders inward. A cell reachable by both searches flows to both oceans.

### Approach Walkthrough  
Height grid:
```
1 2 2
3 2 3
2 4 5
```
- BFS from top/left for Pacific, bottom/right for Atlantic.  
- Intersection of visited sets yields result.

### Code Breakdown  
```cpp
vector<pair<int,int>> pacificAtlantic(vector<vector<int>>& h) {
    int m=h.size(), n=h[0].size();
    vector<vector<bool>> pac(m,vector<bool>(n)),
                         atl(m,vector<bool>(n));
    auto bfs = [&](queue<pair<int,int>>& q, 
                   vector<vector<bool>>& vis) {
        int dirs[4][2]={{1,0},{-1,0},{0,1},{0,-1}};
        while(!q.empty()){
            auto [r,c] = q.front(); q.pop();
            for(auto &d : dirs){
                int nr=r+d[0], nc=c+d[1];
                if(nr<0||nr>=m||nc<0||nc>=n
                 || vis[nr][nc]
                 || h[nr][nc] < h[r][c]) continue;
                vis[nr][nc] = true;
                q.push({nr,nc});
            }
        }
    };
    queue<pair<int,int>> qp, qa;
    // 1) Initialize Pacific border
    for(int i=0;i<m;i++){ pac[i][0]=true; qp.push({i,0}); }
    for(int j=0;j<n;j++){ pac[0][j]=true; qp.push({0,j}); }
    // 2) Initialize Atlantic border
    for(int i=0;i<m;i++){ atl[i][n-1]=true; qa.push({i,n-1}); }
    for(int j=0;j<n;j++){ atl[m-1][j]=true; qa.push({m-1,j}); }

    bfs(qp,pac); bfs(qa,atl);  // 3) Perform BFS for both

    vector<pair<int,int>> res;
    for(int i=0;i<m;i++) for(int j=0;j<n;j++)
        if(pac[i][j] && atl[i][j])
            res.push_back({i,j});   // 4) Collect intersection
    return res;
}
```
1. Mark Pacific edges.  
2. Mark Atlantic edges.  
3. BFS respects non‑decreasing height.  
4. Intersection yields cells flowing to both.

#### Pitfalls & Tips  
- Reverse search avoids expensive per‑cell DFS.  
- Use separate visited arrays.

---

## 7. Surrounded Regions  
Link: https://leetcode.com/problems/surrounded-regions/

### Theory: Border‑Connected Marking  
Regions of `'O'` not touching border are captured. First, mark all `'O'` connected to border as safe (`'#'`), then flip unmarked `'O'` to `'X'`, and restore `'#'`.

### Approach Walkthrough  
Board:
```
X X X X
X O O X
X X O X
X O X X
```
- DFS from border `'O'` at (3,1) marks it.  
- Flip remaining interior `'O'` to `'X'`, restore `'#'` → final.

### Code Breakdown  
```cpp
void solve(vector<vector<char>>& b) {
    int m=b.size(), n=b[0].size();
    function<void(int,int)> dfs = [&](int r,int c) {
        if(r<0||r>=m||c<0||c>=n||b[r][c]!='O') return;
        b[r][c] = '#';                  // 1) Mark safe
        dfs(r+1,c); dfs(r-1,c);
        dfs(r,c+1); dfs(r,c-1);
    };
    // 2) Mark border‑connected O's
    for(int i=0;i<m;i++){ dfs(i,0); dfs(i,n-1); }
    for(int j=0;j<n;j++){ dfs(0,j); dfs(m-1,j); }
    // 3) Flip and restore
    for(int i=0;i<m;i++) for(int j=0;j<n;j++){
        if(b[i][j]=='O') b[i][j]='X';
        else if(b[i][j]=='#') b[i][j]='O';
    }
}
```
1. DFS marks safe `'O'` as `'#'`.  
2. Trigger from all border cells.  
3. Final pass flips unsafe and restores safe.

#### Pitfalls & Tips  
- Be careful to only start DFS from borders.  
- Restore marks in a separate pass.

---

## 8. Course Schedule  
Link: https://leetcode.com/problems/course-schedule/

### Theory: Detect Cycle via Topological Sort (BFS)  
A prerequisite graph is acyclic if you can perform a topological ordering. Kahn’s algorithm uses indegree counts and BFS.

### Approach Walkthrough  
Courses: 2 → [1→0], [2→1]  
- Build indegree: 0:0,1:1,2:1  
- Enqueue 0 → process, decrease indegree of its neighbors.  
- Continue until all processed or stuck.

### Code Breakdown  
```cpp
bool canFinish(int N, vector<vector<int>>& pre) {
    vector<vector<int>> adj(N);
    vector<int> indeg(N,0);
    // 1) Build graph & indegrees
    for (auto& p : pre) {
        adj[p[1]].push_back(p[0]);
        indeg[p[0]]++;
    }
    queue<int> q;
    for (int i = 0; i < N; ++i)
        if (indeg[i] == 0) q.push(i);  // 2) Start with no prerequisites

    int cnt = 0;
    // 3) Process queue
    while (!q.empty()) {
        int u = q.front(); q.pop();
        cnt++;
        for (int v : adj[u])
            if (--indeg[v] == 0)
                q.push(v);
    }
    return cnt == N;  // 4) All courses processed?
}
```
1. Build adjacency list and indegree array.  
2. Initialize queue with zero indegree.  
3. BFS reduces indegree of neighbors.  
4. If all nodes processed, no cycle.

#### Pitfalls & Tips  
- Confirm prerequisites list indices are valid.  
- Use BFS for ordering; DFS with coloring is alternative.

---

## 9. Course Schedule II  
Link: https://leetcode.com/problems/course-schedule-ii/

### Theory: Topological Sort Order Reconstruction  
Same as Course Schedule I, but record the order in which nodes are dequeued.

### Approach Walkthrough  
Use the same graph; maintain a result vector appending each processed node.

### Code Breakdown  
```cpp
vector<int> findOrder(int N, vector<vector<int>>& pre) {
    vector<vector<int>> adj(N);
    vector<int> indeg(N,0);
    for (auto& p : pre) {
        adj[p[1]].push_back(p[0]);
        indeg[p[0]]++;
    }
    queue<int> q;
    for (int i = 0; i < N; ++i)
        if (indeg[i] == 0) q.push(i);

    vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);             // 1) Record ordering
        for (int v : adj[u]) {
            if (--indeg[v] == 0)
                q.push(v);
        }
    }
    // 2) Return order if valid, else empty
    return order.size() == N ? order : vector<int>{};
}
```
1. Append each course when its prerequisites are satisfied.  
2. Validate cycle absence by comparing size.

#### Pitfalls & Tips  
- Duplicate prerequisites may inflate indegree—dedupe input if necessary.

---

## 10. Graph Valid Tree  
Link: https://leetcode.com/problems/graph-valid-tree/

### Theory: Union‑Find for Cycle & Connectivity  
A valid tree has n−1 edges, is connected, and acyclic. Union‑find quickly checks cycles; edge count ensures connectivity.

### Approach Walkthrough  
Edges [[0,1],[1,2],[2,0]] on n=3:  
- Union 0,1; union 1,2; union 2,0 → cycle detected.

### Code Breakdown  
```cpp
int find(vector<int>& p, int x) {
    return p[x] == x ? x : p[x] = find(p, p[x]);
}
bool validTree(int N, vector<vector<int>>& edges) {
    if (edges.size() != N - 1) return false;  // 1) Must have n-1 edges
    vector<int> parent(N);
    iota(parent.begin(), parent.end(), 0);
    for (auto &e : edges) {
        int a = find(parent, e[0]);
        int b = find(parent, e[1]);
        if (a == b) return false;              // 2) Cycle found
        parent[a] = b;                         // 3) Union
    }
    return true;  // 4) No cycle & correct edge count ⇒ tree
}
```
1. Edge count prerequisite.  
2. Detect cycle when two nodes share root.  
3. Union sets otherwise.  
4. Return true when no violations.

#### Pitfalls & Tips  
- Always check `edges.size() == N-1` first.  
- Use path compression for efficiency.

---

## 11. Number of Connected Components in an Undirected Graph  
Link: https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/

### Theory: Union‑Find Component Counting  
Initialize N disjoint sets. Each union reduces component count by one. Remaining count is the number of connected components.

### Approach Walkthrough  
n=5, edges [[0,1],[1,2],[3,4]] → components: {0,1,2}, {3,4} → 2.

### Code Breakdown  
```cpp
int countComponents(int N, vector<vector<int>>& edges) {
    vector<int> p(N);
    iota(p.begin(), p.end(), 0);
    function<int(int)> find = [&](int x) {
        return p[x] == x ? x : p[x] = find(p[x]);
    };
    int cnt = N;                             // 1) Each node is separate
    for (auto &e : edges) {
        int a = find(e[0]), b = find(e[1]);
        if (a != b) { p[a] = b; cnt--; }     // 2) Union reduces count
    }
    return cnt;                             // 3) Remaining components
}
```
1. Initialize each node as its own parent and count.  
2. For each edge, union if roots differ.  
3. Return final count.

#### Pitfalls & Tips  
- Path compression avoids deep chains.  
- Rank optimization can further speed up.

---

## 12. Redundant Connection  
Link: https://leetcode.com/problems/redundant-connection/

### Theory: Detect First Cycle‑Creating Edge  
Iterate edges in order; if union of its endpoints already share a root, it is the redundant connection.

### Approach Walkthrough  
Edges [[1,2],[1,3],[2,3]]:  
- Union 1–2, union 1–3, union 2–3 detects cycle on third edge → return [2,3].

### Code Breakdown  
```cpp
vector<int> findRedundantConnection(vector<vector<int>>& edges) {
    int N = edges.size();
    vector<int> p(N+1);
    iota(p.begin(), p.end(), 0);
    function<int(int)> find = [&](int x) {
        return p[x] == x ? x : p[x] = find(p[x]);
    };
    for (auto &e : edges) {
        int u = find(e[0]), v = find(e[1]);
        if (u == v) return e;               // 1) Found redundant
        p[u] = v;                           // 2) Union
    }
    return {};                             // 3) Should not reach
}
```
1. As soon as `find(u) == find(v)`, return that edge.  
2. Otherwise union them.  
3. Valid input guarantees a cycle, so we always return inside the loop.

#### Pitfalls & Tips  
- Use `N+1` sized parent for 1‑indexed nodes.  
- Return the first offending edge.

---

## 13. Word Ladder  
Link: https://leetcode.com/problems/word-ladder/

### Theory: BFS on Implicit Word Graph  
Each word is a node; an edge exists if two words differ by one letter. BFS finds shortest path length from `beginWord` to `endWord`.

### Approach Walkthrough  
```
begin: "hit"
end:   "cog"
wordList: ["hot","dot","dog","lot","log","cog"]
```
- Level 1: "hit".  
- Level 2: neighbors = ["hot"].  
- Continue until reach "cog". Distance = 5.

### Code Breakdown  
```cpp
int ladderLength(string begin, string end, vector<string>& wl) {
    unordered_set<string> dict(wl.begin(), wl.end());
    if (!dict.count(end)) return 0;          // 1) End must be in list
    queue<pair<string,int>> q;
    q.push({begin,1});                       // 2) Word + distance
    unordered_set<string> vis = {begin};
    int L = begin.size();
    
    while (!q.empty()) {
        auto [word, d] = q.front(); q.pop();
        for (int i = 0; i < L; ++i) {
            string pat = word;               // 3) Generate neighbor
            for (char c = 'a'; c <= 'z'; ++c) {
                pat[i] = c;
                if (!dict.count(pat) || vis.count(pat)) continue;
                if (pat == end) return d + 1; // 4) Found end
                vis.insert(pat);
                q.push({pat, d + 1});        // 5) Enqueue next level
            }
        }
    }
    return 0;                                 // 6) No path found
}
```
1. Ensure `endWord` is valid.  
2. Initialize BFS with `beginWord`.  
3. Generate all one‑letter variations.  
4. Return distance when `endWord` reached.  
5. Mark visited to avoid cycles.  
6. Return 0 if unreachable.

#### Pitfalls & Tips  
- Using pattern approach (precomputed generic states) can optimize multiple queries.  
- Visited set prevents re‑processing.

---

## 🔧 Expanded Helper Functions  
```cpp
// Disjoint Set Union (Union‑Find)
struct DSU {
    vector<int> p, r;
    DSU(int n): p(n), r(n,0) { iota(p.begin(), p.end(), 0); }
    int find(int x) { return p[x]==x? x : p[x]=find(p[x]); }
    bool unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return false;
        if (r[a] < r[b]) swap(a,b);
        p[b] = a;
        if (r[a] == r[b]) r[a]++;
        return true;
    }
};

// 4-direction vectors
int dirs4[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
```
- **DSU** for quick cycle/connectivity checks.  
- **dirs4** for grid traversals.

---

## 🔍 Deep Dive into Graph Paradigms

- **DFS Flood‑Fill**: marks connected components in O(V+E).  
- **Multi‑Source BFS**: computes shortest distances from all sources simultaneously.  
- **Union‑Find**: near‑O(1) union and find for connectivity/cycle detection.  
- **Topological Sort (Kahn’s)**: orders DAGs and detects cycles.  
- **BFS on Implicit Graphs**: Word Ladder uses generated neighbors as edges.  
- **Reverse Reachability**: Pacific‑Atlantic flips direction for efficiency.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- Number of Islands II (dynamic updates)  
- Alien Dictionary (topo sort on characters)  
- Minimum Height Trees (leaf‑peeling BFS)  
- Flight Itinerary (Eulerian path)  
- Cheapest Flights Within K Stops (BFS + DP)

### Advanced Variants & Applications  
- Graphs with weighted edges (Dijkstra, Bellman‑Ford)  
- Maximum flow (Edmonds‑Karp, Dinic)  
- Strongly connected components (Kosaraju, Tarjan)  
- Graph pattern matching and subgraph isomorphism

### Debugging Checklist  
1. **Visited State**: Always mark before enqueue/recursion.  
2. **Edge Cases**: Empty graph, single node, no edges.  
3. **Data Structures**: Ensure DSU parents initialized; queue cleared.  
4. **Performance**: Precompute adjacency or patterns for repeated queries.  
5. **Memory**: Free allocated nodes or large structures when done.

Happy graph solving and exploration!  