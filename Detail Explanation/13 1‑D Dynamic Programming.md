<!--markdownlint-disable-->
# 📚 1‑D Dynamic Programming (Advanced Beginner‑Friendly Master Guide)

This guide dives deep into twelve classic LeetCode **1‑D Dynamic Programming** problems. Each section includes:

1. **Theory**: Why the recurrence works.  
2. **Approach Walkthrough**: A small example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary on the C++ solution.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  
5. **Complexity Analysis**: Time and space costs.  
6. **Advanced Tips & Variants**: Further optimizations or related problems.

---

## 1. Climbing Stairs  
Link: https://leetcode.com/problems/climbing-stairs/

### Theory  
Each step can be 1 or 2 steps — the number of ways to reach step `i` is the sum of ways to reach `i-1` and `i-2`. This is the Fibonacci recurrence.

### Approach Walkthrough  
For `n=4`:  
- Ways to 0: 1 (base)  
- Ways to 1: 1  
- Ways to 2: ways(1)+ways(0)=2  
- Ways to 3: ways(2)+ways(1)=3  
- Ways to 4: ways(3)+ways(2)=5

### Code Breakdown  
```cpp
int climbStairs(int n) {
    vector<int> dp(n+1);
    dp[0] = 1;               // 1) One way to stay at the bottom.
    dp[1] = 1;               // 2) One way to take a single step.
    for (int i = 2; i <= n; ++i) {
        dp[i] = dp[i-1] + dp[i-2];  
        // 3) Recurrence: sum of previous two.
    }
    return dp[n];            // 4) Ways to reach step n.
}
```

Pitfalls & Tips  
- For large `n`, store only the last two values to reduce space to O(1).

Complexity  
- Time: O(n)  
- Space: O(n) → O(1) with rolling variables

---

## 2. Min Cost Climbing Stairs  
Link: https://leetcode.com/problems/min-cost-climbing-stairs/

### Theory  
You pay cost[i] when you step on stair `i`. To minimize total cost, dp[i] = cost[i] + min(dp[i-1], dp[i-2]).

### Approach Walkthrough  
`cost = [10,15,20]`  
- dp[0]=10, dp[1]=15  
- dp[2]=cost[2]+min(10,15)=20+10=30  
- You can finish at either stair 1 or 2, so answer = min(dp[1], dp[2]) = 15.

### Code Breakdown  
```cpp
int minCostClimbingStairs(vector<int>& cost) {
    int n = cost.size();
    int a = cost[0], b = cost[1];       // 1) dp[0], dp[1]
    for (int i = 2; i < n; ++i) {
        int cur = cost[i] + min(a, b);  // 2) dp[i] = cost[i] + min(prev1, prev2)
        a = b;                          // 3) shift window
        b = cur;
    }
    return min(a, b);                   // 4) finish at last or second-last
}
```

Pitfalls & Tips  
- Remember you can start at step 0 or 1, so final result is min of last two dp values.

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 3. House Robber  
Link: https://leetcode.com/problems/house-robber/

### Theory  
At each house `i`, choose max of either skipping (`dp[i-1]`) or robbing it plus `dp[i-2]`: dp[i] = max(dp[i-1], dp[i-2] + nums[i]).

### Approach Walkthrough  
`nums = [2,7,9,3,1]`  
- best up to 0: 2  
- up to 1: max(2,7)=7  
- up to 2: max(7,2+9)=11  
- up to 3: max(11,7+3)=11  
- up to 4: max(11,11+1)=12

### Code Breakdown  
```cpp
int rob(vector<int>& nums) {
    int prev2 = 0, prev1 = 0;
    for (int x : nums) {
        int cur = max(prev1, prev2 + x);  
        // choose to rob (prev2+x) or skip (prev1)
        prev2 = prev1;                   
        prev1 = cur;
    }
    return prev1;                        
}
```

Pitfalls & Tips  
- Track two rolling variables to avoid O(n) space.

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 4. House Robber II  
Link: https://leetcode.com/problems/house-robber-ii/

### Theory  
Houses on a circle → first and last are adjacent. Solve two linear robber problems excluding one end each, then take the max.

### Approach Walkthrough  
`nums = [2,3,2]`  
- Ignore last: solve [2,3] → 3  
- Ignore first: solve [3,2] → 3  
- Answer = 3

