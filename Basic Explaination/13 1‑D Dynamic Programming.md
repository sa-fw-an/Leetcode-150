<!--markdownlint-disable-->
# 📚 1‑D Dynamic Programming

This guide covers twelve classic LeetCode problems under the **1‑D Dynamic Programming** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Recurrence / core DP idea  
3. Step‑by‑step approach  
4. A concise C++ snippet with inline comments  
5. Complexity analysis  
6. Advanced tips & related variants  

---

## 1. Climbing Stairs  
Link: https://leetcode.com/problems/climbing-stairs/

### Description  
You climb a staircase of `n` steps. Each time you can climb 1 or 2 steps. In how many distinct ways can you reach the top?

### Recurrence  
- dp[i] = dp[i-1] + dp[i-2]  

### Approach  
1. Base cases: dp[0] = 1 (one way to stand still), dp[1] = 1.  
2. For i from 2 to n, compute dp[i] = dp[i-1] + dp[i-2].  
3. Return dp[n].

### C++ Snippet  
```cpp
int climbStairs(int n) {
    vector<int> dp(n+1);
    dp[0] = 1; dp[1] = 1;
    for (int i = 2; i <= n; ++i) {
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n];
}
```

Time: O(n)  
Space: O(n) → O(1) by rolling two variables

---

## 2. Min Cost Climbing Stairs  
Link: https://leetcode.com/problems/min-cost-climbing-stairs/

### Description  
Given an array `cost[i]` to step on stair i, you can start at 0 or 1 and move 1 or 2 steps. What is the minimum cost to reach beyond the last stair?

### Recurrence  
- dp[i] = cost[i] + min(dp[i-1], dp[i-2])  

### Approach  
1. Let `n = cost.size()`.  
2. dp[0] = cost[0], dp[1] = cost[1].  
3. For i from 2 to n-1: dp[i] = cost[i] + min(dp[i-1], dp[i-2]).  
4. Answer = min(dp[n-1], dp[n-2]).

### C++ Snippet  
```cpp
int minCostClimbingStairs(vector<int>& cost) {
    int n = cost.size();
    int a = cost[0], b = cost[1];
    for (int i = 2; i < n; ++i) {
        int cur = cost[i] + min(a, b);
        a = b;
        b = cur;
    }
    // pay to step on last or second last
    return min(a, b);
}
```

Time: O(n)  
Space: O(1)

---

## 3. House Robber  
Link: https://leetcode.com/problems/house-robber/

### Description  
Given non‑negative `nums[i]`, maximize sum of chosen elements with no two adjacent picks.

### Recurrence  
- dp[i] = max(dp[i-1], dp[i-2] + nums[i])  

### Approach  
1. Base: dp[0] = nums[0], dp[1] = max(nums[0], nums[1]).  
2. For i from 2 to n-1, dp[i] = max(dp[i-1], dp[i-2] + nums[i]).  
3. Return dp[n-1].

### C++ Snippet  
```cpp
int rob(vector<int>& nums) {
    int prev2 = 0, prev1 = 0;
    for (int x : nums) {
        int cur = max(prev1, prev2 + x);
        prev2 = prev1;
        prev1 = cur;
    }
    return prev1;
}
```

Time: O(n)  
Space: O(1)

---

## 4. House Robber II  
Link: https://leetcode.com/problems/house-robber-ii/

### Description  
Houses arranged in a circle. Cannot rob adjacent houses; first and last are neighbors. Maximize loot.

### Recurrence  
- Solve two linear runs: houses[0..n-2] and houses[1..n-1] with House Robber I.

### Approach  
1. If n == 1, return nums[0].  
2. Define `robLine(a, b)` as House Robber on subarray [a..b].  
3. Answer = max(robLine(0,n-2), robLine(1,n-1)).

### C++ Snippet  
```cpp
int robRange(vector<int>& nums, int l, int r) {
    int prev2 = 0, prev1 = 0;
    for (int i = l; i <= r; ++i) {
        int cur = max(prev1, prev2 + nums[i]);
        prev2 = prev1;
        prev1 = cur;
    }
    return prev1;
}

int rob(vector<int>& nums) {
    int n = nums.size();
    if (n == 1) return nums[0];
    return max(robRange(nums, 0, n-2),
               robRange(nums, 1, n-1));
}
```

Time: O(n)  
Space: O(1)

---

## 5. Longest Palindromic Substring  
Link: https://leetcode.com/problems/longest-palindromic-substring/

### Description  
Given string `s`, find the longest palindromic substring.

### Recurrence  
- dp[i][j] = (s[i]==s[j]) && (j-i<3 || dp[i+1][j-1])  

### Approach  
1. Let `n = s.size()`.  
2. dp table of size n×n, initialized false.  
3. Expand substrings by length from 1 to n.  
4. Track maximum window.

