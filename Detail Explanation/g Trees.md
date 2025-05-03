<!--markdownlint-disable-->
# 📚 Trees (Advanced Beginner‑Friendly Master Guide)

This guide preserves the original structure—problem titles, links, and code blocks—while teaching each concept from first principles. For each of the fifteen problems you will find:

1. **Theory**: The underlying idea in plain English.  
2. **Approach Walkthrough**: A small, concrete example you can trace by hand.  
3. **Detailed Code Breakdown**: Every line of the C++ snippet explained in plain English.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  

After the problems, you’ll find:

- **Expanded Helper Functions** with detailed comments and usage notes.  
- **Deep Dive** into core tree‑traversal and reconstruction paradigms.  
- **Next Steps to Mastery** with follow‑up challenges, advanced variants, and debugging checklists.

Let’s begin!

---

## 1. Invert Binary Tree  
Link: https://leetcode.com/problems/invert-binary-tree/

### Theory: Recursion Swaps Subtrees  
Every node’s left and right children can be swapped independently—this naturally fits a **depth‑first** recursion. Swapping at the current node then recursing into both subtrees inverts the entire tree.

### Approach Walkthrough  
Given:
```
    4
   / \
  2   7
 / \ / \
1  3 6  9
```
1. At root 4, swap children → left becomes 7, right becomes 2.  
2. Recurse on the new left subtree (original 7): swap 6↔9.  
3. Recurse on the new right subtree (original 2): swap 1↔3.  
Result:
```
    4
   / \
  7   2
 / \ / \
9  6 3  1
```

### Detailed Code Breakdown  
```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root)                          // 1) Base case: empty node → nothing to invert.
        return nullptr;
    swap(root->left, root->right);      // 2) Swap the left and right child pointers.
    invertTree(root->left);             // 3) Recursively invert the (new) left subtree.
    invertTree(root->right);            // 4) Recursively invert the (new) right subtree.
    return root;                        // 5) Return the root of the inverted subtree.
}
```
- Line 1: Stops recursion at null.  
- Line 2: Built‑in `swap` efficiently exchanges pointers.  
- Lines 3–4: Each subtree is processed similarly.  
- Line 5: Returns the modified root for chaining.

#### Pitfalls & Tips  
- Forgetting the base case leads to null‑pointer dereference.  
- Ensure you swap **before** recursing so you visit original children in any order.

---

## 2. Maximum Depth of Binary Tree  
Link: https://leetcode.com/problems/maximum-depth-of-binary-tree/

### Theory: DFS Height Calculation  
The depth of a tree is one more than the maximum depth of its children. This is a classic **post‑order** recursion: compute child depths first, then add one.

### Approach Walkthrough  
For:
```
   3
  / \
 9  20
    / \
   15  7
```
- Depth at null = 0.  
- Depth at 9 and 15/7 leaves = 1.  
- Depth at node 20 = 1 + max(1,1) = 2.  
- Depth at root = 1 + max(1,2) = 3.

### Detailed Code Breakdown  
```cpp
int maxDepth(TreeNode* root) {
    if (!root)                         // 1) Empty subtree → depth 0.
        return 0;
    int L = maxDepth(root->left);      // 2) Compute left subtree depth.
    int R = maxDepth(root->right);     // 3) Compute right subtree depth.
    return 1 + max(L, R);              // 4) Current depth = max child + 1.
}
```

#### Pitfalls & Tips  
- Always return 0 for null to terminate recursion correctly.  
- Use `max(L, R)` to choose the deeper branch.

---

## 3. Diameter of Binary Tree  
Link: https://leetcode.com/problems/diameter-of-binary-tree/

### Theory: Track Longest Path Through Each Node  
The diameter is the longest path (in edges) between any two nodes. At each node, the longest path through it is `leftDepth + rightDepth`. Track a global maximum as you compute depths.

### Approach Walkthrough  
```
    1
   / \
  2   3
 / \
4   5
```
- Depths: at 4,5,3 = 0; at 2 = 1; at 1’s children depths = (2,1) → diameter candidate = 2+1=3 edges.  
- The global maximum is 3.

