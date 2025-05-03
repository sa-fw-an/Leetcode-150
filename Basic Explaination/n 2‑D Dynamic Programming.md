<!--markdownlint-disable-->
# 📚 2‑D Dynamic Programming

This guide covers eleven classic LeetCode problems under the **2‑D Dynamic Programming** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Recurrence / core DP idea  
3. Step‑by‑step approach  
4. A concise C++ snippet with inline comments  
5. Complexity analysis  
6. Advanced tips & related variants  

---

## 1. Unique Paths  
Link: https://leetcode.com/problems/unique-paths/

### Description  
Count how many ways a robot can move from the top-left to bottom-right of an `m×n` grid, moving only down or right.

### Recurrence  
- `dp[i][j] = dp[i-1][j] + dp[i][j-1]`

### Approach  
1. Initialize `dp[0][*] = dp[*][0] = 1`.  
2. For each `i` from `1` to `m-1` and `j` from `1` to `n-1`, set `dp[i][j] = dp[i-1][j] + dp[i][j-1]`.  
3. Return `dp[m-1][n-1]`.

### C++ Snippet  
```cpp
int uniquePaths(int m, int n) {
    vector<vector<int>> dp(m, vector<int>(n, 1));
    for (int i = 1; i < m; ++i) {
        for (int j = 1; j < n; ++j) {
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    return dp[m-1][n-1];
}
```

Time: O(m·n)  
Space: O(m·n) → O(n) by rolling array

---

## 2. Longest Common Subsequence  
Link: https://leetcode.com/problems/longest-common-subsequence/

### Description  
Given two strings, find the length of their longest subsequence common to both.

### Recurrence  
- `dp[i][j] = dp[i-1][j-1] + 1` if `A[i-1]==B[j-1]`, else `max(dp[i-1][j], dp[i][j-1])`

### Approach  
1. Let `m=A.size()`, `n=B.size()`.  
2. Build `(m+1)×(n+1)` table, initialized to 0.  
3. Fill row by row with the above recurrence.  
4. Return `dp[m][n]`.

### C++ Snippet  
```cpp
int longestCommonSubsequence(string A, string B) {
    int m = A.size(), n = B.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1));
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (A[i-1] == B[j-1])
                dp[i][j] = dp[i-1][j-1] + 1;
            else
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
        }
    }
    return dp[m][n];
}
```

Time: O(m·n)  
Space: O(m·n) → O(n) by two rows

---

## 3. Best Time to Buy and Sell Stock with Cooldown  
Link: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

### Description  
Maximize profit over days with prices, where after a sell you cannot buy on the next day (1-day cooldown).

### Recurrence  
Use three states per day:  
- `hold[i]` = max profit holding a stock  
- `sell[i]` = max profit just sold  
- `rest[i]` = max profit resting  

Transitions:  
```text
hold[i] = max(hold[i-1], rest[i-1] - price)
sell[i] = hold[i-1] + price
rest[i] = max(rest[i-1], sell[i-1])
```

### Approach  
1. Initialize `hold[0] = -price[0]`, `sell[0] = 0`, `rest[0] = 0`.  
2. Iterate `i=1…n-1`, update states.  
3. Answer = `max(sell[n-1], rest[n-1])`.

### C++ Snippet  
```cpp
int maxProfit(vector<int>& P) {
    int n = P.size();
    int hold = -P[0], sell = 0, rest = 0;
    for (int i = 1; i < n; ++i) {
        int prevHold = hold, prevSell = sell;
        hold = max(prevHold, rest - P[i]);
        sell = prevHold + P[i];
        rest = max(rest, prevSell);
    }
    return max(sell, rest);
}
```

Time: O(n)  
Space: O(1)

---

## 4. Coin Change II  
Link: https://leetcode.com/problems/coin-change-ii/

### Description  
Count combinations to make `amount` using unlimited coins of given denominations.

### Recurrence  
- `dp[i][a] += dp[i-1][a - coin_i]`

