<!--markdownlint-disable-->
# 📚 Graphs

This guide covers thirteen classic LeetCode problems under the **Graphs** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Number of Islands  
Link: https://leetcode.com/problems/number-of-islands/

### Description  
Given a 2D grid of `'1'` (land) and `'0'` (water), count the number of connected islands (4‑directional).

### Core Idea: DFS Flood Fill  
1. Iterate each cell; when you see `'1'`, increment count and DFS to mark entire island as `'0'`.  
2. DFS from `(r,c)`: if out‑of‑bounds or not `'1'`, return; else mark visited and recurse on neighbors.

### C++ Snippet  
```cpp
int numIslands(vector<vector<char>>& grid) {
    int m = grid.size(), n = grid[0].size(), count = 0;
    function<void(int,int)> dfs = [&](int r, int c) {
        if (r<0||r>=m||c<0||c>=n||grid[r][c]!='1') return;
        grid[r][c] = '0';
        dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1);
    };
    for (int i=0;i<m;++i) for (int j=0;j<n;++j)
        if (grid[i][j]=='1') {
            ++count;
            dfs(i,j);
        }
    return count;
}
```

---

## 2. Max Area of Island  
Link: https://leetcode.com/problems/max-area-of-island/

### Description  
Find the maximum area (number of cells) of an island in a 2D grid of `'1'`/`'0'`.

### Core Idea: DFS Area Computation  
1. Same flood‑fill as Number of Islands, but return size.  
2. DFS returns `1 + sum(dfs(neighbors))`. Track max.

### C++ Snippet  
```cpp
int maxAreaOfIsland(vector<vector<int>>& g) {
    int m=g.size(), n=g[0].size(), best=0;
    function<int(int,int)> dfs = [&](int r,int c){
        if(r<0||r>=m||c<0||c>=n||g[r][c]==0) return 0;
        g[r][c] = 0;
        return 1 + dfs(r+1,c)+dfs(r-1,c)+dfs(r,c+1)+dfs(r,c-1);
    };
    for(int i=0;i<m;i++) for(int j=0;j<n;j++)
        best = max(best, dfs(i,j));
    return best;
}
```

---

## 3. Clone Graph  
Link: https://leetcode.com/problems/clone-graph/

### Description  
Given a reference to a node in an undirected connected graph, return a deep clone of the graph.

### Core Idea: DFS/BFS with Hash Map  
1. Use a map `old→new`.  
2. DFS(node): if seen, return clone; else create clone, then for each neighbor push `dfs(neighbor)` into `clone->neighbors`.

### C++ Snippet  
```cpp
Node* cloneGraph(Node* node) {
    if(!node) return nullptr;
    unordered_map<Node*,Node*> mp;
    function<Node*(Node*)> dfs = [&](Node* cur){
        if(mp.count(cur)) return mp[cur];
        Node* copy = new Node(cur->val);
        mp[cur] = copy;
        for(auto* nei: cur->neighbors)
            copy->neighbors.push_back(dfs(nei));
        return copy;
    };
    return dfs(node);
}
```

---

## 4. Walls and Gates  
Link: https://leetcode.com/problems/walls-and-gates/

### Description  
Fill each empty room (INF) in a grid with distance to its nearest gate (0). Walls are -1.

### Core Idea: Multi‑Source BFS  
1. Initialize queue with all gates’ positions.  
2. BFS level by level, updating `dist+1` for neighbors that are INF.

### C++ Snippet  
```cpp
void wallsAndGates(vector<vector<int>>& rooms) {
    int m=rooms.size(), n=rooms[0].size();
    queue<pair<int,int>> q;
    for(int i=0;i<m;i++) for(int j=0;j<n;j++)
        if(rooms[i][j]==0) q.push({i,j});
    int dirs[4][2]={{1,0},{-1,0},{0,1},{0,-1}};
    while(!q.empty()) {
        auto [r,c]=q.front(); q.pop();
        for(auto& d:dirs) {
            int nr=r+d[0], nc=c+d[1];
            if(nr<0||nr>=m||nc<0||nc>=n||rooms[nr][nc]!=INT_MAX) continue;
            rooms[nr][nc] = rooms[r][c] + 1;
            q.push({nr,nc});
        }
    }
}
```

---

## 5. Rotting Oranges  
Link: https://leetcode.com/problems/rotting-oranges/

