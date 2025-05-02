<!--markdownlint-disable-->
# 📚 Tries

This guide covers three LeetCode problems under the **Tries** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with detailed inline comments  
4. A “Common Helper Functions” section  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Implement Trie (Prefix Tree)  
Link: https://leetcode.com/problems/implement-trie-prefix-tree/

### Description  
Design a data structure that supports inserting words, searching for exact matches, and checking for prefixes.

### Core Idea: Fixed‑Size Child Array + End‑Of‑Word Flag  
1. **Node Structure**: Each node has an array of 26 child pointers (one per lowercase letter) and a boolean `isEnd`.  
2. **Insert**: Walk down the tree, creating nodes as needed for each character, then mark the last node’s `isEnd = true`.  
3. **Search**: Traverse the tree for each character; if any child is missing, the word doesn’t exist. At the end, return `isEnd`.  
4. **StartsWith**: Same as search, but ignore `isEnd`—only care that the prefix path exists.

### C++ Code Snippet: Trie Implementation  
```cpp
// Implements a Trie (prefix tree) supporting insert, search, and prefix check.
class Trie {
    // Node structure for each character
    struct Node {
        Node* children[26];     // pointers to next letters
        bool isEnd;             // true if this node marks end of a word
        Node() : isEnd(false) {
            // initialize all children to nullptr
            for (auto &p : children) p = nullptr;
        }
    };
    
    Node* root;  // root of the trie

public:
    Trie() {
        root = new Node();      // empty root
    }
    
    // Insert a word into the trie.
    void insert(const string& word) {
        Node* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            // if the child doesn't exist, create it
            if (!cur->children[idx]) {
                cur->children[idx] = new Node();
            }
            cur = cur->children[idx];  // move to the child
        }
        cur->isEnd = true;       // mark end of word
    }
    
    // Returns true if the exact word is in the trie.
    bool search(const string& word) {
        Node* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!cur->children[idx]) {
                return false;     // missing path ⇒ not found
            }
            cur = cur->children[idx];
        }
        return cur->isEnd;       // only true if end-of-word
    }
    
    // Returns true if there is any word in the trie that starts with the given prefix.
    bool startsWith(const string& prefix) {
        Node* cur = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!cur->children[idx]) {
                return false;     // missing path ⇒ no such prefix
            }
            cur = cur->children[idx];
        }
        return true;             // path exists
    }
};
```

---

## 2. Design Add and Search Words Data Structure  
Link: https://leetcode.com/problems/design-add-and-search-words-data-structure/

### Description  
Extend a trie to support wildcard search: the `'.'` character can match any single letter.

### Core Idea: DFS Over Wildcard Branches  
1. **Insert**: Same as a standard trie.  
2. **Search with '.'**: Perform a recursive DFS:
   - If the current character is not `'.'`, follow the corresponding child.  
   - If it is `'.'`, try **all** non‑null children and recurse.  
   - At the end, return true only if you reach a node with `isEnd = true`.

### C++ Code Snippet: Wildcard Search Trie  
```cpp
// Extends Trie to support '.' wildcard in search.
class WordDictionary {
    struct Node {
        Node* children[26];
        bool isEnd;
        Node() : isEnd(false) {
            for (auto &p : children) p = nullptr;
        }
    };
    Node* root;

    // Helper DFS to support wildcard '.'
    bool dfsSearch(const string& word, int pos, Node* cur) {
        if (!cur) return false;              // dead end
        if (pos == word.size()) {
            return cur->isEnd;               // end-of-word check
        }
        char c = word[pos];
        if (c == '.') {
            // try every possible child
            for (auto child : cur->children) {
                if (dfsSearch(word, pos + 1, child)) {
                    return true;
                }
            }
            return false;
        } else {
            int idx = c - 'a';
            return dfsSearch(word, pos + 1, cur->children[idx]);
        }
    }

public:
    WordDictionary() {
        root = new Node();
    }
    
    // Adds a word into the data structure.
    void addWord(const string& word) {
        Node* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!cur->children[idx]) {
                cur->children[idx] = new Node();
            }
            cur = cur->children[idx];
        }
        cur->isEnd = true;
    }
    
    // Searches for a word or pattern (with '.' wildcard).
    bool search(const string& word) {
        return dfsSearch(word, 0, root);
    }
};
```

---

## 3. Word Search II  
Link: https://leetcode.com/problems/word-search-ii/