### C++ Snippet  
```cpp
string longestPalindrome(string s) {
    int n = s.size(), start = 0, maxLen = 1;
    vector<vector<bool>> dp(n, vector<bool>(n,false));
    for (int len = 1; len <= n; ++len) {
        for (int i = 0; i + len - 1 < n; ++i) {
            int j = i + len - 1;
            if (s[i] == s[j] &&
               (len < 3 || dp[i+1][j-1])) {
                dp[i][j] = true;
                if (len > maxLen) {
                    start = i;
                    maxLen = len;
                }
            }
        }
    }
    return s.substr(start, maxLen);
}
```

Time: O(n²)  
Space: O(n²) → Manacher’s algorithm O(n)

---

## 6. Palindromic Substrings  
Link: https://leetcode.com/problems/palindromic-substrings/

### Description  
Count all palindromic substrings in `s`.

### Recurrence  
Same dp as Longest Palindrome, but count each dp[i][j] == true.

### Approach  
1. Build dp table with same recurrence.  
2. Increment counter whenever dp[i][j] = true.

### C++ Snippet  
```cpp
int countSubstrings(string s) {
    int n = s.size(), ans = 0;
    vector<vector<bool>> dp(n, vector<bool>(n,false));
    for (int len = 1; len <= n; ++len) {
        for (int i = 0; i + len - 1 < n; ++i) {
            int j = i + len - 1;
            if (s[i] == s[j] &&
               (len < 3 || dp[i+1][j-1])) {
                dp[i][j] = true;
                ans++;
            }
        }
    }
    return ans;
}
```

Time: O(n²)  
Space: O(n²) → Center‐expansion O(n²) time, O(1) space

---

## 7. Decode Ways  
Link: https://leetcode.com/problems/decode-ways/

### Description  
A digit string maps `1→A`, …, `26→Z`. Count how many ways to decode it.

### Recurrence  
- dp[i] = dp[i-1] if s[i] ≠ '0'  
- plus dp[i-2] if two‐digit s[i-1..i] ∈ [10,26]

### Approach  
1. Let `n = s.size()`.  
2. dp[0] = 1; dp[1] = (s[0]!='0').  
3. For i from 2 to n:  
   - If s[i-1]!='0', dp[i] += dp[i-1].  
   - If substring [i-2..i-1] between "10" and "26", dp[i] += dp[i-2].  

### C++ Snippet  
```cpp
int numDecodings(string s) {
    int n = s.size();
    if (n==0 || s[0]=='0') return 0;
    vector<int> dp(n+1);
    dp[0] = 1; dp[1] = 1;
    for (int i = 2; i <= n; ++i) {
        if (s[i-1] != '0')
            dp[i] += dp[i-1];
        int two = stoi(s.substr(i-2,2));
        if (two >= 10 && two <= 26)
            dp[i] += dp[i-2];
    }
    return dp[n];
}
```

Time: O(n)  
Space: O(n) → O(1) with two variables

---

## 8. Coin Change  
Link: https://leetcode.com/problems/coin-change/

### Description  
Given coin denominations `coins[]` and amount, compute the fewest coins needed to make up the amount; return -1 if impossible.

### Recurrence  
- dp[a] = min(dp[a], dp[a-coin]+1) for each coin  

### Approach  
1. Initialize dp[0]=0, dp[1..amount]=∞.  
2. For a from 1 to amount:  
   - For each coin ≤ a, dp[a] = min(dp[a], dp[a-coin]+1).  
3. Return dp[amount] or -1.

### C++ Snippet  
```cpp
int coinChange(vector<int>& coins, int amount) {
    const int INF = amount+1;
    vector<int> dp(amount+1, INF);
    dp[0] = 0;
    for (int a = 1; a <= amount; ++a) {
        for (int c : coins) {
            if (c <= a)
                dp[a] = min(dp[a], dp[a-c] + 1);
        }
    }
    return dp[amount] > amount ? -1 : dp[amount];
}
```

Time: O(amount × n_coins)  
Space: O(amount)

---

## 9. Maximum Product Subarray  
Link: https://leetcode.com/problems/maximum-product-subarray/

### Description  
Given nums[], find subarray with maximum product. Zeros and negatives complicate tracking.

### Recurrence  
- At i, track `maxProd[i]` and `minProd[i]`  
- maxProd[i] = max(nums[i], nums[i]*maxProd[i-1], nums[i]*minProd[i-1])  
- minProd similarly

### Approach  
1. Initialize `maxP = minP = ans = nums[0]`.  
2. For i from 1 to n-1:  
   - If nums[i]<0, swap(maxP,minP).  
   - maxP = max(nums[i], maxP*nums[i])  
   - minP = min(nums[i], minP*nums[i])  
   - ans = max(ans, maxP).

### C++ Snippet  
```cpp
int maxProduct(vector<int>& nums) {
    int maxP = nums[0], minP = nums[0], ans = nums[0];
    for (int i = 1; i < nums.size(); ++i) {
        if (nums[i] < 0) swap(maxP, minP);
        maxP = max(nums[i], maxP * nums[i]);
        minP = min(nums[i], minP * nums[i]);
        ans = max(ans, maxP);
    }
    return ans;
}
```

