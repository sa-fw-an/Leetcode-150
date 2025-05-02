<!--markdownlint-disable-->
# 📚 Tries (Advanced Beginner‑Friendly Master Guide)

This guide deepens your understanding of three classic **Trie** problems from LeetCode. For each, you’ll find:

1. **Theory**: Why Tries are the right data structure.  
2. **Approach Walkthrough**: A concrete example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary on each part of the C++ implementation.  
4. **Pitfalls & Tips**: Common mistakes and best practices.

After the problems, you’ll find:

- **🔧 Expanded Helper Functions**  
- **🔍 Deep Dive into Trie Paradigms**  
- **🚀 Next Steps to Mastery**

---

## 1. Implement Trie (Prefix Tree)  
Link: https://leetcode.com/problems/implement-trie-prefix-tree/

### Theory: Fast Prefix Queries via Character Paths  
A **Trie** (prefix tree) stores strings character by character in a tree. Each node represents a prefix; edges correspond to letters.  
- **Insert**: Create or follow nodes for each character.  
- **Search**: Traverse nodes for each character to verify existence.  
- **StartsWith**: Similar to search, but only require the prefix path to exist.

This yields O(L) time per operation (L = word length), independent of the number of stored words.

### Approach Walkthrough  
Insert “app” then search “apple” and prefix “app”:

1. **Insert “app”**  
   - Start at root.  
   - 'a': child missing → create node.  
   - 'p': create child.  
   - 'p': create child, mark `isEnd = true`.  

2. **Search “apple”**  
   - 'a' → exists;  
   - 'p' → exists;  
   - 'p' → exists and `isEnd=true` so far;  
   - 'l' → child missing → return false.  

3. **StartsWith “app”**  
   - Traverse 'a','p','p': all exist → return true.

### Line‑by‑Line Code Breakdown  
```cpp
class Trie {
    struct Node {
        Node* children[26];  // 26 lowercase pointers
        bool isEnd;          // marks a complete word
        Node(): isEnd(false) {
            for (auto &c : children) c = nullptr;
        }
    };
    Node* root;             // entry point of trie

public:
    Trie() {
        root = new Node();   // initialize empty root
    }

    void insert(const string& word) {
        Node* cur = root;    
        for (char c : word) {
            int idx = c - 'a';  
            if (!cur->children[idx]) {
                // 1) Create node if path missing
                cur->children[idx] = new Node();
            }
            cur = cur->children[idx]; // 2) Move to next node
        }
        cur->isEnd = true;    // 3) Mark end of word
    }

    bool search(const string& word) {
        Node* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!cur->children[idx]) {
                return false; // 4) Path breaks ⇒ word absent
            }
            cur = cur->children[idx];
        }
        return cur->isEnd;    // 5) Only true if marked end
    }

    bool startsWith(const string& prefix) {
        Node* cur = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!cur->children[idx]) {
                return false; // 6) Prefix path does not exist
            }
            cur = cur->children[idx];
        }
        return true;          // 7) Full prefix found
    }
};
```
- Steps 1–3 implement `insert`: create nodes and mark endings.  
- Steps 4–5 implement exact `search`.  
- Steps 6–7 implement `startsWith`, ignoring `isEnd`.

#### Pitfalls & Tips  
- Always initialize all 26 pointers to `nullptr`.  
- Don’t confuse `isEnd` with existence of a prefix.  
- Free allocated nodes when the Trie is no longer needed to avoid leaks.

---

## 2. Design Add and Search Words Data Structure  
Link: https://leetcode.com/problems/design-add-and-search-words-data-structure/

### Theory: Wildcard Matching via DFS  
We extend a standard Trie to support the `'.'` wildcard, matching any single letter.  
- **addWord**: Same as normal insert.  
- **search**: If character is letter, follow that branch; if `'.'`, try all non‑null children recursively.

### Approach Walkthrough  
Add “bad”, “dad”, “mad” then search “.ad”:

1. **addWord** builds three paths: `b→a→d`, `d→a→d`, `m→a→d`.  
2. **search “.ad”**  
   - At pos 0: `'.'` → try children `b,d,m`.  
   - For each, follow `a→d` and check `isEnd` at end.

### Line‑by‑Line Code Breakdown  
```cpp
class WordDictionary {
    struct Node {
        Node* children[26];
        bool isEnd;
        Node(): isEnd(false) {
            for (auto &c : children) c = nullptr;
        }
    };
    Node* root;

    bool dfsSearch(const string& word, int pos, Node* cur) {
        if (!cur) return false;                // 1) Null node ⇒ fail
        if (pos == word.size()) 
            return cur->isEnd;                 // 2) At end, check flag
        char c = word[pos];
        if (c == '.') {
            // 3) Wildcard: try all branches
            for (auto child : cur->children) {
                if (dfsSearch(word, pos+1, child))
                    return true;
            }
            return false;
        } else {
            int idx = c - 'a';
            return dfsSearch(word, pos+1, cur->children[idx]);
        }
    }

public:
    WordDictionary() {
        root = new Node();
    }

    void addWord(const string& word) {
        Node* cur = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!cur->children[idx])
                cur->children[idx] = new Node();
            cur = cur->children[idx];
        }
        cur->isEnd = true;                     // 4) Mark end of word
    }

    bool search(const string& word) {
        return dfsSearch(word, 0, root);       // 5) Kick off DFS
    }
};
```
1. Early return when node is null.  
2. If at end of word, return `isEnd`.  
3. For `'.'`, branch into all children.  
4–5. `addWord` and `search` glue DFS and insertion.

