<!--markdownlint-disable-->
# 📚 2‑D Dynamic Programming (Advanced Beginner‑Friendly Master Guide)

This guide elucidates eleven foundational LeetCode **2‑D Dynamic Programming** problems. Each section includes:

1. **Theory**: The core DP insight in plain English.  
2. **Approach Walkthrough**: A small example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary on the C++ solution.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  
5. **Complexity Analysis**: Time and space costs.  
6. **Advanced Tips & Variants**: Further optimizations or related problems.

---

## 1. Unique Paths  
Link: https://leetcode.com/problems/unique-paths/

### Theory  
Moving only right or down on an `m×n` grid forms a lattice path problem. The number of ways to reach cell `(i,j)` equals the sum of ways to reach `(i-1,j)` and `(i,j-1)`.

### Approach Walkthrough  
Grid 3×3:
```
Start → (0,1) → (0,2)
 ↓         ↙
(1,0) → (1,1) → (1,2)
 ↓         ↙
(2,0) → (2,1) → Finish
```
- All top row and left column = 1 way.  
- Cell (1,1) = ways(0,1)+ways(1,0)=1+1=2.  
- Cell (2,2) = ways(1,2)+ways(2,1)=3+3=6.

### Code Breakdown  
```cpp
int uniquePaths(int m, int n) {
    vector<vector<int>> dp(m, vector<int>(n, 1));
    // 1) Initialize first row+col to 1 (only one way along edges).
    for (int i = 1; i < m; ++i) {
        for (int j = 1; j < n; ++j) {
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
            // 2) Recurrence: sum of top and left neighbors.
        }
    }
    return dp[m-1][n-1];
    // 3) Answer at bottom-right.
}
```

Pitfalls & Tips  
- Rolling array over columns reduces space to O(n).  
- Beware integer overflow for very large m, n—use 64-bit if needed.

Complexity  
- Time: O(m·n)  
- Space: O(m·n) → O(n) with rolling array

---

## 2. Longest Common Subsequence  
Link: https://leetcode.com/problems/longest-common-subsequence/

### Theory  
Find the longest sequence present in both strings (not necessarily contiguous). If characters match, extend the subsequence; otherwise carry forward the best of dropping one character.

### Approach Walkthrough  
A = “ABCBDAB”, B = “BDCAB”  
- Build dp table row by row.  
- At A[3]=‘B’ and B[2]=‘C’, dp[4][3]=max(dp[3][3], dp[4][2]) = 2.  
- Final dp[m][n] = 4 (“BCAB” or “BDAB”).

### Code Breakdown  
```cpp
int longestCommonSubsequence(string A, string B) {
    int m = A.size(), n = B.size();
    // 1) dp[i][j] = length for A[0..i-1], B[0..j-1]
    vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (A[i-1] == B[j-1]) {
                dp[i][j] = dp[i-1][j-1] + 1;
                // 2) Match: extend
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
                // 3) No match: best of dropping a char
            }
        }
    }
    return dp[m][n];
    // 4) LCS length
}
```

Pitfalls & Tips  
- Can reduce space to O(n) using two rows.  
- To reconstruct the subsequence, trace back through the dp table.

Complexity  
- Time: O(m·n)  
- Space: O(m·n) → O(n) with two-row optimization

---

## 3. Best Time to Buy and Sell Stock with Cooldown  
Link: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

### Theory  
Model three daily states—holding, sold, or resting. Transitions enforce the 1-day cooldown after a sell.

### Approach Walkthrough  
Prices [1,2,3,0,2]:  
- Day 0: hold=-1, sell=0, rest=0  
- Day 1: hold=-1, sell=1, rest=0  
- Day 2: hold=-1, sell=2, rest=1  
- Day 3: hold=1, sell=0, rest=2  
- Day 4: hold=1, sell=3, rest=2 → max(sell,rest)=3

### Code Breakdown  
```cpp
int maxProfit(vector<int>& P) {
    int n = P.size();
    int hold = -P[0], sell = 0, rest = 0;
    // 1) Initialize day 0 states.
    for (int i = 1; i < n; ++i) {
        int prevHold = hold, prevSell = sell;
        hold = max(prevHold, rest - P[i]);
        // 2) Either keep holding or buy after rest.
        sell = prevHold + P[i];
        // 3) Sell today from holding.
        rest = max(rest, prevSell);
        // 4) Stay resting or enter from sell.
    }
    return max(sell, rest);
    // 5) Max profit without holding at end.
}
```