### Detailed Code Breakdown  
```cpp
int diameterAns = 0;

int depth(TreeNode* node) {
    if (!node) return 0;  
    int L = depth(node->left);                       // 1) Left subtree depth.
    int R = depth(node->right);                      // 2) Right subtree depth.
    diameterAns = max(diameterAns, L + R);           // 3) Update global diameter.
    return 1 + max(L, R);                            // 4) Return depth including current.
}

int diameterOfBinaryTree(TreeNode* root) {
    diameterAns = 0;                                 // 5) Reset before use.
    depth(root);                                     // 6) Populate diameterAns.
    return diameterAns;                              // 7) Return final answer.
}
```

#### Pitfalls & Tips  
- Remember to reset `diameterAns` if the function is called multiple times.  
- Diameter is in **edges**, so sum depths without adding 1.

---

## 4. Balanced Binary Tree  
Link: https://leetcode.com/problems/balanced-binary-tree/

### Theory: Height Check with Early Termination  
A tree is balanced if at every node, the child depths differ by ≤1. We use recursion that returns `-1` if unbalanced; otherwise the subtree height. If either child returns `-1` or the two heights differ >1, propagate `-1`.

### Approach Walkthrough  
```
    1
   / \
  2   2
 /
3
```
- Left subtree depth = 2, right = 1 → difference =1 → still balanced.  
- Node 2’s left =1, right=0 → difference=1.  
- Balanced overall.

### Detailed Code Breakdown  
```cpp
int checkHeight(TreeNode* node) {
    if (!node) return 0;  
    int L = checkHeight(node->left);   
    if (L < 0) return -1;               // 1) Left unbalanced → propagate.
    int R = checkHeight(node->right);
    if (R < 0) return -1;               // 2) Right unbalanced → propagate.
    if (abs(L - R) > 1) return -1;      // 3) Current node unbalanced.
    return 1 + max(L, R);               // 4) Otherwise return height.
}

bool isBalanced(TreeNode* root) {
    return checkHeight(root) >= 0;      // 5) Non‑negative means balanced.
}
```

#### Pitfalls & Tips  
- Early exit saves time by avoiding full traversal once imbalance is found.  
- Use `abs(L - R)` to compare subtree heights.

---

## 5. Same Tree  
Link: https://leetcode.com/problems/same-tree/

### Theory: Simultaneous Pre‑order DFS  
Two trees are identical if their root values match and their left/right subtrees are also identical. A simple recursive comparison suffices.

### Approach Walkthrough  
```
Tree A:   1       Tree B:   1
         / \              / \
        2   3            2   3
```
Each pair `(1,1)`, `(2,2)`, `(3,3)` match → trees are identical.

### Detailed Code Breakdown  
```cpp
bool isSameTree(TreeNode* p, TreeNode* q) {
    if (!p && !q) return true;          // 1) Both null → identical.
    if (!p || !q || p->val != q->val)    // 2) One null or values differ → false.
        return false;
    // 3) Recurse on left and right subtrees.
    return isSameTree(p->left, q->left)
        && isSameTree(p->right, q->right);
}
```

#### Pitfalls & Tips  
- Check both null before checking values.  
- Use `&&` to ensure both subtrees match.

---

## 6. Subtree of Another Tree  
Link: https://leetcode.com/problems/subtree-of-another-tree/

### Theory: DFS on Main Tree + Same‑Tree Check  
Traverse tree `s`. At each node, call `isSameTree(sNode, t)`. If any match, return true; otherwise continue DFS.

### Approach Walkthrough  
```
s:      3             t: 4
       / \                \
      4   5                1
     / \
    1   2
```
At `s` node 4, `isSameTree(4, t)` returns true.

### Detailed Code Breakdown  
```cpp
bool isSubtree(TreeNode* s, TreeNode* t) {
    if (!s) return false;               // 1) Reached leaf → no match.
    if (isSameTree(s, t))               // 2) Exact match at this root?
        return true;
    // 3) Otherwise search left or right.
    return isSubtree(s->left, t)
        || isSubtree(s->right, t);
}
```

#### Pitfalls & Tips  
- `isSameTree` must handle null pointers correctly.  
- Worst‑case O(m·n); acceptable for typical constraints.

---

## 7. Lowest Common Ancestor of a BST  
Link: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

### Theory: Exploit BST Order Property  
In a BST, left subtree < root < right subtree. If both `p` and `q` are less than root, LCA is in left; if both greater, in right; otherwise root splits them and is LCA.

