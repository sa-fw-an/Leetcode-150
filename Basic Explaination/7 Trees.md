<!--markdownlint-disable-->
# 📚 Trees

This guide covers fifteen classic LeetCode problems under the **Trees** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with detailed inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Invert Binary Tree  
Link: https://leetcode.com/problems/invert-binary-tree/

### Description  
Given the root of a binary tree, swap every node’s left and right children to invert the tree.

### Core Idea: Recursion (DFS)  
1. Base case: if node is null, return.  
2. Swap left and right pointers.  
3. Recursively invert left subtree and right subtree.

### C++ Snippet  
```cpp
// Inverts a binary tree in-place using DFS recursion.
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;            // base case
    // swap children
    swap(root->left, root->right);
    // recurse on subtrees
    invertTree(root->left);
    invertTree(root->right);
    return root;
}
```

---

## 2. Maximum Depth of Binary Tree  
Link: https://leetcode.com/problems/maximum-depth-of-binary-tree/

### Description  
Return the length of the longest path from the root down to any leaf.

### Core Idea: Depth‑First Search  
1. If node is null, depth = 0.  
2. Depth = 1 + max(depth(left), depth(right)).

### C++ Snippet  
```cpp
// Computes the maximum depth of a binary tree.
int maxDepth(TreeNode* root) {
    if (!root) return 0;                  // empty tree
    // get depths of subtrees
    int L = maxDepth(root->left);
    int R = maxDepth(root->right);
    return 1 + max(L, R);                 // add current level
}
```

---

## 3. Diameter of Binary Tree  
Link: https://leetcode.com/problems/diameter-of-binary-tree/

### Description  
The diameter is the length (edges) of the longest path between any two nodes.

### Core Idea: Post‑order DFS with Tracking  
1. For each node, compute left and right depths.  
2. Update global diameter = max(diameter, L + R).  
3. Return 1 + max(L, R) to parent.

### C++ Snippet  
```cpp
int diameterAns = 0;
int depth(TreeNode* node) {
    if (!node) return 0;
    int L = depth(node->left);
    int R = depth(node->right);
    diameterAns = max(diameterAns, L + R); // path through this node
    return 1 + max(L, R);
}
int diameterOfBinaryTree(TreeNode* root) {
    depth(root);
    return diameterAns;
}
```

---

## 4. Balanced Binary Tree  
Link: https://leetcode.com/problems/balanced-binary-tree/

### Description  
A binary tree is balanced if for every node, the depths of left and right subtrees differ by at most 1.

### Core Idea: DFS with Early Exit  
1. Return height or –1 if subtree unbalanced.  
2. Recursively get heights of left and right.  
3. If any is –1 or |L–R|>1, return –1.  
4. Else return 1 + max(L,R).

### C++ Snippet  
```cpp
int checkHeight(TreeNode* node) {
    if (!node) return 0;
    int L = checkHeight(node->left);
    if (L < 0) return -1;               // left unbalanced
    int R = checkHeight(node->right);
    if (R < 0) return -1;               // right unbalanced
    if (abs(L - R) > 1) return -1;      // current unbalanced
    return 1 + max(L, R);
}
bool isBalanced(TreeNode* root) {
    return checkHeight(root) >= 0;
}
```

---

## 5. Same Tree  
Link: https://leetcode.com/problems/same-tree/

### Description  
Check if two binary trees are structurally identical and values match.

### Core Idea: Simultaneous DFS  
1. If both nodes null, true.  
2. If one is null or values differ, false.  
3. Recurse on left and right pairs.

### C++ Snippet  
```cpp
// Determines whether two trees are identical.
bool isSameTree(TreeNode* p, TreeNode* q) {
    if (!p && !q) return true;
    if (!p || !q || p->val != q->val) return false;
    return isSameTree(p->left, q->left)
        && isSameTree(p->right, q->right);
}
```

---

## 6. Subtree of Another Tree  
Link: https://leetcode.com/problems/subtree-of-another-tree/

### Description  
Check if tree `t` is a subtree of `s`.

### Core Idea: DFS + Same‑Tree Check  
1. Traverse `s` in DFS.  
2. At each node, if `isSameTree(node, t)` is true, return true.  
3. Else recurse on left and right.

### C++ Snippet  
```cpp
bool isSubtree(TreeNode* s, TreeNode* t) {
    if (!s) return false;
    if (isSameTree(s, t)) return true;   // match at this root
    return isSubtree(s->left, t)
        || isSubtree(s->right, t);
}
```

---

## 7. Lowest Common Ancestor of a BST  
Link: https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

### Description  
In a BST, find the lowest node that has both `p` and `q` as descendants.

### Core Idea: BST Property  
1. If both `p` and `q` values < root, recurse left.  
2. If both > root, recurse right.  
3. Else root is LCA.

### C++ Snippet  
```cpp
// Finds LCA in a BST using value comparisons.
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    int rv = root->val, pv = p->val, qv = q->val;
    if (pv < rv && qv < rv)
        return lowestCommonAncestor(root->left, p, q);
    if (pv > rv && qv > rv)
        return lowestCommonAncestor(root->right, p, q);
    return root;                          // split point
}
```