### Description  
In a grid of fresh (1), rotten (2), and empty (0) oranges, each minute rotten ones rot adjacent fresh. Return minutes until all fresh rot, or -1.

### Core Idea: BFS with Time Tracking  
1. Multi‑source BFS from all rotten oranges, track minutes.  
2. For each level, rot neighbors; count fresh.  
3. Return last minute if no fresh remains.

### C++ Snippet  
```cpp
int orangesRotting(vector<vector<int>>& g) {
    int m=g.size(),n=g[0].size(), fresh=0, minutes=0;
    queue<pair<int,int>> q;
    for(int i=0;i<m;i++) for(int j=0;j<n;j++){
        if(g[i][j]==2) q.push({i,j});
        else if(g[i][j]==1) fresh++;
    }
    int dirs[4][2]={{1,0},{-1,0},{0,1},{0,-1}};
    while(!q.empty() && fresh){
        int sz=q.size(); minutes++;
        while(sz--){
            auto [r,c]=q.front(); q.pop();
            for(auto& d:dirs){
                int nr=r+d[0], nc=c+d[1];
                if(nr<0||nr>=m||nc<0||nc>=n||g[nr][nc]!=1) continue;
                g[nr][nc]=2;
                fresh--;
                q.push({nr,nc});
            }
        }
    }
    return fresh? -1 : minutes;
}
```

---

## 6. Pacific Atlantic Water Flow  
Link: https://leetcode.com/problems/pacific-atlantic-water-flow/

### Description  
Given height grid, find coordinates from which water can flow to both Pacific (top/left) and Atlantic (bottom/right) edges.

### Core Idea: Reverse BFS/DFS from Oceans  
1. From all Pacific-border cells, BFS/DFS mark reachable.  
2. From all Atlantic-border cells, mark reachable.  
3. Intersection of both sets is answer.

### C++ Snippet  
```cpp
vector<pair<int,int>> pacificAtlantic(vector<vector<int>>& h) {
    int m=h.size(), n=h[0].size();
    vector<vector<bool>> pac(m,vector<bool>(n)), atl(m,vector<bool>(n));
    auto bfs=[&](queue<pair<int,int>>& q, auto& vis){
        int dirs[4][2]={{1,0},{-1,0},{0,1},{0,-1}};
        while(!q.empty()){
            auto [r,c]=q.front(); q.pop();
            for(auto& d:dirs){
                int nr=r+d[0], nc=c+d[1];
                if(nr<0||nr>=m||nc<0||nc>=n||vis[nr][nc]||h[nr][nc]<h[r][c]) continue;
                vis[nr][nc]=true; q.push({nr,nc});
            }
        }
    };
    queue<pair<int,int>> qp, qa;
    for(int i=0;i<m;i++){
        pac[i][0]=true; qp.push({i,0});
        atl[i][n-1]=true; qa.push({i,n-1});
    }
    for(int j=0;j<n;j++){
        pac[0][j]=true; qp.push({0,j});
        atl[m-1][j]=true; qa.push({m-1,j});
    }
    bfs(qp,pac); bfs(qa,atl);
    vector<pair<int,int>> res;
    for(int i=0;i<m;i++) for(int j=0;j<n;j++)
        if(pac[i][j] && atl[i][j])
            res.push_back({i,j});
    return res;
}
```

---

## 7. Surrounded Regions  
Link: https://leetcode.com/problems/surrounded-regions/

### Description  
Capture all regions of `'O'` not connected to the border by flipping them to `'X'`.

### Core Idea: Border‑Connected DFS/BFS  
1. From every `'O'` on the border, mark connected `'O'` as `'#'`.  
2. After marking, flip remaining `'O'`→`'X'`, `'#'`→`'O'`.

### C++ Snippet  
```cpp
void solve(vector<vector<char>>& b) {
    int m=b.size(),n=b[0].size();
    function<void(int,int)> dfs=[&](int r,int c){
        if(r<0||r>=m||c<0||c>=n||b[r][c]!='O') return;
        b[r][c]='#';
        dfs(r+1,c); dfs(r-1,c); dfs(r,c+1); dfs(r,c-1);
    };
    for(int i=0;i<m;i++){ dfs(i,0); dfs(i,n-1); }
    for(int j=0;j<n;j++){ dfs(0,j); dfs(m-1,j); }
    for(int i=0;i<m;i++) for(int j=0;j<n;j++){
        if(b[i][j]=='O') b[i][j]='X';
        else if(b[i][j]=='#') b[i][j]='O';
    }
}
```

