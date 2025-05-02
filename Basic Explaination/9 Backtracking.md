<!--markdownlint-disable-->
# 📚 Backtracking

This guide covers nine classic LeetCode problems under the **Backtracking** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with detailed inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Subsets  
Link: https://leetcode.com/problems/subsets/

### Description  
Given a set of distinct integers `nums`, return all possible subsets (the power set).

### Core Idea: DFS Backtracking  
1. Sort (optional) for consistency.  
2. Define a recursive `dfs(start, path)`.  
3. At each call, add the current `path` to results.  
4. Loop `i` from `start` to `n-1`:  
   - Include `nums[i]` in `path`.  
   - Recurse with `dfs(i+1, path)`.  
   - Backtrack by removing last element.

### C++ Snippet: Generate All Subsets  
```cpp
// Solves "Subsets" by exploring include/exclude at each index.
void dfsSubsets(int start, 
                const vector<int>& nums, 
                vector<int>& path, 
                vector<vector<int>>& res) {
    res.push_back(path);                 // record current subset
    for (int i = start; i < nums.size(); ++i) {
        path.push_back(nums[i]);         // choose nums[i]
        dfsSubsets(i + 1, nums, path, res);
        path.pop_back();                 // undo choice
    }
}

vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> path;
    dfsSubsets(0, nums, path, res);
    return res;
}
```

---

## 2. Combination Sum  
Link: https://leetcode.com/problems/combination-sum/

### Description  
Given distinct `candidates` and a target, return all unique combinations where numbers sum to `target`. You may reuse elements.

### Core Idea: DFS with Reuse  
1. Sort `candidates` (optional).  
2. `dfs(start, remTarget, path)`:  
   - If `remTarget == 0`, record `path`.  
   - Loop `i` from `start` to end:  
     - If `candidates[i] > remTarget`, break (prune).  
     - Include `candidates[i]`, recurse with `(i, remTarget - candidates[i])`.  
     - Backtrack.

### C++ Snippet: Combination Sum with Pruning  
```cpp
// Finds all combinations summing to target, reusing elements.
void dfsCombSum(int start, 
                int rem, 
                const vector<int>& cand, 
                vector<int>& path, 
                vector<vector<int>>& res) {
    if (rem == 0) {
        res.push_back(path);
        return;
    }
    for (int i = start; i < cand.size(); ++i) {
        if (cand[i] > rem) break;        // no valid further
        path.push_back(cand[i]);         
        dfsCombSum(i, rem - cand[i], cand, path, res);
        path.pop_back();                 // backtrack
    }
}

vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    sort(candidates.begin(), candidates.end());
    vector<vector<int>> res;
    vector<int> path;
    dfsCombSum(0, target, candidates, path, res);
    return res;
}
```

---

## 3. Combination Sum II  
Link: https://leetcode.com/problems/combination-sum-ii/

### Description  
Like Combination Sum, but each candidate may be used at most once and `candidates` may contain duplicates.

### Core Idea: DFS with Skip Duplicates  
1. Sort `candidates`.  
2. `dfs(start, rem, path)`:  
   - If `rem == 0`, record.  
   - Loop `i` from `start` to end:  
     - If `i > start && cand[i] == cand[i-1]`, skip duplicate.  
     - If `cand[i] > rem`, break.  
     - Include `cand[i]`, recurse with `(i+1, rem - cand[i])`.  
     - Backtrack.

### C++ Snippet: Unique Combinations  
```cpp
// Finds combinations summing to target, no reuse and skip dups.
void dfsCombSum2(int start, 
                 int rem, 
                 const vector<int>& cand, 
                 vector<int>& path, 
                 vector<vector<int>>& res) {
    if (rem == 0) {
        res.push_back(path);
        return;
    }
    for (int i = start; i < cand.size(); ++i) {
        if (i > start && cand[i] == cand[i - 1]) continue; // skip dup
        if (cand[i] > rem) break;
        path.push_back(cand[i]);
        dfsCombSum2(i + 1, rem - cand[i], cand, path, res);
        path.pop_back();
    }
}

vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
    sort(candidates.begin(), candidates.end());
    vector<vector<int>> res;
    vector<int> path;
    dfsCombSum2(0, target, candidates, path, res);
    return res;
}
```

---

## 4. Permutations  
Link: https://leetcode.com/problems/permutations/

### Description  
Given distinct `nums`, return all possible orderings (permutations).

### Core Idea: DFS + Swap or Visited  
- **Swap method**: swap current index with each `i` ≥ index, recurse, then swap back.

### C++ Snippet: In‑Place Swap Permutations  
```cpp
// Generates all permutations by swapping in place.
void dfsPermute(int idx, 
                vector<int>& nums, 
                vector<vector<int>>& res) {
    if (idx == nums.size()) {
        res.push_back(nums);           // one permutation complete
        return;
    }
    for (int i = idx; i < nums.size(); ++i) {
        swap(nums[idx], nums[i]);      // choose i-th element
        dfsPermute(idx + 1, nums, res);
        swap(nums[idx], nums[i]);      // backtrack swap
    }
}

vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> res;
    dfsPermute(0, nums, res);
    return res;
}
```