### Approach  
1. Let `dp[a]` be ways to make `a`. Initialize `dp[0]=1`.  
2. For each coin, for `a=coin…amount`, `dp[a] += dp[a-coin]`.  
3. Return `dp[amount]`.

### C++ Snippet  
```cpp
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount+1);
    dp[0] = 1;
    for (int c : coins) {
        for (int a = c; a <= amount; ++a)
            dp[a] += dp[a - c];
    }
    return dp[amount];
}
```

Time: O(amount × n_coins)  
Space: O(amount)

---

## 5. Target Sum  
Link: https://leetcode.com/problems/target-sum/

### Description  
Given `nums[]`, assign `+` or `-` to each to reach target. Count number of ways.

### Recurrence  
Convert to subset sum: Let sum(nums)=S, target=T. Then `P - N = T` and `P + N = S` ⇒ `P = (S+T)/2`. Count subsets summing to `P`.

### Approach  
1. Compute `sum`. If `(sum+T)%2!=0` return 0.  
2. Let `P = (sum+T)/2`.  
3. Count subsets with sum `P` using 1-D knapsack: `dp[a] += dp[a - num]` backwards.  

### C++ Snippet  
```cpp
int findTargetSumWays(vector<int>& nums, int T) {
    int S = accumulate(nums.begin(), nums.end(), 0);
    if ((S + T) % 2) return 0;
    int P = (S + T) / 2;
    vector<int> dp(P+1);
    dp[0] = 1;
    for (int x : nums) {
        for (int a = P; a >= x; --a)
            dp[a] += dp[a - x];
    }
    return dp[P];
}
```

Time: O(n × P)  
Space: O(P)

---

## 6. Interleaving String  
Link: https://leetcode.com/problems/interleaving-string/

### Description  
Check if `s3` is formed by interleaving `s1` and `s2`.

### Recurrence  
- `dp[i][j] = (dp[i-1][j] && s1[i-1]==s3[i+j-1])`  
  `|| (dp[i][j-1] && s2[j-1]==s3[i+j-1])`

### Approach  
1. If `len1+len2 != len3`, return false.  
2. Build `(len1+1)×(len2+1)` table, initialize `dp[0][0]=true`.  
3. Fill first row/col, then rest with recurrence.  
4. Return `dp[len1][len2]`.

### C++ Snippet  
```cpp
bool isInterleave(string A, string B, string C) {
    int n = A.size(), m = B.size();
    if (n + m != C.size()) return false;
    vector<vector<bool>> dp(n+1, vector<bool>(m+1));
    dp[0][0] = true;
    for (int i = 0; i <= n; ++i) {
        for (int j = 0; j <= m; ++j) {
            if (i > 0)
                dp[i][j] |= dp[i-1][j] && A[i-1] == C[i+j-1];
            if (j > 0)
                dp[i][j] |= dp[i][j-1] && B[j-1] == C[i+j-1];
        }
    }
    return dp[n][m];
}
```

Time: O(n·m)  
Space: O(n·m) → O(m) rolling row

---

## 7. Longest Increasing Path in a Matrix  
Link: https://leetcode.com/problems/longest-increasing-path-in-a-matrix/

### Description  
Find the longest strictly increasing path in a 2D `m×n` matrix.

### Core Idea: DFS + Memoization  
- Let `dfs(i,j)` return length of longest path starting at `(i,j)`.  
- Memoize results to avoid recomputation.

### Approach  
1. Initialize `memo[m][n] = 0`.  
2. For each cell, `ans = max(ans, dfs(i,j))`.  
3. In `dfs(r,c)`, if `memo[r][c] > 0` return it.  
4. Else, explore 4 directions where `next > curr`, take max + 1, store in `memo`.