---

## 8. Binary Tree Level Order Traversal  
Link: https://leetcode.com/problems/binary-tree-level-order-traversal/

### Description  
Return node values level by level from top to bottom.

### Core Idea: BFS with Queue  
1. Use a queue, push root.  
2. While not empty, process current level size, collect values, enqueue children.

### C++ Snippet  
```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> res;
    if (!root) return res;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        vector<int> level;
        for (int i = 0; i < sz; ++i) {
            auto node = q.front(); q.pop();
            level.push_back(node->val);
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        res.push_back(level);
    }
    return res;
}
```

---

## 9. Binary Tree Right Side View  
Link: https://leetcode.com/problems/binary-tree-right-side-view/

### Description  
Return the values visible when looking at the tree from the right side.

### Core Idea: BFS and capture last of each level  
1. Perform level order traversal.  
2. For each level, record the last node’s value.

### C++ Snippet  
```cpp
vector<int> rightSideView(TreeNode* root) {
    vector<int> ans;
    if (!root) return ans;
    queue<TreeNode*> q; q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        for (int i = 0; i < sz; ++i) {
            auto node = q.front(); q.pop();
            if (i == sz - 1) ans.push_back(node->val); // last in level
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return ans;
}
```

---

## 10. Count Good Nodes in Binary Tree  
Link: https://leetcode.com/problems/count-good-nodes-in-binary-tree/

### Description  
A node is “good” if on the path from root to that node, no node has a greater value.

### Core Idea: DFS with Max‑So‑Far  
1. Pass down the maximum value seen so far.  
2. If `node->val >= maxSoFar`, increment count.  
3. Update `maxSoFar = max(maxSoFar, node->val)` and recurse.

### C++ Snippet  
```cpp
int countDFS(TreeNode* node, int maxSoFar) {
    if (!node) return 0;
    int cnt = 0;
    if (node->val >= maxSoFar) {
        cnt = 1;                          // this node is good
        maxSoFar = node->val;            // update max
    }
    cnt += countDFS(node->left, maxSoFar);
    cnt += countDFS(node->right, maxSoFar);
    return cnt;
}
int goodNodes(TreeNode* root) {
    return countDFS(root, INT_MIN);
}
```

---

## 11. Validate Binary Search Tree  
Link: https://leetcode.com/problems/validate-binary-search-tree/

### Description  
Check if a binary tree is a valid BST: left < node < right for every subtree.

### Core Idea: DFS with Range Limits  
1. Pass down `lower` and `upper` bounds.  
2. For each node, ensure `lower < val < upper`.  
3. Recurse left with updated `upper = val`, and right with `lower = val`.

### C++ Snippet  
```cpp
bool validate(TreeNode* node, long low, long high) {
    if (!node) return true;
    if (node->val <= low || node->val >= high) return false;
    return validate(node->left, low, node->val)
        && validate(node->right, node->val, high);
}
bool isValidBST(TreeNode* root) {
    return validate(root, LONG_MIN, LONG_MAX);
}
```

---

## 12. Kth Smallest Element in a BST  
Link: https://leetcode.com/problems/kth-smallest-element-in-a-bst/

### Description  
Find the k-th smallest element in a BST.

### Core Idea: In-order DFS  
1. In-order traversal yields sorted values.  
2. Maintain a counter; when it reaches k, record the node’s value.

### C++ Snippet  
```cpp
int kthVal, countK;
void inorder(TreeNode* node, int k) {
    if (!node || countK >= k) return;
    inorder(node->left, k);
    if (++countK == k) {
        kthVal = node->val;             // found k-th
        return;
    }
    inorder(node->right, k);
}
int kthSmallest(TreeNode* root, int k) {
    countK = 0;
    inorder(root, k);
    return kthVal;
}
```

---

## 13. Construct Binary Tree from Preorder and Inorder  
Link: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

### Description  
Given preorder and inorder traversals, rebuild the binary tree.

### Core Idea: Divide & Conquer  
1. Preorder’s first element is root.  
2. Find root index in inorder to split left/right sizes.  
3. Recursively build left subtree from next preorder slice, and right subtree.

### C++ Snippet  
```cpp
unordered_map<int,int> idxMap;
TreeNode* build(int preL, int preR, int inL, int inR,
                vector<int>& pre, vector<int>& in) {
    if (preL > preR) return nullptr;
    int rootVal = pre[preL];
    int i = idxMap[rootVal];            // index in inorder
    int leftSize = i - inL;
    TreeNode* root = new TreeNode(rootVal);
    // build left
    root->left = build(preL+1, preL+leftSize, inL, i-1, pre, in);
    // build right
    root->right = build(preL+leftSize+1, preR, i+1, inR, pre, in);
    return root;
}
TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
    int n = preorder.size();
    for (int i = 0; i < n; ++i)
        idxMap[inorder[i]] = i;
    return build(0, n-1, 0, n-1, preorder, inorder);
}
```

---