---

## 5. Subsets II  
Link: https://leetcode.com/problems/subsets-ii/

### Description  
Return all subsets of `nums` (may contain duplicates) without duplicate subsets.

### Core Idea: DFS + Skip Duplicates  
1. Sort `nums`.  
2. `dfs(start)`: record current `path`.  
3. Loop `i` from `start` to `n-1`:  
   - If `i > start && nums[i] == nums[i-1]`, skip.  
   - Include `nums[i]`, recurse `(i+1)`, backtrack.

### C++ Snippet: Unique Subsets  
```cpp
// Generates subsets skipping duplicate branches.
void dfsSubsets2(int start, 
                 const vector<int>& nums, 
                 vector<int>& path, 
                 vector<vector<int>>& res) {
    res.push_back(path);
    for (int i = start; i < nums.size(); ++i) {
        if (i > start && nums[i] == nums[i - 1]) continue; // skip dup
        path.push_back(nums[i]);
        dfsSubsets2(i + 1, nums, path, res);
        path.pop_back();
    }
}

vector<vector<int>> subsetsWithDup(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    vector<vector<int>> res;
    vector<int> path;
    dfsSubsets2(0, nums, path, res);
    return res;
}
```

---

## 6. Word Search  
Link: https://leetcode.com/problems/word-search/

### Description  
Given a 2D board and a word, check if the word exists by traversing adjacent cells (up/down/left/right) without revisiting.

### Core Idea: DFS with Visited Marking  
1. Loop through each cell as potential start.  
2. `dfs(r, c, idx)`: if `board[r][c] != word[idx]`, return false.  
3. If `idx == word.length()-1`, found.  
4. Mark visited (e.g. `board[r][c] = '#'`), recurse in 4 directions.  
5. Restore cell after backtracking.

### C++ Snippet: Board DFS Search  
```cpp
// Checks if word exists in board via backtracking DFS.
bool dfsWord(vector<vector<char>>& b, 
             int r, int c, 
             const string& w, 
             int idx) {
    int m = b.size(), n = b[0].size();
    if (r < 0 || r >= m || c < 0 || c >= n || b[r][c] != w[idx])
        return false;
    if (idx == w.size() - 1) return true; // full match
    char tmp = b[r][c];
    b[r][c] = '#';                        // mark visited
    // explore neighbors
    bool found = dfsWord(b, r+1, c, w, idx+1)
              || dfsWord(b, r-1, c, w, idx+1)
              || dfsWord(b, r, c+1, w, idx+1)
              || dfsWord(b, r, c-1, w, idx+1);
    b[r][c] = tmp;                        // restore
    return found;
}

bool exist(vector<vector<char>>& board, const string& word) {
    int m = board.size(), n = board[0].size();
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (dfsWord(board, i, j, word, 0)) return true;
        }
    }
    return false;
}
```

---

## 7. Palindrome Partitioning  
Link: https://leetcode.com/problems/palindrome-partitioning/

### Description  
Partition a string `s` into substrings so that every substring is a palindrome; return all possible partitions.

### Core Idea: DFS + Palindrome Check  
1. `dfs(start, path)`:  
   - If `start == s.length()`, record `path`.  
   - Loop `end` from `start` to `n-1`:  
     - If `s[start..end]` is palindrome, include substring, recurse `(end+1)`, backtrack.

### C++ Snippet: Partition with Palindrome Pruning  
```cpp
// Helper to check palindrome in O(len)
bool isPal(const string& s, int l, int r) {
    while (l < r) {
        if (s[l++] != s[r--]) return false;
    }
    return true;
}

void dfsPartition(int start, 
                  const string& s, 
                  vector<string>& path, 
                  vector<vector<string>>& res) {
    if (start == s.size()) {
        res.push_back(path);
        return;
    }
    for (int end = start; end < s.size(); ++end) {
        if (!isPal(s, start, end)) continue;  // skip if not palindrome
        path.push_back(s.substr(start, end - start + 1));
        dfsPartition(end + 1, s, path, res);
        path.pop_back();
    }
}

vector<vector<string>> partition(string s) {
    vector<vector<string>> res;
    vector<string> path;
    dfsPartition(0, s, path, res);
    return res;
}
```

---

## 8. Letter Combinations of a Phone Number  
Link: https://leetcode.com/problems/letter-combinations-of-a-phone-number/

### Description  
Given digits `2–9`, return all possible letter combinations as on a telephone keypad.

### Core Idea: DFS by Mapping  
1. Map each digit to its letters.  
2. `dfs(idx, path)`:  
   - If `idx == digits.length()`, record `path`.  
   - Else, for each `ch` in `map[digits[idx]]`, append and recurse.