### Description  
Given an `m×n` board of letters and a list of words, return all words that can be formed by tracing adjacent cells (horizontally or vertically) without reusing a cell.

### Core Idea: Build Trie + DFS Pruning  
1. **Build Trie of all words**: Each terminal node stores `word` (non‑empty) when insertion finishes.  
2. **DFS on Board**:
   - Start a DFS from each cell.  
   - At each step, check if current character exists in the trie node’s children.  
   - If you reach a node with `word` non‑empty, add it to results and clear `node->word` to avoid duplicates.  
   - Mark the board cell as visited (`'#'`), recurse in 4 directions, then restore it.
3. **Pruning**: If a character is not in the trie path, abort that branch immediately.

### C++ Code Snippet: Word Search II with Trie  
```cpp
// Finds all words in board using a trie for all patterns.
class Solution {
    struct Node {
        Node* children[26];
        string word;           // non-empty if node ends a word
        Node() : word("") {
            for (auto &c : children) c = nullptr;
        }
    };
    
    // Build trie from list of words
    Node* buildTrie(const vector<string>& words) {
        Node* root = new Node();
        for (auto &w : words) {
            Node* cur = root;
            for (char c : w) {
                int idx = c - 'a';
                if (!cur->children[idx]) {
                    cur->children[idx] = new Node();
                }
                cur = cur->children[idx];
            }
            cur->word = w;   // store word at end node
        }
        return root;
    }
    
    // DFS from board[r][c]
    void dfs(vector<vector<char>>& board, int r, int c, Node* node, vector<string>& res) {
        char ch = board[r][c];
        int idx = ch - 'a';
        Node* next = node->children[idx];
        if (!next) return;          // no matching prefix
        if (!next->word.empty()) {
            res.push_back(next->word);
            next->word.clear();     // de‑duplicate
        }
        board[r][c] = '#';          // mark visited
        int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
        for (auto &d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < board.size()
             && nc >= 0 && nc < board[0].size()
             && board[nr][nc] != '#') {
                dfs(board, nr, nc, next, res);
            }
        }
        board[r][c] = ch;           // restore
    }
    
public:
    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        vector<string> res;
        Node* root = buildTrie(words);
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                dfs(board, i, j, root, res);
            }
        }
        return res;
    }
};
```

---

## 🔧 Common Helper Functions  
```cpp
// Definition for a trie node (26 lowercase letters).
struct TrieNode {
    TrieNode* children[26];
    bool isEnd;
    TrieNode() : isEnd(false) {
        for (auto &p : children) p = nullptr;
    }
};

// Utility: delete all trie nodes to free memory
void deleteTrie(TrieNode* root) {
    if (!root) return;
    for (auto c : root->children) {
        deleteTrie(c);
    }
    delete root;
}
```

---

## 🔍 Algorithmic Paradigms  
- Trie construction and traversal (insert, search, wildcard)  
- DFS/backtracking with early pruning  
- Combining Trie + grid DFS for multi‑pattern search  

### Complexity Summary  
| Problem                              | Time                                              | Space                           |
|--------------------------------------|---------------------------------------------------|---------------------------------|
| Implement Trie                       | insert/search O(L), startsWith O(L)               | O(ALPHABET_SIZE · totalNodes)   |
| Add & Search Words Data Structure    | addWord O(L), search O(26^d · d) worst (d = len)  | O(ALPHABET_SIZE · totalNodes)   |
| Word Search II                       | O(m·n·4^L + ∑L) (pruned by trie)                  | O(ALPHABET_SIZE · totalTrieNodes + m·n) |

---

## 🚀 Advanced Tips & Practice  

- **Memory vs. Speed**  
  - Array-of-26 pointers is fast but memory-heavy; use `unordered_map<char,Node*>` if alphabet is sparse.  
- **Wildcard Optimization**  
  - Cache results of sub‑searches or use memoization for repeated patterns.  
- **Board DFS Pruning**  
  - Remove trie branches when no longer needed (`delete node` when child empty) to speed up later lookups.  
- **Follow‑Up Problems**  
  - Autocomplete systems, Replace Words, Longest Word in Dictionary through Deleting  
  - Implement Magic Dictionary (single-character mismatch)  
- **Common Pitfalls**  
  - Forgetting to restore visited marks in DFS.  
  - Memory leaks: always free trie nodes when done.  
  - Stack overflow on deep recursion—switch to iterative or increase stack if needed.  

Happy coding with Tries!  