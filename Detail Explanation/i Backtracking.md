<!--markdownlint-disable-->
# 📚 Backtracking (Advanced Beginner‑Friendly Master Guide)

This guide enhances nine classic LeetCode **Backtracking** problems with beginner‑friendly explanations, step‑by‑step walkthroughs, detailed code breakdowns, expanded helper functions, deep dives into paradigms, and next‑step guidance.

Each problem section includes:
1. **Theory**: The core backtracking idea in plain English.  
2. **Approach Walkthrough**: A small example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: What each line does, how it implements the theory, and common pitfalls.  
4. **Pitfalls & Tips**: Mistakes to watch out for and how to avoid them.

After the problems, you’ll find:
- **🔧 Expanded Helper Functions**  
- **🔍 Deep Dive into Backtracking Paradigms**  
- **🚀 Next Steps to Mastery**

---

## 1. Subsets  
Link: https://leetcode.com/problems/subsets/

### Theory: Include/Exclude Decision Tree  
At each index, we decide whether to include the current element in our subset (`path`) or skip it. This forms a binary tree of decisions and visits all 2ⁿ subsets.

### Approach Walkthrough  
`nums = [1,2]`  
- Start with `path = []`.  
- Include 1 → `path = [1]` → recurse start=1.  
  - Include 2 → `path = [1,2]` → record.  
  - Exclude 2 → backtrack to `[1]` → record.  
- Exclude 1 → `path = []` → recurse start=1.  
  - Include 2 → `path = [2]` → record.  
  - Exclude 2 → `[]` → record.

### Code Breakdown  
```cpp
void dfsSubsets(int start, 
                const vector<int>& nums, 
                vector<int>& path, 
                vector<vector<int>>& res) {
    res.push_back(path);                 // 1) Record current subset.
    for (int i = start; i < nums.size(); ++i) {
        path.push_back(nums[i]);         // 2) Include nums[i].
        dfsSubsets(i + 1, nums, path, res);  
                                         // 3) Recurse to build larger subsets.
        path.pop_back();                 // 4) Backtrack: remove last element.
    }
}

vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> path;
    dfsSubsets(0, nums, path, res);      // 5) Kick off recursion at index 0.
    return res;
}
```
1. We record `path` at every call to capture all subset sizes (including empty).  
2. Loop chooses each element in turn.  
3. Recursing with `i+1` moves to the next index.  
4. Backtracking restores `path` for the next iteration.  
5. Final results contain 2ⁿ subsets.

#### Pitfalls & Tips  
- Forgetting `pop_back()` leaves extra elements in `path`.  
- Not capturing the empty subset if you record only in the base case.

---

## 2. Combination Sum  
Link: https://leetcode.com/problems/combination-sum/

### Theory: Reuse Allowed, Prune with Sorted Candidates  
We choose each candidate multiple times as long as it doesn’t exceed the remaining target (`rem`). Sorting lets us stop early when a candidate > `rem`.

### Approach Walkthrough  
`candidates = [2,3,6,7], target = 7` (sorted)  
- Start `rem=7`, `path=[]`.  
- Try 2: `path=[2]`, `rem=5`.  
  - Try 2 again until `rem=1` → invalid → backtrack.  
  - Try 3: `path=[2,3]`, `rem=2` → try 2 → `path=[2,3,2]`, `rem=0` → record.  
- Try 3 first-level: `path=[3]`, `rem=4` → continue…  
- Try 6,7 similarly to find other combos `[7]`.