### Approach Walkthrough  
BST:
```
      6
     / \
    2   8
   / \  /\
  0  4 7 9
       \
        5
```
For `p=2`, `q=8`, root=6 splits them → LCA=6.

### Detailed Code Breakdown  
```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    int rv = root->val, pv = p->val, qv = q->val;
    if (pv < rv && qv < rv)               // 1) Both less → left side.
        return lowestCommonAncestor(root->left, p, q);
    if (pv > rv && qv > rv)               // 2) Both greater → right side.
        return lowestCommonAncestor(root->right, p, q);
    return root;                          // 3) Split point.
}
```

#### Pitfalls & Tips  
- Works only for BST, not general binary trees.  
- Ensure `p` and `q` exist in the tree beforehand.

---

## 8. Binary Tree Level Order Traversal  
Link: https://leetcode.com/problems/binary-tree-level-order-traversal/

### Theory: Breadth‑First Search (BFS) by Level  
Use a queue to process nodes level by level. At each iteration, record the queue size (nodes at current level), dequeue that many, collect values, and enqueue their children.

### Approach Walkthrough  
```
    1
   / \
  2   3
 / \
4   5
```
- Level 1: [1]  
- Level 2: [2,3]  
- Level 3: [4,5]

### Detailed Code Breakdown  
```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> res;
    if (!root) return res;            
    queue<TreeNode*> q;
    q.push(root);                     // 1) Start with the root.
    while (!q.empty()) {
        int sz = q.size();            // 2) Number of nodes in this level.
        vector<int> level;
        for (int i = 0; i < sz; ++i) {
            auto node = q.front(); q.pop();  
            level.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        res.push_back(level);         // 3) Append collected values.
    }
    return res;
}
```

#### Pitfalls & Tips  
- Always capture `q.size()` before the loop.  
- Enqueue children for the next level.

---

## 9. Binary Tree Right Side View  
Link: https://leetcode.com/problems/binary-tree-right-side-view/

### Theory: Level‑Order + Last Node  
Perform BFS by level and record the last node’s value at each level—the node visible from the right side.

### Approach Walkthrough  
```
    1
   / \
  2   3
   \   \
    5   4
```
Levels → last values: [1,3,4].

### Detailed Code Breakdown  
```cpp
vector<int> rightSideView(TreeNode* root) {
    vector<int> ans;
    if (!root) return ans;
    queue<TreeNode*> q; q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; ++i) {
            auto node = q.front(); q.pop();
            if (i == sz - 1)               // 1) Last node in this level?
                ans.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return ans;
}
```

#### Pitfalls & Tips  
- Use `i == sz - 1` to pick the last node.  
- If you record first node instead, you see the left side.

---

## 10. Count Good Nodes in Binary Tree  
Link: https://leetcode.com/problems/count-good-nodes-in-binary-tree/

### Theory: DFS Passing Maximum  
A “good” node has value ≥ all values on the path from the root. As we traverse, carry the maximum seen so far. If current node ≥ that max, increment count.

### Approach Walkthrough  
```
    3
   / \
  1   4
     / \
    1   5
```
Good nodes: 3,4,5 → count=3.

### Detailed Code Breakdown  
```cpp
int countDFS(TreeNode* node, int maxSoFar) {
    if (!node) return 0;
    int cnt = 0;
    if (node->val >= maxSoFar) {     // 1) Good if ≥ maxSoFar.
        cnt = 1;
        maxSoFar = node->val;        // 2) Update maxSoFar.
    }
    cnt += countDFS(node->left, maxSoFar);
    cnt += countDFS(node->right, maxSoFar);
    return cnt;
}

int goodNodes(TreeNode* root) {
    return countDFS(root, INT_MIN);  // 3) Start with smallest possible.
}
```

#### Pitfalls & Tips  
- Pass `maxSoFar` **by value** so each path tracks its own max.  
- Initialize with `INT_MIN` to count the root if desired.

---

## 11. Validate Binary Search Tree  
Link: https://leetcode.com/problems/validate-binary-search-tree/

### Theory: DFS with Range Limits  
Each node must satisfy `lower < node->val < upper`. Recursively pass tightened bounds: left subtree’s upper bound = current value; right subtree’s lower bound = current value.

### Approach Walkthrough  
```
    5
   / \
  1   4
     / \
    3   6
```
Node 3 in right subtree of 5 must be >5 but is not → invalid.