### Code Breakdown  
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
    // max of two runs
    return max(robRange(nums, 0, n-2),
               robRange(nums, 1, n-1));
}
```

Pitfalls & Tips  
- Handle `n==1` separately to avoid index errors.

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 5. Longest Palindromic Substring  
Link: https://leetcode.com/problems/longest-palindromic-substring/

### Theory  
Use a DP table where `dp[i][j]` is true if `s[i..j]` is a palindrome. Recurrence:  
```
dp[i][j] = (s[i]==s[j]) && (j-i<3 || dp[i+1][j-1])
```

### Approach Walkthrough  
`s = "babad"`  
- Substrings of length 1 are palindromes.  
- Check length 2 and up, expanding window, updating max length and start index.

### Code Breakdown  
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

Pitfalls & Tips  
- This DP is O(n²) time and space.  
- Manacher’s algorithm can reduce time to O(n).

Complexity  
- Time: O(n²)  
- Space: O(n²)

---

## 6. Palindromic Substrings  
Link: https://leetcode.com/problems/palindromic-substrings/

### Theory  
Same DP as Longest Palindromic Substring, but count all `dp[i][j] = true`.

### Approach Walkthrough  
`s = "aaa"`  
- Substrings: "a"(3), "aa"(2), "aaa"(1) → total 6.

### Code Breakdown  
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
                ans++;  // count this palindrome
            }
        }
    }
    return ans;
}
```

Pitfalls & Tips  
- Center‑expansion method uses O(1) space with two-pointer expansion at each center.

Complexity  
- Time: O(n²)  
- Space: O(n²)

---

## 7. Decode Ways  
Link: https://leetcode.com/problems/decode-ways/

### Theory  
Each digit or valid two-digit number maps to a letter. At position `i`,  
```
dp[i] = (s[i]!='0' ? dp[i-1] : 0) 
      + (10 <= s[i-2..i-1] <= 26 ? dp[i-2] : 0)
```

### Approach Walkthrough  
`s = "226"`  
- dp[0]=1, dp[1]= (2!='0')?1:0 =1  
- two-digit "22"? yes → +dp[-1]=1 → dp[2]=2  
- dp[3]: s[2]='6' → +dp[2]=2; "26"? yes → +dp[1]=1 → dp[3]=3

### Code Breakdown  
```cpp
int numDecodings(string s) {
    int n = s.size();
    if (n == 0 || s[0] == '0') return 0;
    vector<int> dp(n+1);
    dp[0] = 1;  
    dp[1] = 1;  

    for (int i = 2; i <= n; ++i) {
        if (s[i-1] != '0')
            dp[i] += dp[i-1];                   // single‑digit decode
        int two = stoi(s.substr(i-2,2));
        if (two >= 10 && two <= 26)
            dp[i] += dp[i-2];                   // two‑digit decode
    }
    return dp[n];
}
```

Pitfalls & Tips  
- Handle leading '0' carefully to avoid invalid parses.

Complexity  
- Time: O(n)  
- Space: O(n) → O(1) with two variables

---

## 8. Coin Change  
Link: https://leetcode.com/problems/coin-change/

### Theory  
Unbounded knapsack: to form amount `a`, try each coin `c` and use dp[a-c] + 1.

### Approach Walkthrough  
`coins=[1,2,5], amount=11`  
- dp[0]=0  
- dp[1]=min(dp[0]+1)=1, …, dp[11]=min(dp[10]+1, dp[9]+1, dp[6]+1)=3

### Code Breakdown  
```cpp
int coinChange(vector<int>& coins, int amount) {
    const int INF = amount + 1;
    vector<int> dp(amount+1, INF);
    dp[0] = 0;                        // 1) Zero coins to make 0

    for (int a = 1; a <= amount; ++a) {
        for (int c : coins) {
            if (c <= a)
                dp[a] = min(dp[a], dp[a-c] + 1);
        }
    }
    return dp[amount] > amount ? -1 : dp[amount];
}
```

Pitfalls & Tips  
- Initialize dp with `amount+1` (greater than any possible count).  
- Iteration order ensures you can reuse coins unlimited times.

Complexity  
- Time: O(amount × n_coins)  
- Space: O(amount)

---

## 9. Maximum Product Subarray  
Link: https://leetcode.com/problems/maximum-product-subarray/

### Theory  
Because a negative number flips sign, track both `maxP` and `minP` up to index `i`.

### Approach Walkthrough  
`nums=[2,3,-2,4]`  
- i=0: maxP=2,minP=2  
- i=1: maxP=6,minP=3  
- i=2: swap→ maxP=-2,minP=-6  
- i=3: maxP=max(4,-8)=4, minP=min(4,-24)=-24

### Code Breakdown  
```cpp
int maxProduct(vector<int>& nums) {
    int maxP = nums[0], minP = nums[0], ans = nums[0];
    for (int i = 1; i < nums.size(); ++i) {
        if (nums[i] < 0) swap(maxP, minP);     // 1) flip on negative
        maxP = max(nums[i], maxP * nums[i]);   // 2) best ending here
        minP = min(nums[i], minP * nums[i]);   // 3) worst (most negative)
        ans = max(ans, maxP);                  // 4) global best
    }
    return ans;
}
```