### C++ Snippet  
```cpp
int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
int dfs(int r, int c, vector<vector<int>>& A, vector<vector<int>>& memo) {
    if (memo[r][c]) return memo[r][c];
    int m = A.size(), n = A[0].size();
    int best = 1;
    for (auto &d : dirs) {
        int nr = r + d[0], nc = c + d[1];
        if (nr>=0&&nr<m&&nc>=0&&nc<n && A[nr][nc]>A[r][c]) {
            best = max(best, 1 + dfs(nr,nc,A,memo));
        }
    }
    return memo[r][c] = best;
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

Time: O(m·n)  
Space: O(m·n)

---

## 8. Distinct Subsequences  
Link: https://leetcode.com/problems/distinct-subsequences/

### Description  
Count how many ways `T` appears as a subsequence in `S`.

### Recurrence  
- If `S[i-1]==T[j-1]`:  
  `dp[i][j] = dp[i-1][j-1] + dp[i-1][j]`  
  else  
  `dp[i][j] = dp[i-1][j]`

### Approach  
1. Let `m=S.size(), n=T.size()`.  
2. Build `(m+1)×(n+1)` table, `dp[*][0]=1`.  
3. Fill row by row with above recurrence.  
4. Return `dp[m][n]`.

### C++ Snippet  
```cpp
int numDistinct(string S, string T) {
    int m = S.size(), n = T.size();
    vector<vector<unsigned long>> dp(m+1, vector<unsigned long>(n+1,0));
    for (int i = 0; i <= m; ++i) dp[i][0] = 1;
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            dp[i][j] = dp[i-1][j];
            if (S[i-1] == T[j-1])
                dp[i][j] += dp[i-1][j-1];
        }
    }
    return dp[m][n];
}
```

Time: O(m·n)  
Space: O(m·n) → O(n) row rolling

---

## 9. Edit Distance  
Link: https://leetcode.com/problems/edit-distance/

### Description  
Compute minimum operations (insert, delete, replace) to convert `word1` to `word2`.

### Recurrence  
- If `w1[i-1]==w2[j-1]`, `dp[i][j] = dp[i-1][j-1]`  
- Else `dp[i][j] = 1 + min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])`

### Approach  
1. Let `m,n` lengths; build `(m+1)×(n+1)` table.  
2. Initialize first row/col to `i` and `j`.  
3. Fill with above recurrence.  
4. Return `dp[m][n]`.

### C++ Snippet  
```cpp
int minDistance(string A, string B) {
    int m = A.size(), n = B.size();
    vector<vector<int>> dp(m+1, vector<int>(n+1));
    for (int i = 0; i <= m; ++i) dp[i][0] = i;
    for (int j = 0; j <= n; ++j) dp[0][j] = j;
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (A[i-1] == B[j-1])
                dp[i][j] = dp[i-1][j-1];
            else
                dp[i][j] = 1 + min({dp[i-1][j-1], dp[i-1][j], dp[i][j-1]});
        }
    }
    return dp[m][n];
}
```

Time: O(m·n)  
Space: O(m·n) → O(n) two rows

---

## 10. Burst Balloons  
Link: https://leetcode.com/problems/burst-balloons/

### Description  
Given balloons with values, maximize coins by deciding burst order. Bursting `i` yields `nums[left]*nums[i]*nums[right]`.

### Recurrence  
Let `dp[i][j]` = max coins from `(i,j)` exclusive.  
- `dp[i][j] = max(dp[i][k] + nums[i]*nums[k]*nums[j] + dp[k][j])` for `k` in `(i+1…j-1)`

### Approach  
1. Pad `nums` with 1 at both ends.  
2. Let `n = nums.size()+2`. Build `dp[n][n]` zero.  
3. Consider subarrays of length `len=2…n`:  
   - For `i=0…n-len`, `j=i+len`, loop `k=i+1…j-1`, update `dp[i][j]`.  
4. Return `dp[0][n-1]`.

### C++ Snippet  
```cpp
int maxCoins(vector<int>& A) {
    int n = A.size();
    vector<int> nums(n+2,1);
    for (int i = 0; i < n; ++i) nums[i+1] = A[i];
    int m = n+2;
    vector<vector<int>> dp(m, vector<int>(m));
    for (int len = 2; len < m; ++len) {
        for (int i = 0; i + len < m; ++i) {
            int j = i + len;
            for (int k = i + 1; k < j; ++k) {
                dp[i][j] = max(dp[i][j],
                  dp[i][k] + nums[i]*nums[k]*nums[j] + dp[k][j]);
            }
        }
    }
    return dp[0][m-1];
}
```

Time: O(n³)  
Space: O(n²)

---

## 11. Regular Expression Matching  
Link: https://leetcode.com/problems/regular-expression-matching/

### Description  
Implement regex matching with `.` and `*`, where `*` means zero or more of previous element.

### Recurrence  
- If `p[j-1]!='*'`:  
  `dp[i][j] = dp[i-1][j-1] && (s[i-1]==p[j-1] || p[j-1]=='.')`  
- If `p[j-1]=='*'`:  
  `dp[i][j] = dp[i][j-2]` (zero)  
   `|| (dp[i-1][j] && (s[i-1]==p[j-2] || p[j-2]=='.'))` (one/more)

### Approach  
1. Let `m,n` lengths; build `(m+1)×(n+1)` table.  
2. `dp[0][0]=true`. Initialize `dp[0][j]` for `*` patterns.  
3. Fill with above recurrence row by row.  
4. Return `dp[m][n]`.

### C++ Snippet  
```cpp
bool isMatch(string s, string p) {
    int m = s.size(), n = p.size();
    vector<vector<bool>> dp(m+1, vector<bool>(n+1));
    dp[0][0] = true;
    for (int j = 2; j <= n; ++j)
        if (p[j-1]=='*') dp[0][j] = dp[0][j-2];
    for (int i = 1; i <= m; ++i) {
        for (int j = 1; j <= n; ++j) {
            if (p[j-1] != '*') {
                dp[i][j] = dp[i-1][j-1] &&
                   (s[i-1]==p[j-1] || p[j-1]=='.');
            } else {
                dp[i][j] = dp[i][j-2] || 
                  (dp[i-1][j] &&
                   (s[i-1]==p[j-2] || p[j-2]=='.'));
            }
        }
    }
    return dp[m][n];
}
```

Time: O(m·n)  
Space: O(m·n)

---

## 🔍 Common 2‑D DP Patterns  

- Grid walking (Unique Paths)  
- Sequence alignment (LCS, Edit Distance)  
- Stock/states DP with extra dimension (Cooldown)  
- Coin/knapsack in 2D or transformed to 1D (Coin Change II, Target Sum)  
- String interleaving & subsequence count (Interleaving String, Distinct Subsequences)  
- DFS + memo in 2D (Longest Increasing Path)  
- Interval DP (Burst Balloons)  
- Regex/state machine DP (Regular Expression Matching)  

### Complexity Summary  

| Problem                                         | Time             | Space             |
|-------------------------------------------------|------------------|-------------------|
| Unique Paths                                    | O(m·n)           | O(n)              |
| Longest Common Subsequence                      | O(m·n)           | O(n)              |
| Stock with Cooldown                             | O(n)             | O(1)              |
| Coin Change II                                  | O(amount·k)      | O(amount)         |
| Target Sum                                      | O(n·sum)         | O(sum)            |
| Interleaving String                             | O(n·m)           | O(m)              |
| Longest Increasing Path in Matrix               | O(m·n)           | O(m·n)            |
| Distinct Subsequences                           | O(m·n)           | O(n)              |
| Edit Distance                                   | O(m·n)           | O(n)              |
| Burst Balloons                                  | O(n³)            | O(n²)             |
| Regular Expression Matching                     | O(m·n)           | O(m·n)            |

---

## 🚀 Advanced Tips & Variants  

- **Space Reduction**: roll 2D tables into 1D when dependencies allow.  
- **Monotonic Queue**: optimize sliding-window DP recurrence in 2D.  
- **Divide & Conquer DP**: use in problems like convex hull trick or SMAWK.  
- **Bitmask & State Compression**: for small dimensions combine indexes into bits.  
- **Pattern DP**: model unusual rules (regex, interleaving) as finite‑state transitions.  
- **Interval DP**: classic for partitioning and merging problems (Matrix chain, Balloon burst).  

Happy coding with 2‑D DP patterns!  