### C++ Snippet: Phone DFS Combinations  
```cpp
// Generates letter combos for digit string via backtracking.
vector<string> letterCombinations(string digits) {
    if (digits.empty()) return {};
    vector<string> mapping = {
        "", "", "abc", "def", "ghi", "jkl", 
        "mno", "pqrs", "tuv", "wxyz"
    };
    vector<string> res;
    string path;
    function<void(int)> dfs = [&](int idx) {
        if (idx == digits.size()) {
            res.push_back(path);
            return;
        }
        string &letters = mapping[digits[idx] - '0'];
        for (char c : letters) {
            path.push_back(c);
            dfs(idx + 1);
            path.pop_back();
        }
    };
    dfs(0);
    return res;
}
```

---

## 9. N‑Queens  
Link: https://leetcode.com/problems/n-queens/

### Description  
Place `n` queens on an `n×n` board so none attack each other; return all distinct solutions.

### Core Idea: DFS + Column/Diagonal Tracking  
1. Track used columns (`cols`), diagonal1 (`r+c`), diagonal2 (`r-c+n-1`) sets.  
2. `dfs(row, path)`:  
   - If `row == n`, convert `path` to board and record.  
   - Loop `col` from `0` to `n-1`:  
     - If column or diagonals occupied, skip.  
     - Mark occupied, recurse `row+1`, backtrack.

### C++ Snippet: Queens with Sets  
```cpp
// Solves N-Queens using backtracking with three occupancy sets.
void dfsNQ(int row, int n, 
           vector<int>& path, 
           vector<bool>& cols, 
           vector<bool>& d1, 
           vector<bool>& d2, 
           vector<vector<string>>& res) {
    if (row == n) {
        vector<string> board(n, string(n, '.'));
        for (int r = 0; r < n; ++r) 
            board[r][path[r]] = 'Q';
        res.push_back(board);
        return;
    }
    for (int c = 0; c < n; ++c) {
        int id1 = row + c;
        int id2 = row - c + n - 1;
        if (cols[c] || d1[id1] || d2[id2]) continue;
        cols[c] = d1[id1] = d2[id2] = true;
        path[row] = c;
        dfsNQ(row + 1, n, path, cols, d1, d2, res);
        cols[c] = d1[id1] = d2[id2] = false; // backtrack
    }
}

vector<vector<string>> solveNQueens(int n) {
    vector<vector<string>> res;
    vector<int> path(n);
    vector<bool> cols(n), d1(2*n-1), d2(2*n-1);
    dfsNQ(0, n, path, cols, d1, d2, res);
    return res;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Print a 2D vector of ints
void printVec2D(const vector<vector<int>>& v) {
    for (auto& row : v) {
        for (int x : row) cout << x << ' ';
        cout << '\n';
    }
}

// Print a vector of strings
void printStrVec(const vector<string>& v) {
    for (auto& s : v) cout << '"' << s << "\" ";
    cout << '\n';
}

// Print a chessboard
void printBoard(const vector<string>& board) {
    for (auto& row : board) cout << row << '\n';
}
```

---

## 🔍 Algorithmic Paradigms  
- **DFS Recursion** with branching at each choice point  
- **Backtracking** to undo choices and prune search space  
- **Pruning** by sorting + early break (Combination Sum) or skip duplicates  
- **State Tracking** via arrays/sets for constraints (N‑Queens, Word Search)  

### Why It Works  
Backtracking systematically explores all valid branches, pruning when constraints fail. Recursion maintains the current “path” state, and backtracking restores it for exploration of alternative branches.

### Complexity Summary  

| Problem                            | Time Complexity          | Space Complexity          |
|------------------------------------|--------------------------|---------------------------|
| Subsets                            | O(2ⁿ · n)                | O(n) recursion + O(2ⁿ·n)  |
| Combination Sum                    | O(⁠*) exponential         | O(n) recursion + output   |
| Combination Sum II                 | O(⁠*) exponential         | O(n) + output             |
| Permutations                       | O(n! · n)               | O(n) recursion + output   |
| Subsets II                         | O(2ⁿ · n)                | O(n) + output             |
| Word Search                        | O(m·n·4ˡ) l = word length| O(m·n) + recursion depth  |
| Palindrome Partitioning            | O(2ⁿ · n)                | O(n) recursion + output   |
| Letter Combinations                | O(4ˡ · l) l = digits len | O(l) recursion + output   |
| N‑Queens                           | O(n! / [(n/2)!]²)        | O(n) + O(n·n) output      |

(\* Exact depends on pruning efficiency.)

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Start with include/exclude (Subsets, Permutations).  
- **Medium**: Add pruning and skip-duplicate logic (Combination Sum II, Subsets II).  
- **Hard**: Maintain auxiliary state arrays/sets to enforce constraints (Word Search, N‑Queens).

### Follow‑Up Problems  
- Word Search II (multiple words)  
- Sudoku Solver  
- Knight’s Tour  
- Combination Sum III / IV  
- Restore IP Addresses  
- Expressions Add Operators  

### Common Pitfalls & Debugging  
- Forgetting to backtrack (pop or unmark visited).  
- Incorrect pruning order (must sort before break).  
- Off‑by‑one in indices or recursion bounds.  
- Not restoring state for grid/board problems.  
- Excessive copies of path vs. in‑place modification.

Happy exploring backtracking and mastering these patterns!  