Pitfalls & Tips  
- Ensure order: use previous day's hold/rest for transitions.  
- For multiple cooldowns, extend state machine accordingly.

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 4. Coin Change II  
Link: https://leetcode.com/problems/coin-change-ii/

### Theory  
Unbounded knapsack: for each coin, accumulate ways to form every intermediate amount.

### Approach Walkthrough  
coins=[1,2], amount=4  
- After coin 1: dp=[1,1,1,1,1]  
- After coin 2: dp[2]+=dp[0]=2, dp[3]+=dp[1]=2, dp[4]+=dp[2]=3 → dp[4]=3

### Code Breakdown  
```cpp
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount+1, 0);
    dp[0] = 1;  // 1) One way to make 0.
    for (int c : coins) {
        for (int a = c; a <= amount; ++a) {
            dp[a] += dp[a - c];
            // 2) Add ways by using coin c.
        }
    }
    return dp[amount];
}
```

Pitfalls & Tips  
- Iterate coins outermost to avoid counting permutations.  
- For combinations vs. permutations, swap loops.

Complexity  
- Time: O(amount × n_coins)  
- Space: O(amount)

---

## 5. Target Sum  
Link: https://leetcode.com/problems/target-sum/

### Theory  
Convert sign assignments to subset sum: choosing positives `P` and negatives `N` so `P - N = T` and `P + N = S` ⇒ `P = (S + T)/2`.

### Approach Walkthrough  
nums=[1,1,1,1,1], T=3 → S=5, P=4  
Count subsets summing to 4: C(5,4)=5

### Code Breakdown  
```cpp
int findTargetSumWays(vector<int>& nums, int T) {
    int S = accumulate(nums.begin(), nums.end(), 0);
    if ((S + T) % 2) return 0;         // 1) If odd, no solution.
    int P = (S + T) / 2;
    vector<int> dp(P+1, 0);
    dp[0] = 1;                         // 2) One way to sum 0.
    for (int x : nums) {
        for (int a = P; a >= x; --a) {
            dp[a] += dp[a - x];
            // 3) Include x in subset.
        }
    }
    return dp[P];
}
```

Pitfalls & Tips  
- Check `(S+T)%2==0` to ensure integer P.  
- Reverse loop to avoid reuse within iteration.

Complexity  
- Time: O(n × P)  
- Space: O(P)

---

## 6. Interleaving String  
Link: https://leetcode.com/problems/interleaving-string/

### Theory  
Cell `(i,j)` is reachable if it comes from `(i-1,j)` matching `s1[i-1]` or `(i,j-1)` matching `s2[j-1]`.

### Approach Walkthrough  
A=“aab”, B=“axy”, C=“aaxaby”:  
- dp[2][2] = true since dp[1][2] (from B) and C[3]=‘a’ matches A[1].

### Code Breakdown  
```cpp
bool isInterleave(string A, string B, string C) {
    int n = A.size(), m = B.size();
    if (n + m != C.size()) return false;  // 1) Length check.
    vector<vector<bool>> dp(n+1, vector<bool>(m+1, false));
    dp[0][0] = true;                       // 2) Empty interleaving.
    for (int i = 0; i <= n; ++i) {
        for (int j = 0; j <= m; ++j) {
            if (i > 0)
                dp[i][j] |= dp[i-1][j] && A[i-1] == C[i+j-1];
            if (j > 0)
                dp[i][j] |= dp[i][j-1] && B[j-1] == C[i+j-1];
            // 3) Combine two sources.
        }
    }
    return dp[n][m];
}
```

Pitfalls & Tips  
- Use 1D rolling array for space O(m).  
- Early exit when dp row becomes all false.

Complexity  
- Time: O(n·m)  
- Space: O(n·m) → O(m) with rolling

---

## 7. Longest Increasing Path in a Matrix  
Link: https://leetcode.com/problems/longest-increasing-path-in-a-matrix/

### Theory  
Each cell’s best path length = 1 + max of neighbor paths where neighbor value > current. Memoize DFS to avoid recomputation.