Pitfalls & Tips  
- Always swap `maxP` and `minP` before updating if the current is negative.

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 10. Word Break  
Link: https://leetcode.com/problems/word-break/

### Theory  
Segment string `s` into dictionary words: dp[i] is true if any `dp[j]` and `s[j..i-1]` in the set.

### Approach Walkthrough  
`s="leetcode"`, `dict={"leet","code"}`  
- dp[0]=true  
- dp[4]=dp[0]&&"leet"→true  
- dp[8]=dp[4]&&"code"→true

### Code Breakdown  
```cpp
bool wordBreak(string s, vector<string>& dict) {
    int n = s.size();
    unordered_set<string> st(dict.begin(), dict.end());
    vector<bool> dp(n+1,false);
    dp[0] = true;                     // 1) empty string valid

    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j < i; ++j) {
            if (dp[j] && st.count(s.substr(j, i-j))) {
                dp[i] = true;         // 2) found a valid cut
                break;
            }
        }
    }
    return dp[n];
}
```

Pitfalls & Tips  
- Substring calls in inner loop can be costly—use rolling hash or trie for optimization.

Complexity  
- Time: O(n² · m) where m = avg word length  
- Space: O(n)

---

## 11. Longest Increasing Subsequence  
Link: https://leetcode.com/problems/longest-increasing-subsequence/

### Theory  
Patience sorting: maintain an array `tails` where `tails[k]` = smallest ending value of an increasing subsequence of length `k+1`.

### Approach Walkthrough  
`nums=[10,9,2,5,3,7,101,18]`  
- tails evolves: [10] → [9] → [2] → [2,5] → [2,3] → [2,3,7] → [2,3,7,101] → [2,3,7,18] → length 4

### Code Breakdown  
```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;
    for (int x : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end())
            tails.push_back(x);         // 1) extend sequence
        else
            *it = x;                    // 2) replace to keep minimal tail
    }
    return tails.size();               // 3) length of LIS
}
```

Pitfalls & Tips  
- `lower_bound` finds the first element ≥ x.  
- This method gives length but not the sequence itself.

Complexity  
- Time: O(n log n)  
- Space: O(n)

---

## 12. Partition Equal Subset Sum  
Link: https://leetcode.com/problems/partition-equal-subset-sum/

### Theory  
Subset-sum DP: decide whether to include each number in a half-sum target. Use 1‑D boolean DP from high to low.

### Approach Walkthrough  
`nums=[1,5,11,5]`, sum=22 → target=11  
- dp[0]=true  
- with 1: dp[1]=true  
- with 5: dp[6]=true, dp[5]=true  
- ... eventually dp[11]=true

### Code Breakdown  
```cpp
bool canPartition(vector<int>& nums) {
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if (sum % 2) return false;             // 1) odd → impossible
    int target = sum / 2;
    vector<bool> dp(target+1,false);
    dp[0] = true;                          // 2) zero sum achievable

    for (int num : nums) {
        for (int a = target; a >= num; --a) {
            dp[a] = dp[a] || dp[a - num];  // 3) include or exclude
        }
    }
    return dp[target];                     // 4) can we reach half sum?
}
```

Pitfalls & Tips  
- Iterate `a` downward to avoid using a number multiple times in one iteration.

Complexity  
- Time: O(n × sum)  
- Space: O(sum)

---

## 🔧 Common 1‑D DP Patterns

- **Linear Recurrence**: Fibonacci‑style (Climbing Stairs)  
- **Rolling Window**: keep last k states for O(1) space  
- **Two‑State Tracking**: max/min (Max Product Subarray)  
- **Unbounded Knapsack**: coin change, min cost stairs  
- **0/1 Knapsack & Subset‑Sum**: partition, word break  
- **Patience Sorting**: LIS in O(n log n)  
- **DP on Strings**: palindrome, decode ways

---

## 🚀 Next Steps to Mastery

- **Manacher’s Algorithm**: longest palindrome in O(n).  
- **Memoized Recursion**: rewrite DP as top‑down with cache.  
- **Bitmask DP**: for small n subsets (O(n·2ⁿ)).  
- **Monotonic Queue**: optimize sliding-window recurrences.  
- **State Compression**: pack small state sets into bits.  
- **Advanced Exercises**:  
  - **Profit Optimization**: stock trading with cooldown/fees  
  - **Grid DP**: unique paths with obstacles  
  - **Tree DP**: house robber on trees  
  - **Game Theory DP**: “can win” states

Happy coding and conquering 1‑D DP!  