### Code Breakdown  
```cpp
void dfsCombSum(int start, 
                int rem, 
                const vector<int>& cand, 
                vector<int>& path, 
                vector<vector<int>>& res) {
    if (rem == 0) {                      // 1) Found a valid combination.
        res.push_back(path);
        return;
    }
    for (int i = start; i < cand.size(); ++i) {
        if (cand[i] > rem) break;        // 2) Prune: no need to continue.
        path.push_back(cand[i]);         // 3) Choose candidate i.
        dfsCombSum(i, rem - cand[i], cand, path, res);
                                          // reuse i by passing same index.
        path.pop_back();                 // 4) Backtrack.
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
1. Base case when `rem == 0`.  
2. Sorted array allows early break to prune whole branches.  
3. Recurse with `i` to allow reuse of the same candidate.  
4. Backtracking ensures other combinations are explored.

#### Pitfalls & Tips  
- Sorting required for correct pruning.  
- Failing to check `cand[i] > rem` leads to unnecessary recursion.

---

## 3. Combination Sum II  
Link: https://leetcode.com/problems/combination-sum-ii/

### Theory: No Reuse, Skip Duplicates  
Each candidate can be used once. Sorting + skipping equal neighbors avoids duplicate result sets.

### Approach Walkthrough  
`candidates = [10,1,2,7,6,1,5], target = 8`  
Sorted: `[1,1,2,5,6,7,10]`  
- At index 0: pick 1, recurse.  
- Skip index 1 if also 1 and index > start (avoid duplicate starting 1s).  
- Continue combination logic with no reuse.

### Code Breakdown  
```cpp
void dfsCombSum2(int start, 
                 int rem, 
                 const vector<int>& cand, 
                 vector<int>& path, 
                 vector<vector<int>>& res) {
    if (rem == 0) {
        res.push_back(path);             // record valid.
        return;
    }
    for (int i = start; i < cand.size(); ++i) {
        if (i > start && cand[i] == cand[i - 1]) continue; 
                                         // 1) Skip duplicate.
        if (cand[i] > rem) break;        // 2) Prune.
        path.push_back(cand[i]);         
        dfsCombSum2(i + 1, rem - cand[i], cand, path, res);
        path.pop_back();                 // 3) Backtrack.
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
1. Duplicate skip condition ensures unique combos.  
2. Pruning still applies.  
3. Passing `i+1` prevents reuse.

#### Pitfalls & Tips  
- The skip looks at `i > start`—don’t skip the first occurrence.  
- Sorting is mandatory for correct duplicate detection.

---

## 4. Permutations  
Link: https://leetcode.com/problems/permutations/

### Theory: In‑Place Swap Generates All Orderings  
Swapping each element into the “current” position at `idx` generates permutations without extra space.

### Approach Walkthrough  
`nums = [1,2,3]`  
- idx=0 swap 1↔1 → recurse on [2,3].  
- idx=1 swap 2↔2 → recurse idx=2 → record [1,2,3].  
- Backtrack swaps restore original order.

### Code Breakdown  
```cpp
void dfsPermute(int idx, 
                vector<int>& nums, 
                vector<vector<int>>& res) {
    if (idx == nums.size()) {
        res.push_back(nums);            // 1) Complete permutation.
        return;
    }
    for (int i = idx; i < nums.size(); ++i) {
        swap(nums[idx], nums[i]);       // 2) Place nums[i] at idx.
        dfsPermute(idx + 1, nums, res); // 3) Recurse on next position.
        swap(nums[idx], nums[i]);       // 4) Backtrack swap.
    }
}

vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> res;
    dfsPermute(0, nums, res);
    return res;
}
```
1. Base case when `idx` reaches end.  
2–4. Swap, recurse, swap back pattern.

#### Pitfalls & Tips  
- Swapping back in reverse order is essential to restore state.  
- Avoid extra data structures by swapping in place.

---

## 5. Subsets II  
Link: https://leetcode.com/problems/subsets-ii/

### Theory: Generate Subsets with Duplicate Skipping  
Similar to Subsets, but skip elements equal to the previous at the same recursive level to avoid duplicate subsets.

### Approach Walkthrough  
`nums = [1,2,2]`  
- At start, record `[]`.  
- i=0 pick 1 → recurse → record `[1]`, `[1,2]`, `[1,2,2]`.  
- Back at start, i=1 pick first 2 → record `[2]`, `[2,2]`.  
- Skip i=2 because same as i=1.

### Code Breakdown  
```cpp
void dfsSubsets2(int start, 
                 const vector<int>& nums, 
                 vector<int>& path, 
                 vector<vector<int>>& res) {
    res.push_back(path);                 // 1) Record subset.
    for (int i = start; i < nums.size(); ++i) {
        if (i > start && nums[i] == nums[i - 1]) continue; 
                                         // 2) Skip duplicate branch.
        path.push_back(nums[i]);         
        dfsSubsets2(i + 1, nums, path, res);
        path.pop_back();                 // 3) Backtrack.
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
2. Skipping ensures that at each level you only choose the first occurrence of a duplicate.

#### Pitfalls & Tips  
- Sorting is mandatory.  
- Skip condition must use `i > start`.

---

## 6. Word Search  
Link: https://leetcode.com/problems/word-search/

### Theory: Grid DFS with Visited Marking  
We perform DFS from each cell, marking visited to avoid reuse, and restore after exploring.

### Approach Walkthrough  
Board:
```
A B C E
S F C S
A D E E
```  
Word = “ABCCED”  
- Start at (0,0) ‘A’, then (0,1) ‘B’, … find path.

### Code Breakdown  
```cpp
bool dfsWord(vector<vector<char>>& b, 
             int r, int c, 
             const string& w, 
             int idx) {
    int m = b.size(), n = b[0].size();
    if (r<0||r>=m||c<0||c>=n||b[r][c]!=w[idx])
        return false;                   // 1) Boundary or char mismatch.
    if (idx == w.size() - 1) return true;
    char tmp = b[r][c];
    b[r][c] = '#';                      // 2) Mark visited.
    bool found = dfsWord(b, r+1,c,w,idx+1)
              || dfsWord(b, r-1,c,w,idx+1)
              || dfsWord(b, r,c+1,w,idx+1)
              || dfsWord(b, r,c-1,w,idx+1);
    b[r][c] = tmp;                      // 3) Restore original.
    return found;
}

bool exist(vector<vector<char>>& board, const string& word) {
    for (int i=0; i<board.size(); ++i)
      for (int j=0; j<board[0].size(); ++j)
        if (dfsWord(board,i,j,word,0)) return true;
    return false;
}
```
1. Immediate return on invalid or mismatch.  
2. In‑place mark avoids extra memory.  
3. Always restore to allow other paths.

#### Pitfalls & Tips  
- Failing to restore cell breaks further searches.  
- Using a separate `visited` array is slower and uses extra space.

---

## 7. Palindrome Partitioning  
Link: https://leetcode.com/problems/palindrome-partitioning/

### Theory: DFS + Palindrome Pruning  
We explore every cut, but only recurse when the substring is a palindrome. A helper checks palindrome in O(length).

### Approach Walkthrough  
`s = “aab”`:  
- idx=0 → “a” is palindrome → recurse on “ab”.  
- Next “a” → “a” → “b” → record [“a”,”a”,”b”].  
- Or “aa” → record [“aa”,”b”].

### Code Breakdown  
```cpp
bool isPal(const string& s, int l, int r) {
    while (l < r) {
        if (s[l++] != s[r--]) return false; // 1) Two‑pointer check.
    }
    return true;
}

void dfsPartition(int start, 
                  const string& s, 
                  vector<string>& path, 
                  vector<vector<string>>& res) {
    if (start == s.size()) {
        res.push_back(path);             // 2) Full partition found.
        return;
    }
    for (int end = start; end < s.size(); ++end) {
        if (!isPal(s, start, end)) continue;  // 3) Prune non‑pal
        path.push_back(s.substr(start, end - start + 1));
        dfsPartition(end + 1, s, path, res);
        path.pop_back();                 // 4) Backtrack.
    }
}

vector<vector<string>> partition(string s) {
    vector<vector<string>> res;
    vector<string> path;
    dfsPartition(0, s, path, res);
    return res;
}
```
1. Palindrome helper uses two‑pointer technique.  
2. Base case when we've covered the string.  
3. Early skip greatly reduces branches.  
4. Backtracking restores `path`.

#### Pitfalls & Tips  
- Substring creation in loop can be costly; consider caching palindrome checks.

---

## 8. Letter Combinations of a Phone Number  
Link: https://leetcode.com/problems/letter-combinations-of-a-phone-number/

### Theory: Fixed‑Length DFS by Digit Map  
Each digit maps to 3–4 letters. We build combinations character by character, depth = number of digits.

### Approach Walkthrough  
Digits = “23” → mapping: 2→“abc”, 3→“def”  
- idx=0: a→ recurse to idx=1: d,e,f → combinations: ad,ae,af  
- b→ bd,be,bf; c→ cd,ce,cf.

### Code Breakdown  
```cpp
vector<string> letterCombinations(string digits) {
    if (digits.empty()) return {};
    vector<string> mapping = { "", "", "abc","def","ghi","jkl","mno","pqrs","tuv","wxyz" };
    vector<string> res;
    string path;
    function<void(int)> dfs = [&](int idx) {
        if (idx == digits.size()) {      // 1) Completed one combination
            res.push_back(path);
            return;
        }
        string &letters = mapping[digits[idx] - '0'];
        for (char c : letters) {
            path.push_back(c);            
            dfs(idx + 1);                 
            path.pop_back();              // 2) Backtrack
        }
    };
    dfs(0);                              // 3) Start recursion
    return res;
}
```
1. Base case when `idx` reaches end.  
2. Backtracking resets `path`.  
3. Single recursive helper covers all digits.

#### Pitfalls & Tips  
- Handle empty input string before recursion.  
- Mapping must align to indices `'2'–'9'`.

---

## 9. N‑Queens  
Link: https://leetcode.com/problems/n-queens/

### Theory: Row‑by‑Row DFS with Column/Diagonal Sets  
We place one queen per row. Track occupied columns and diagonals (`row+col`, `row-col+n-1`) in boolean arrays. Only explore safe columns.

### Approach Walkthrough  
n=4 → place Q in row 0 at col 1,2,… then recurse to next row validating attacks.

### Code Breakdown  
```cpp
void dfsNQ(int row, int n, 
           vector<int>& path, 
           vector<bool>& cols, 
           vector<bool>& d1, 
           vector<bool>& d2, 
           vector<vector<string>>& res) {
    if (row == n) {                     // 1) All rows filled.
        vector<string> board(n, string(n, '.'));
        for (int r = 0; r < n; ++r)
            board[r][ path[r] ] = 'Q';
        res.push_back(board);
        return;
    }
    for (int c = 0; c < n; ++c) {
        int id1 = row + c, id2 = row - c + n - 1;
        if (cols[c] || d1[id1] || d2[id2]) continue; 
                                          // 2) Skip attacks.
        cols[c] = d1[id1] = d2[id2] = true;  
        path[row] = c;                   
        dfsNQ(row + 1, n, path, cols, d1, d2, res);
        cols[c] = d1[id1] = d2[id2] = false;
                                          // 3) Backtrack flags
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
1. Build board from `path` when complete.  
2. Check three attack constraints.  
3. Backtrack both `path` and occupancy.

#### Pitfalls & Tips  
- Off‑by‑one in diagonal indexing.  
- Ensure boolean arrays are sized correctly: `2*n - 1`.

---

## 🔧 Expanded Helper Functions  
```cpp
// Print a 2D vector of ints
void printVec2D(const vector<vector<int>>& v) {
    for (auto& row : v) {
        for (int x : row) cout << x << ' ';
        cout << '\n';
    }
}

// Print a 2D vector of strings
void printStrVec2D(const vector<vector<string>>& v) {
    for (auto& vec : v) {
        cout << "[ ";
        for (auto& s : vec) cout << '"' << s << "\" ";
        cout << "]\n";
    }
}

// Build vector from range [start, end)
vector<int> range(int start, int end) {
    vector<int> v;
    for (int i = start; i < end; ++i) v.push_back(i);
    return v;
}
```
- Use these to visualize results, especially in debugging.  
- Adapt `printStrVec2D` for boards, partitions, permutations.

---

## 🔍 Deep Dive into Paradigms

### DFS Backtracking  
- **Invariant**: At each recursion level, `path` holds a partial solution.  
- **Backtrack**: After exploring a branch, undo the last choice to restore state.  
- **Pruning**: Use sorting or checks (like palindrome test) to skip infeasible branches early.

### Key Patterns  
- **Include/Exclude**: Subsets, combination sums.  
- **In‑Place Swap**: Permutations to avoid extra memory.  
- **Visited Marking**: Word Search grid traversal.  
- **State Arrays**: N‑Queens uses boolean arrays for constraints.  
- **String Path**: Palindrome Partitioning and Letter Combinations build string incrementally.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Word Search II** (find multiple words)  
- **Sudoku Solver** (9×9 grid backtracking)  
- **Knight’s Tour** (graph DFS)  
- **Restore IP Addresses** (string partitioning)  
- **Combination Sum III / IV** (fixed count or count variants)

### Advanced Variants & Real‑World Applications  
- Constraint Satisfaction: sudoku, crosswords.  
- Game solvers: n‑puzzle, sliding tiles.  
- Permutation generators for test-case creation.  
- Expression evaluation with backtracking.

### Debugging Checklist  
1. **Backtrack Correctly**: Always undo your last change (pop, swap, unmark).  
2. **Prune Early**: Sort inputs or check constraints before recursing.  
3. **Check Base Case**: Ensure you record or stop at the correct recursion depth.  
4. **Trace Small Cases**: Step through `n=2,3` to confirm logic.  
5. **Avoid TLE**: Aggressive pruning and correct skip‑duplicate logic are essential.

Keep practicing these patterns—they form the backbone of many complex algorithms. Happy coding!  