### Detailed Code Breakdown  
```cpp
bool validate(TreeNode* node, long low, long high) {
    if (!node) return true;                       
    if (node->val <= low || node->val >= high)     // 1) Out of bounds?
        return false;
    // 2) Check subtrees with updated ranges.
    return validate(node->left, low, node->val)
        && validate(node->right, node->val, high);
}

bool isValidBST(TreeNode* root) {
    return validate(root, LONG_MIN, LONG_MAX);     // 3) Use wide long range.
}
```

#### Pitfalls & Tips  
- Use `long` to avoid overflow when bounds approach `INT_MIN`/`MAX`.  
- Ensure strictly `<` and `>` comparisons.

---

## 12. Kth Smallest Element in a BST  
Link: https://leetcode.com/problems/kth-smallest-element-in-a-bst/

### Theory: In‑Order Traversal Yields Sorted Sequence  
An in‑order DFS visits nodes in ascending order. Count nodes as you visit; when count == k, record the value and stop.

### Approach Walkthrough  
BST `[3,1,4,null,2]`, `k=1`: in‑order visits 1 first → answer 1.

### Detailed Code Breakdown  
```cpp
int kthVal, countK;

void inorder(TreeNode* node, int k) {
    if (!node || countK >= k) return;      // 1) Stop if found or null.
    inorder(node->left, k);                 
    if (++countK == k) {                   // 2) Check if this is k-th.
        kthVal = node->val;                
        return;
    }
    inorder(node->right, k);                
}

int kthSmallest(TreeNode* root, int k) {
    countK = 0;                            
    inorder(root, k);
    return kthVal;                         // 3) Return recorded value.
}
```

#### Pitfalls & Tips  
- Check `countK >= k` to stop further recursion.  
- Initialize `countK` before traversal.

---

## 13. Construct Binary Tree from Preorder and Inorder  
Link: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

### Theory: Preorder Defines Root; Inorder Splits Subtrees  
The first element of preorder is the root. Find its index in inorder to determine left‑subtree size. Recursively build left and right from the corresponding slices.

### Approach Walkthrough  
`preorder=[3,9,20,15,7]`, `inorder=[9,3,15,20,7]`:

- Root=3; in-order index=1 → left has size 1 (`[9]`), right size 3 (`[15,20,7]`).

### Detailed Code Breakdown  
```cpp
unordered_map<int,int> idxMap;  // value → index in inorder

TreeNode* build(int preL, int preR, int inL, int inR,
                vector<int>& pre, vector<int>& in) {
    if (preL > preR) return nullptr;  
    int rootVal = pre[preL];                 
    int i = idxMap[rootVal];          // 1) Find root in inorder.
    int leftSize = i - inL;
    TreeNode* root = new TreeNode(rootVal);
    // 2) Build left subtree.
    root->left = build(preL+1, preL+leftSize, inL, i-1, pre, in);
    // 3) Build right subtree.
    root->right = build(preL+leftSize+1, preR, i+1, inR, pre, in);
    return root;
}

TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
    int n = preorder.size();
    for (int i = 0; i < n; ++i)
        idxMap[inorder[i]] = i;       // 4) Fill map for O(1) lookups.
    return build(0, n-1, 0, n-1, preorder, inorder);
}
```

#### Pitfalls & Tips  
- Precompute `idxMap` to avoid O(n) scans inside recursion.  
- Carefully compute the indices when slicing.

---

## 14. Binary Tree Maximum Path Sum  
Link: https://leetcode.com/problems/binary-tree-maximum-path-sum/

### Theory: Max Gain from Each Node  
We compute, for each node, the maximum gain if we continue upward through parent: `node->val + max(0, leftGain, rightGain)`. Meanwhile, the path sum passing through this node is `node->val + leftGain + rightGain`. Track the global maximum.

### Approach Walkthrough  
```
   -10
   / \
  9  20
     / \
    15  7
```
- At 15,7 gains =15,7. Path through 20 =20+15+7=42. Global=42.

### Detailed Code Breakdown  
```cpp
int maxSum = INT_MIN;

int maxGain(TreeNode* node) {
    if (!node) return 0;  
    int L = max(maxGain(node->left), 0);  
    int R = max(maxGain(node->right), 0);
    maxSum = max(maxSum, node->val + L + R);  
    return node->val + max(L, R);            
}

int maxPathSum(TreeNode* root) {
    maxSum = INT_MIN;  
    maxGain(root);
    return maxSum;  
}
```