---

## 8. Course Schedule  
Link: https://leetcode.com/problems/course-schedule/

### Description  
Given `numCourses` and prerequisites edges, determine if you can finish all courses (i.e., graph is acyclic).

### Core Idea: Topological Sort (BFS Kahn’s) or DFS Cycle Detect  
- **BFS**: Compute indegree, enqueue zero‑indegree nodes, process and decrement neighbors. If processed count equals courses, no cycle.

### C++ Snippet (BFS)  
```cpp
bool canFinish(int N, vector<vector<int>>& pre) {
    vector<vector<int>> adj(N);
    vector<int> indeg(N,0);
    for(auto& p:pre){
        adj[p[1]].push_back(p[0]);
        indeg[p[0]]++;
    }
    queue<int> q;
    for(int i=0;i<N;i++) if(indeg[i]==0) q.push(i);
    int cnt=0;
    while(!q.empty()){
        int u=q.front(); q.pop();
        cnt++;
        for(int v:adj[u])
            if(--indeg[v]==0) q.push(v);
    }
    return cnt==N;
}
```

---

## 9. Course Schedule II  
Link: https://leetcode.com/problems/course-schedule-ii/

### Description  
Return a valid ordering of courses or empty if impossible.

### Core Idea: Topological Sort (BFS or DFS)  
- Use Kahn’s BFS; collect nodes in order as you dequeue.

### C++ Snippet (BFS)  
```cpp
vector<int> findOrder(int N, vector<vector<int>>& pre) {
    vector<vector<int>> adj(N);
    vector<int> indeg(N,0);
    for(auto& p:pre){
        adj[p[1]].push_back(p[0]);
        indeg[p[0]]++;
    }
    queue<int> q;
    for(int i=0;i<N;i++) if(indeg[i]==0) q.push(i);
    vector<int> order;
    while(!q.empty()){
        int u=q.front(); q.pop();
        order.push_back(u);
        for(int v:adj[u])
            if(--indeg[v]==0) q.push(v);
    }
    return order.size()==N? order: vector<int>{};
}
```

---

## 10. Graph Valid Tree  
Link: https://leetcode.com/problems/graph-valid-tree/

### Description  
Check if an undirected graph with `n` nodes and edges is a valid tree: connected and acyclic.

### Core Idea: Union‑Find or BFS + Edge Count  
- **Union‑Find**: If any union merges two in the same set → cycle. Finally, check single set and edges == n−1.

### C++ Snippet (Union‑Find)  
```cpp
int find(vector<int>& p, int x){ return p[x]==x?x:p[x]=find(p,p[x]); }
bool validTree(int N, vector<vector<int>>& edges) {
    if(edges.size()!=N-1) return false;
    vector<int> parent(N);
    iota(parent.begin(), parent.end(), 0);
    for(auto&e:edges){
        int a=find(parent,e[0]), b=find(parent,e[1]);
        if(a==b) return false;
        parent[a]=b;
    }
    return true;
}
```

---

## 11. Number of Connected Components in an Undirected Graph  
Link: https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph/

### Description  
Count connected components in an undirected graph of `n` nodes and edges.

### Core Idea: Union‑Find or DFS/BFS  
- **Union‑Find**: Union all edges, count unique roots.

### C++ Snippet (Union‑Find)  
```cpp
int countComponents(int N, vector<vector<int>>& edges) {
    vector<int> p(N);
    iota(p.begin(),p.end(),0);
    function<int(int)> find=[&](int x){
        return p[x]==x?x:p[x]=find(p[x]);
    };
    int cnt=N;
    for(auto&e:edges){
        int a=find(e[0]), b=find(e[1]);
        if(a!=b){ p[a]=b; cnt--; }
    }
    return cnt;
}
```

---

## 12. Redundant Connection  
Link: https://leetcode.com/problems/redundant-connection/

### Description  
Given a tree with one extra edge, return the redundant edge that creates a cycle.

### Core Idea: Union‑Find Cycle Detection  
1. Iterate edges; if union finds same root, that edge is redundant.