### Approach Walkthrough  
```
A = [
 [9,9,4],
 [6,6,8],
 [2,1,1]
]
```
- Cell (2,1)=1: neighbors bigger → best=1+max(paths from 2,1’s neighbors)=4.

### Code Breakdown  
```cpp
int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
int dfs(int r, int c, vector<vector<int>>& A, vector<vector<int>>& memo) {
    if (memo[r][c]) return memo[r][c]; // 1) Cached result.
    int m = A.size(), n = A[0].size(), best = 1;
    for (auto &d : dirs) {
        int nr = r + d[0], nc = c + d[1];
        if (nr>=0&&nr<m&&nc>=0&&nc<n && A[nr][nc]>A[r][c]) {
            best = max(best, 1 + dfs(nr,nc,A,memo));
        }
    }
    return memo[r][c] = best;           // 2) Store and return.
}

int longestIncreasingPath(vector<vector<int>>& A) {
    int m = A.size(), n = A[0].size(), ans = 0;
    vector<vector<int>> memo(m, vector<int>(n,0));
    for (int i = 0; i < m; ++i)
        for (int j = 0; j < n; ++j)
            ans = max(ans, dfs(i,j,A,memo));
    return ans;
}
```

Pitfalls & Tips  
- Without memoization, DFS is exponential.  
- Guard against stack overflow for large grids—use iterative or increase stack.

Complexity  
- Time: O(m·n)  
- Space: O(m·n)

---

## 8. Distinct Subsequences  
Link: https://leetcode.com/problems/distinct-subsequences/

### Theory  
To count embeddings of `T` in `S`, build dp so `dp[i][j]` = ways T[0..j-1] in S[0..i-1].

### Approach Walkthrough  
S=“rabbbit”, T=“rabbit”:  
- Many ways skip extra ‘b’.

### Code Breakdown  
```cpp
int numDistinct(string S, string T) {
    int m = S.size(), n = T.size();
    vector<vector<unsigned long>> dp(m+1, vector<unsigned long>(n+1, 0));
    for (int i = 0; i <= m; ++i) dp[i][0] = 1;  // 1) Empty T.
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            dp[i][j] = dp[i-1][j];
            if (S[i-1] == T[j-1])
                dp[i][j] += dp[i-1][j-1];
            // 2) Combine skip S[i-1] or match it.
        }
    }
    return dp[m][n];
}
```

Pitfalls & Tips  
- Use 64‑bit counters to avoid overflow.  
- Can optimize to O(n) space via single row.

Complexity  
- Time: O(m·n)  
- Space: O(m·n) → O(n) with rolling

---

## 9. Edit Distance  
Link: https://leetcode.com/problems/edit-distance/

### Theory  
Classic Levenshtein distance: insert, delete, replace operations cost 1.

### Approach Walkthrough  
“horse” → “ros”:  
- Transform “horse” → “rorse”(replace h→r), → “rose”(delete r), → “ros” (delete e) = 3 ops.

### Code Breakdown  
```cpp
int minDistance(string A, string B) {
    int m = A.size(), n = B.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1));
    for (int i = 0; i <= m; ++i) dp[i][0] = i;  // 1) Deletions
    for (int j = 0; j <= n; ++j) dp[0][j] = j;  // 2) Insertions
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (A[i-1] == B[j-1])
                dp[i][j] = dp[i-1][j-1];
            else
                dp[i][j] = 1 + min({
                    dp[i-1][j-1], // replace
                    dp[i-1][j],   // delete
                    dp[i][j-1]    // insert
                });
        }
    }
    return dp[m][n];
}
```

Pitfalls & Tips  
- Two-row optimization reduces space to O(n).  
- Be careful initializing the first row/column.

Complexity  
- Time: O(m·n)  
- Space: O(m·n) → O(n) with two rows

---

## 10. Burst Balloons  
Link: https://leetcode.com/problems/burst-balloons/

### Theory  
Interval DP: choose the last balloon to burst in each interval, combining subinterval results.

### Approach Walkthrough  
A=[3,1,5,8]:  
- Pad to [1,3,1,5,8,1].  
- Solve intervals of increasing length, e.g. interval (0,5) considers k=1..4 as last.