#### Pitfalls & Tips  
- Use `max(..., 0)` to exclude negative contributions.  
- Reset `maxSum` before each call.

---

## 15. Serialize and Deserialize Binary Tree  
Link: https://leetcode.com/problems/serialize-and-deserialize-binary-tree/

### Theory: Preorder with Null Markers  
We serialize via **preorder DFS**, writing `val,` or `#,` for null. Deserialization reads tokens sequentially, reconstructing nodes recursively.

### Approach Walkthrough  
Tree:
```
   1
  / \
 2   3
    / \
   4   5
```
Serialize → `"1,2,#,#,3,4,#,#,5,#,#,"`.

### Detailed Code Breakdown  
```cpp
class Codec {
public:
    string serialize(TreeNode* root) {
        string s;
        function<void(TreeNode*)> dfs = [&](TreeNode* node) {
            if (!node) { s += "#,"; return; }         // 1) Null marker.
            s += to_string(node->val) + ",";         // 2) Node value.
            dfs(node->left);                        
            dfs(node->right);                       
        };
        dfs(root);
        return s;                                    // 3) Return full string.
    }

    TreeNode* deserialize(const string& data) {
        stringstream ss(data);
        string tok;
        function<TreeNode*()> dfs = [&]() {
            if (!getline(ss, tok, ',')) return nullptr; 
            if (tok == "#") 
                return nullptr;                        // 4) Null -> no node.
            TreeNode* node = new TreeNode(stoi(tok)); 
            node->left = dfs();                        
            node->right = dfs();                       
            return node;                               // 5) Return constructed node.
        };
        return dfs();                                 // 6) Kick off recursion.
    }
};
```

#### Pitfalls & Tips  
- Ensure each token read uses `getline` with comma delimiter.  
- Maintain synchronization between serialize and deserialize orders.

---

## 🔧 Expanded Helper Functions  
```cpp
// Definition for binary tree node.
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x): val(x), left(nullptr), right(nullptr) {}
};

// In-order traversal print
void printInorder(TreeNode* root) {
    if (!root) return;
    printInorder(root->left);
    cout << root->val << ' ';
    printInorder(root->right);
}

// Build tree from vector using level-order (for testing)
TreeNode* buildTree(const vector<int>& a) {
    if (a.empty()) return nullptr;
    vector<TreeNode*> nodes(a.size());
    for (int i = 0; i < a.size(); ++i)
        nodes[i] = a[i] >= 0 ? new TreeNode(a[i]) : nullptr;
    for (int i = 0; i < a.size(); ++i) {
        if (!nodes[i]) continue;
        int l = 2*i+1, r = 2*i+2;
        if (l < a.size()) nodes[i]->left = nodes[l];
        if (r < a.size()) nodes[i]->right = nodes[r];
    }
    return nodes[0];
}
```
- Use `-1` or another sentinel to denote null in `buildTree`.  
- Helpful for quick unit tests.

---

## 🔍 Deep Dive into Paradigms

### DFS Recursion  
- Pre‑order: invert, serialize  
- In‑order: validate BST, kth smallest  
- Post‑order: max depth, diameter, balance  

### BFS (Level‑Order)  
- Level traversal, right‑side view  

### Divide & Conquer  
- Reconstruct from traversals  

### State‑Tracking DFS  
- Balanced (–1 flag), good nodes, max path  

### Parametric & Marker‑Based  
- Serialize/Deserialize with `#,` markers  

---

## 🚀 Next Steps to Mastery

### Follow‑Up Challenges  
- Path Sum I/III, Sum of Left Leaves  
- Zigzag Level Order, Vertical Order  
- LCA of Binary Tree (general, not BST)  
- Tree Sequence (BST iterator), Binary Search Tree Iterator  

### Advanced Variants  
- Threaded binary trees, Euler Tour Trees  
- Balanced trees: AVL, Red‑Black, B‑Trees  
- Persistent (immutable) trees, segment trees  

### Debugging Checklist  
1. Always handle null pointers in recursion.  
2. Confirm base cases before recursive calls.  
3. Trace small examples (1–3 nodes).  
4. Reset global state (counters, accumulators).  
5. Compare serialization/deserialization output on diverse shapes.

Happy coding and mastering Trees!  