## 14. Binary Tree Maximum Path Sum  
Link: https://leetcode.com/problems/binary-tree-maximum-path-sum/

### Description  
Find the maximum path sum (can start/end anywhere) in the tree.

### Core Idea: DFS with Max‑Gain  
1. Define `maxGain(node)` = max gain from this node to any leaf.  
2. Compute leftGain = max(maxGain(left), 0), same for right.  
3. Update global `res = max(res, node->val + leftGain + rightGain)`.  
4. Return `node->val + max(leftGain, rightGain)`.

### C++ Snippet  
```cpp
int maxSum = INT_MIN;
int maxGain(TreeNode* node) {
    if (!node) return 0;
    int L = max(maxGain(node->left), 0);  
    int R = max(maxGain(node->right), 0);
    maxSum = max(maxSum, node->val + L + R); // path through node
    return node->val + max(L, R);             // extend to parent
}
int maxPathSum(TreeNode* root) {
    maxGain(root);
    return maxSum;
}
```

---

## 15. Serialize and Deserialize Binary Tree  
Link: https://leetcode.com/problems/serialize-and-deserialize-binary-tree/

### Description  
Convert a binary tree to a string and back.

### Core Idea: Preorder with Null Markers  
- **Serialize**: DFS preorder, append value or "#" for null, separated by commas.  
- **Deserialize**: Split by commas, use queue of tokens, build tree recursively.

### C++ Snippet  
```cpp
class Codec {
public:
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        string s;
        function<void(TreeNode*)> dfs = [&](TreeNode* node) {
            if (!node) { s += "#,"; return; }
            s += to_string(node->val) + ",";
            dfs(node->left);
            dfs(node->right);
        };
        dfs(root);
        return s;
    }
    // Decodes your encoded data to tree.
    TreeNode* deserialize(const string& data) {
        stringstream ss(data);
        string tok;
        function<TreeNode*()> dfs = [&]() {
            if (!getline(ss, tok, ',')) return nullptr;
            if (tok == "#") return nullptr;
            TreeNode* node = new TreeNode(stoi(tok));
            node->left = dfs();
            node->right = dfs();
            return node;
        };
        return dfs();
    }
};
```

---

## 🔧 Common Helper Functions  
```cpp
// Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x): val(x), left(nullptr), right(nullptr) {}
};

// Utility to print inorder traversal
void printInorder(TreeNode* root) {
    if (!root) return;
    printInorder(root->left);
    cout << root->val << ' ';
    printInorder(root->right);
}
```

---

## 🔍 Algorithmic Paradigms  
- **DFS Recursion**: invert, max depth, diameter, same tree, subtree, max path, good nodes  
- **Divide & Conquer**: build tree from traversals  
- **BFS (Level Order)**: level order, right side view  
- **Two‑Pointer in Recursion**: validate BST, LCA in BST  
- **State‑Tracking DFS**: balanced tree, good nodes, max path sum  
- **Serialization/Deserialization**: token-based preorder  

### Complexity Summary  
| Problem                                         | Time               | Space                |
|-------------------------------------------------|--------------------|----------------------|
| Invert Binary Tree                              | O(n)               | O(n) recursion       |
| Maximum Depth                                   | O(n)               | O(n) recursion       |
| Diameter of Binary Tree                         | O(n)               | O(n) recursion       |
| Balanced Binary Tree                            | O(n)               | O(n) recursion       |
| Same Tree                                       | O(n)               | O(n) recursion       |
| Subtree of Another Tree                         | O(m·n)             | O(n) recursion       |
| LCA of BST                                      | O(h)               | O(h) recursion       |
| Level Order Traversal                           | O(n)               | O(n) queue           |
| Right Side View                                 | O(n)               | O(n) queue           |
| Count Good Nodes                                | O(n)               | O(n) recursion       |
| Validate BST                                    | O(n)               | O(n) recursion       |
| Kth Smallest in BST                             | O(h + k)           | O(h) recursion       |
| Build Tree from Preorder & Inorder             | O(n)               | O(n) map + recursion |
| Binary Tree Maximum Path Sum                    | O(n)               | O(n) recursion       |
| Serialize/Deserialize Binary Tree               | O(n)               | O(n) string + recursion |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Master basic DFS on trees, swapping pointers, tracking depth.  
- **Medium**: Combine DFS with global state or limits (diameter, good nodes, validate BST).  
- **Hard**: Use recursion with maps (reconstruction), or two‑pass algorithms (serialize/deserialize).

### Follow‑Up Problems  
- Binary Tree Zigzag Level Order, Path Sum, Sum of Left Leaves  
- Lowest Common Ancestor of a Binary Tree (not BST)  
- Vertical Order Traversal, Binary Tree Cameras  
- Serialize/Deserialize N‑ary Tree, Threaded Binary Tree  

### Common Pitfalls & Debugging  
- Forgetting to handle null pointers in recursion.  
- Off‑by‑one when computing diameters or path sums.  
- Using global variables without resetting between calls.  
- Mismanaging string streams in serialization/deserialization.  

Happy coding and mastering Trees!  