### C++ Snippet  
```cpp
vector<int> findRedundantConnection(vector<vector<int>>& edges) {
    int N = edges.size();
    vector<int> p(N+1);
    iota(p.begin(),p.end(),0);
    function<int(int)> find=[&](int x){
        return p[x]==x?x:p[x]=find(p[x]);
    };
    for(auto& e: edges){
        int u=find(e[0]), v=find(e[1]);
        if(u==v) return e;
        p[u]=v;
    }
    return {};
}
```

---

## 13. Word Ladder  
Link: https://leetcode.com/problems/word-ladder/

### Description  
Given `beginWord`, `endWord`, and a word list, return the length of shortest transformation sequence changing one letter at a time.

### Core Idea: BFS on Word Graph  
1. Preprocess patterns (e.g. `h*t`) mapping to words.  
2. BFS from `beginWord`, for each word generate patterns, enqueue unvisited neighbors until `endWord`.

### C++ Snippet  
```cpp
int ladderLength(string begin, string end, vector<string>& wl) {
    unordered_set<string> dict(wl.begin(), wl.end());
    if(!dict.count(end)) return 0;
    queue<pair<string,int>> q;
    q.push({begin,1});
    unordered_set<string> vis;
    vis.insert(begin);
    int L = begin.size();
    while(!q.empty()){
        auto [word, d] = q.front(); q.pop();
        for(int i=0;i<L;i++){
            string pat = word;
            for(char c='a';c<='z';c++){
                pat[i]=c;
                if(!dict.count(pat) || vis.count(pat)) continue;
                if(pat==end) return d+1;
                vis.insert(pat);
                q.push({pat,d+1});
            }
        }
    }
    return 0;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Union-Find DSU
struct DSU {
    vector<int> p, r;
    DSU(int n): p(n), r(n,0) { iota(p.begin(),p.end(),0); }
    int find(int x){ return p[x]==x?x:p[x]=find(p[x]); }
    bool unite(int a, int b){
        a=find(a); b=find(b);
        if(a==b) return false;
        if(r[a]<r[b]) swap(a,b);
        p[b]=a;
        if(r[a]==r[b]) r[a]++;
        return true;
    }
};

// Direction vectors
int dirs4[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
```

---

## 🔍 Algorithmic Paradigms  
- DFS Flood‑Fill (Number/Max Area of Islands, Surrounded Regions)  
- Multi‑Source BFS (Walls & Gates, Rotting Oranges, Pacific‑Atlantic)  
- Graph Clone & Traversal (Clone Graph)  
- Topological Sort (Course Schedule I/II)  
- Union‑Find (Valid Tree, Connected Components, Redundant Edge)  
- BFS on Implicit Graph (Word Ladder)  

### Complexity Summary  
| Problem                                              | Time                           | Space                       |
|------------------------------------------------------|--------------------------------|-----------------------------|
| Number of Islands / Max Area of Island               | O(m·n)                         | O(m·n) recursion            |
| Clone Graph                                          | O(V+E)                         | O(V)                        |
| Walls and Gates                                      | O(m·n)                         | O(m·n) queue                |
| Rotting Oranges                                      | O(m·n)                         | O(m·n) queue                |
| Pacific Atlantic Water Flow                          | O(m·n)                         | O(m·n) vis arrays + queue   |
| Surrounded Regions                                   | O(m·n)                         | O(m·n) recursion            |
| Course Schedule I/II                                 | O(V+E)                         | O(V+E)                      |
| Graph Valid Tree / Connected Components / Redundant  | O(E·α(N))                      | O(N) DSU                    |
| Word Ladder                                          | O(M·26·L) L=word length        | O(M·L)                      |

---

## 🚀 Advanced Tips & Practice  

- **Grid BFS/DFS**: Always mark visited **before** enqueuing/recursing to avoid repeats.  
- **Union‑Find**: Path compression + union by rank yield near‑O(1) ops.  
- **Topo Sort vs. DFS Cycle**: Kahn’s algorithm gives ordering; DFS detect cycle with color marking.  
- **Implicit Graphs**: For Word Ladder, generating patterns on the fly trades memory for speed.  
- **Follow‑Up Problems**  
  - Number of Islands II (dynamic additions)  
  - Alien Dictionary (topo sort on char graph)  
  - Minimum Height Trees (BFS peel)  
  - Flight Itineraries (Eulerian path)  
  - Cheapest Flights Within K Stops (BFS + DP)  

Happy graph traversals and transformations!  