#### Pitfalls & Tips  
- DFS can explore many paths for multiple wildcards; avoid stack overflow on long patterns.  
- Clearing intermediate recursion states isn’t necessary—no backtracking state beyond call stack.

---

## 3. Word Search II  
Link: https://leetcode.com/problems/word-search-ii/

### Theory: Multi‑Pattern Search via Trie + Board DFS  
We build a Trie of all target words so we can prune DFS branches early. Each Trie node holds a `word` when a word ends there. During board DFS, reaching a node with non‑empty `word` yields a match.

### Approach Walkthrough  
Board:
```
o a a n
e t a e
i h k r
i f l v
```
Words: ["oath","pea","eat","rain"]  
- Build Trie for ["oath","pea","eat","rain"]  
- For each cell, DFS matching board char to trie child.  
- On reaching node with `word="oath"`, record and clear it to avoid duplicates.

### Line‑by‑Line Code Breakdown  
```cpp
class Solution {
    struct Node {
        Node* children[26];
        string word;           // stores full word at end node
        Node(): word("") {
            for (auto &c : children) c = nullptr;
        }
    };

    Node* buildTrie(const vector<string>& words) {
        Node* root = new Node();
        for (auto &w : words) {
            Node* cur = root;
            for (char c : w) {
                int idx = c - 'a';
                if (!cur->children[idx])
                    cur->children[idx] = new Node();
                cur = cur->children[idx];
            }
            cur->word = w;    // 1) Store word at terminal node
        }
        return root;
    }

    void dfs(vector<vector<char>>& board, int r, int c, Node* node,
             vector<string>& res) {
        char ch = board[r][c];
        int idx = ch - 'a';
        Node* nxt = node->children[idx];
        if (!nxt) return;                         // 2) No matching prefix
        if (!nxt->word.empty()) {
            res.push_back(nxt->word);             // 3) Found word
            nxt->word.clear();                    //    de‑duplicate
        }
        board[r][c] = '#';                        // 4) Mark visited
        int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
        for (auto &d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr>=0 && nr<board.size() &&
                nc>=0 && nc<board[0].size() &&
                board[nr][nc] != '#') {
                dfs(board, nr, nc, nxt, res);
            }
        }
        board[r][c] = ch;                         // 5) Restore
    }

public:
    vector<string> findWords(vector<vector<char>>& board,
                             vector<string>& words) {
        vector<string> res;
        Node* root = buildTrie(words);           // 6) Build trie
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                dfs(board, i, j, root, res);     // 7) Launch DFS from each cell
            }
        }
        return res;
    }
};
```
1. Store the word at the terminal node for direct access.  
2. Prune DFS if no child matches the character.  
3. Detect and record complete words.  
4–5. Mark cell as visited and restore after recursion.  
6–7. Build Trie once, then DFS from all positions.

#### Pitfalls & Tips  
- Clearing `node->word` prevents duplicates.  
- Restoring board cell is critical to allow other paths.

---

## 🔧 Expanded Helper Functions  
```cpp
// Recursively delete all Trie nodes to avoid memory leaks.
void deleteTrie(Trie::Node* root) {
    if (!root) return;
    for (auto child : root->children)
        deleteTrie(child);
    delete root;
}

// Print all words in result vector
void printWords(const vector<string>& words) {
    cout << "[";
    for (int i = 0; i < words.size(); ++i) {
        cout << '"' << words[i] << '"';
        if (i + 1 < words.size()) cout << ", ";
    }
    cout << "]\n";
}
```
- Use `deleteTrie` after using any Trie-based structure.  
- `printWords` helps visualize search results.

---

## 🔍 Deep Dive into Trie Paradigms

### Fixed‑Array vs Map Children  
- **Array[26]**: O(1) child access, high memory.  
- **Map/Unordered_map**: saves memory for sparse alphabets, slower access.

### Storing Data at Nodes  
- **isEnd** flag for boolean queries.  
- **word** string for multi‑pattern search to avoid passing path buffer.

### Wildcard & Regex‑like Matching  
- Use DFS to branch wherever wildcard appears; complexity multiplies by alphabet size.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Implement Magic Dictionary** (single-letter substitutions)  
- **Replace Words** (prefix replacement)  
- **Long Pressed Name** (two‑pointer + Trie)

### Advanced Variants  
- Case‑insensitive or Unicode Tries  
- Compressed Tries (Radix trees) for space optimization  
- Suffix Tries/Automata for substring search

### Debugging Checklist  
1. Verify all pointers initialized to `nullptr`.  
2. Confirm `isEnd` or `word` is set only at terminal nodes.  
3. Ensure visited marks (`'#'`) are restored.  
4. Free memory to avoid leaks in long-running services.  
5. Test edge cases: empty strings, all wildcards, maximum board size.

Happy coding with Tries!  