### Code Breakdown  
```cpp
int maxCoins(vector<int>& A) {
    int n = A.size();
    vector<int> nums(n+2, 1);
    for (int i = 0; i < n; ++i) nums[i+1] = A[i];
    int m = n + 2;
    vector<vector<int>> dp(m, vector<int>(m, 0));
    for (int len = 2; len < m; ++len) {
        for (int i = 0; i + len < m; ++i) {
            int j = i + len;
            for (int k = i + 1; k < j; ++k) {
                dp[i][j] = max(dp[i][j],
                  dp[i][k] + nums[i]*nums[k]*nums[j] + dp[k][j]);
                // 1) Best split at k
            }
        }
    }
    return dp[0][m-1];
}
```

Pitfalls & Tips  
- O(n³) time; small n only.  
- Can memoize recursion with pruning for sparse intervals.

Complexity  
- Time: O(n³)  
- Space: O(n²)

---

## 11. Regular Expression Matching  
Link: https://leetcode.com/problems/regular-expression-matching/

### Theory  
DP over string and pattern indices, treating `*` as zero or more of the preceding element.

### Approach Walkthrough  
s="aab", p="c*a*b":  
- `c*` can match zero c’s, reduce to a*b, then match “aa” and “b”.

### Code Breakdown  
```cpp
bool isMatch(string s, string p) {
    int m = s.size(), n = p.size();
    vector<vector<bool>> dp(m+1, vector<bool>(n+1, false));
    dp[0][0] = true;                       // 1) Empty pattern matches empty string.
    // 2) Initialize dp[0][j] for patterns like a* or a*b*.
    for (int j = 2; j <= n; ++j)
        if (p[j-1] == '*') dp[0][j] = dp[0][j-2];

    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (p[j-1] != '*') {
                dp[i][j] = dp[i-1][j-1]
                  && (s[i-1] == p[j-1] || p[j-1] == '.');
                // 3) Direct match or wildcard.
            } else {
                // 4) Zero occurrences or at least one.
                dp[i][j] = dp[i][j-2]
                  || (dp[i-1][j] && 
                      (s[i-1] == p[j-2] || p[j-2] == '.'));
            }
        }
    }
    return dp[m][n];
}
```

Pitfalls & Tips  
- Carefully handle `dp[0][j]` initialization for patterns starting with `*`.  
- Use `j-2` when skipping zero occurrences.

Complexity  
- Time: O(m·n)  
- Space: O(m·n)

---

## 🔍 Common 2‑D DP Patterns  

- **Grid DP**: Unique Paths, Increasing Path  
- **Sequence Alignment**: LCS, Edit Distance  
- **State Machines**: Stock with cooldown, Regex matching  
- **Knapsack Variants**: Coin Change II, Target Sum  
- **Interleaving & Subsequences**: Interleaving String, Distinct Subsequences  
- **Interval DP**: Burst Balloons  
- **DFS + Memo**: Longest Increasing Path

### Complexity Summary  

| Problem                            | Time             | Space             |
|------------------------------------|------------------|-------------------|
| Unique Paths                       | O(m·n)           | O(n)              |
| Longest Common Subsequence         | O(m·n)           | O(n)              |
| Stock with Cooldown                | O(n)             | O(1)              |
| Coin Change II                     | O(amount·k)      | O(amount)         |
| Target Sum                         | O(n·P)           | O(P)              |
| Interleaving String                | O(n·m)           | O(m)              |
| Longest Increasing Path in Matrix  | O(m·n)           | O(m·n)            |
| Distinct Subsequences              | O(m·n)           | O(n)              |
| Edit Distance                      | O(m·n)           | O(n)              |
| Burst Balloons                     | O(n³)            | O(n²)             |
| Regular Expression Matching        | O(m·n)           | O(m·n)            |

---

## 🚀 Advanced Tips & Variants  

- **Space Reduction**: roll DP tables into 1D or two rows when dependencies allow.  
- **Monotonic Queue**: optimize sliding-window DP in 2D.  
- **Divide & Conquer DP**: convex hull trick or SMAWK for certain cost structures.  
- **Bitmask & State Compression**: pack additional indices for small dimensions.  
- **Pattern‑based DP**: treat regex or interleaving as finite automata.  
- **Memoization with DFS**: alternative to bottom‑up, especially for sparse state space.  

Happy mastering 2‑D DP patterns!  