Time: O(n)  
Space: O(1)

---

## 10. Word Break  
Link: https://leetcode.com/problems/word-break/

### Description  
Given string `s` and dictionary `wordDict`, determine if `s` can be segmented into space‑separated dictionary words.

### Recurrence  
- dp[i] = OR over j < i: dp[j] and s[j..i-1] in dict  

### Approach  
1. dp[0] = true.  
2. For i from 1 to n:  
   - For j from 0 to i-1:  
     - If dp[j] and `s.substr(j,i-j)` in set, dp[i] = true, break.  
3. Return dp[n].

### C++ Snippet  
```cpp
bool wordBreak(string s, vector<string>& dict) {
    int n = s.size();
    unordered_set<string> st(dict.begin(), dict.end());
    vector<bool> dp(n+1, false);
    dp[0] = true;
    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j < i; ++j) {
            if (dp[j] && st.count(s.substr(j, i-j))) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[n];
}
```

Time: O(n² · m) where m = avg word length  
Space: O(n)

---

## 11. Longest Increasing Subsequence  
Link: https://leetcode.com/problems/longest-increasing-subsequence/

### Description  
Return length of longest strictly increasing subsequence in `nums`.

### Recurrence  
- dp[i] = 1 + max(dp[j]) for all j < i where nums[j] < nums[i]  

### Approach  
1. O(n²) DP: compute dp[i] as above, track max.  
2. O(n log n) patience sorting: maintain `tails[]`, for each x use `lower_bound` to replace or append.

### C++ Snippet (n log n)  
```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;
    for (int x : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end())
            tails.push_back(x);
        else
            *it = x;
    }
    return tails.size();
}
```

Time: O(n log n)  
Space: O(n)

---

## 12. Partition Equal Subset Sum  
Link: https://leetcode.com/problems/partition-equal-subset-sum/

### Description  
Given nums[], determine if it can be partitioned into two subsets with equal sum.

### Recurrence  
- Subset sum: dp[a] = dp[a] OR dp[a - num]  

### Approach  
1. Compute total sum; if odd return false.  
2. Let target = sum/2.  
3. dp[0] = true; for each num in nums:  
   - For a from target down to num:  
     dp[a] = dp[a] || dp[a - num].  
4. Return dp[target].

### C++ Snippet  
```cpp
bool canPartition(vector<int>& nums) {
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if (sum % 2) return false;
    int target = sum / 2;
    vector<bool> dp(target + 1, false);
    dp[0] = true;
    for (int num : nums) {
        for (int a = target; a >= num; --a) {
            dp[a] = dp[a] || dp[a - num];
        }
    }
    return dp[target];
}
```

Time: O(n × sum)  
Space: O(sum)

---

## 🔍 Common 1‑D DP Patterns  

- **Linear Recurrence**: Fibonacci‐style (Climbing Stairs)  
- **Prefix‐State**: dp[i] depends on dp[i-1], dp[i-2], etc.  
- **Sliding Window DP**: keep only last k states for O(1) space  
- **Two‑State Tracking**: max/min products (Max Product Subarray)  
- **1‑D Boolean Knapsack**: subset sum, word break  
- **Patience Sorting**: LIS in O(n log n)  

### Complexity Summary  

| Problem                               | Time                        | Space               |
|---------------------------------------|-----------------------------|---------------------|
| Climbing Stairs                       | O(n)                        | O(1)                |
| Min Cost Climbing Stairs              | O(n)                        | O(1)                |
| House Robber                          | O(n)                        | O(1)                |
| House Robber II                       | O(n)                        | O(1)                |
| Longest Palindromic Substring         | O(n²)                       | O(n²)               |
| Palindromic Substrings                | O(n²)                       | O(n²)               |
| Decode Ways                           | O(n)                        | O(1)                |
| Coin Change                           | O(amount × coins)           | O(amount)           |
| Maximum Product Subarray              | O(n)                        | O(1)                |
| Word Break                            | O(n²·m)                     | O(n)                |
| Longest Increasing Subsequence        | O(n log n)                  | O(n)                |
| Partition Equal Subset Sum            | O(n × sum)                  | O(sum)              |

---

## 🚀 Advanced Tips & Variants  

- **Space Optimization**: reduce multi‑dimensional DP to 1‑D or O(1) by rolling arrays.  
- **Manacher’s Algorithm**: longest palindrome in O(n).  
- **Memoized DFS**: many DP problems can be recast recursively with memo tables.  
- **Bitmask DP**: solve small n subset problems in O(n·2ⁿ).  
- **Monotonic Queue**: optimize windowed DP recurrences in O(n).  
- **State Compression**: pack small states into bits for constant extra space.  

Happy coding and mastering 1‑D DP patterns!  