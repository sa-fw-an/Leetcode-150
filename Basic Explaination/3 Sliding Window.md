<!--markdownlint-disable-->
# 📚 Sliding Window

This guide covers six classic LeetCode problems under the **Sliding Window** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Best Time to Buy and Sell Stock  
Link: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

### Description  
You’re given an array `prices` where `prices[i]` is the stock price on day `i`. You may complete **one** transaction (buy one and sell one share). Find the maximum profit.

### Core Idea: One-Pass Tracking (Sliding Min)  
1. Keep track of the lowest price seen so far (`minPrice`).  
2. At each day `i`, compute potential profit = `prices[i] - minPrice`.  
3. Update `maxProfit` if this profit is larger.  
4. Update `minPrice` if `prices[i]` is lower.  
5. End with the largest profit found.

### C++ Snippet: Single Transaction Max Profit  
```cpp
// Solves "Best Time to Buy and Sell Stock" by scanning once
int maxProfit(vector<int>& prices) {
    int minPrice = INT_MAX;   // lowest price up to current day
    int maxProfit = 0;        // best profit so far
    for (int p : prices) {
        // If current price is lower, update minPrice
        minPrice = min(minPrice, p);
        // Calculate profit if sold today
        int profit = p - minPrice;
        // Update maxProfit if this is larger
        maxProfit = max(maxProfit, profit);
    }
    return maxProfit;
}
```

---

## 2. Longest Substring Without Repeating Characters  
Link: https://leetcode.com/problems/longest-substring-without-repeating-characters/

### Description  
Given a string `s`, find the length of the longest substring without repeating characters.

### Core Idea: Variable‑Size Sliding Window  
1. Use two pointers `left` and `right` to define a window.  
2. Expand `right`, adding characters to a map that stores the last index.  
3. If a repeating character is encountered inside the window, move `left` to one past its previous index.  
4. Track the window’s maximum size as `right - left + 1`.

### C++ Snippet: Dynamic Window with Index Map  
```cpp
// Finds longest substring with all unique chars
int lengthOfLongestSubstring(const string& s) {
    vector<int> lastIndex(256, -1);  // last seen index for each char
    int maxLen = 0;
    int left = 0;                     // window start
    for (int right = 0; right < s.size(); ++right) {
        char c = s[right];
        // If c appeared inside current window, shrink from left
        if (lastIndex[c] >= left) {
            left = lastIndex[c] + 1;
        }
        lastIndex[c] = right;        // update last seen pos
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

---

## 3. Longest Repeating Character Replacement  
Link: https://leetcode.com/problems/longest-repeating-character-replacement/

### Description  
Given a string `s` and integer `k`, you can replace up to `k` characters in `s` to make all characters in a substring the same. Return the length of the longest such substring.

### Core Idea: Sliding Window + Max Frequency  
1. Expand a window with `right`, track counts of each letter.  
2. Keep `maxCount` = highest frequency in the window.  
3. If window size minus `maxCount` > `k`, shrink from `left` and decrement counts.  
4. The window size is always a valid candidate; update `maxLen`.

### C++ Snippet: Window with Frequency and Greedy Shrink  
```cpp
// Finds longest substring after ≤k replacements to uniform char
int characterReplacement(const string& s, int k) {
    vector<int> count(26, 0);
    int left = 0, maxCount = 0, maxLen = 0;
    for (int right = 0; right < s.size(); ++right) {
        // Increment count for new char
        maxCount = max(maxCount, ++count[s[right] - 'A']);
        // If too many replacements needed, shrink window
        while ((right - left + 1) - maxCount > k) {
            --count[s[left] - 'A'];
            ++left;
        }
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

---

## 4. Permutation in String  
Link: https://leetcode.com/problems/permutation-in-string/

### Description  
Given strings `s1` and `s2`, return **true** if `s2` contains a permutation of `s1`.

### Core Idea: Fixed‑Size Sliding Window + Frequency Comparison  
1. Build a frequency array `need[26]` for `s1`.  
2. Slide a window of length `s1.size()` over `s2`, maintaining a `window[26]` count.  
3. Compare `need` and `window` at each step; if equal, return true.  
4. Update the window counts by adding the new char and removing the old one.

### C++ Snippet: Fixed Window with Two Count Arrays  
```cpp
// Checks if any substring of s2 is a permutation of s1
bool checkInclusion(const string& s1, const string& s2) {
    int n = s1.size(), m = s2.size();
    if (n > m) return false;
    vector<int> need(26, 0), window(26, 0);
    // Build need from s1
    for (char c : s1) ++need[c - 'a'];
    // Initialize first window in s2
    for (int i = 0; i < n; ++i) ++window[s2[i] - 'a'];
    if (window == need) return true;
    // Slide the window
    for (int i = n; i < m; ++i) {
        ++window[s2[i] - 'a'];         // add new char
        --window[s2[i - n] - 'a'];     // remove old char
        if (window == need) return true;
    }
    return false;
}
```

---

## 5. Minimum Window Substring  
Link: https://leetcode.com/problems/minimum-window-substring/

### Description  
Given strings `s` and `t`, find the smallest substring in `s` that contains all characters of `t`.

### Core Idea: Two‑Pointer + Match Count  
1. Build `need` map for `t`.  
2. Expand `right`, updating `window` counts and `formed` when a need is met.  
3. When `formed == required` (all chars covered), try to shrink from `left` to minimize.  
4. Track the smallest window seen.  

### C++ Snippet: Expand & Contract Window with Formed Count  
```cpp
// Finds smallest substring of s covering all of t
string minWindow(const string& s, const string& t) {
    if (s.empty() || t.empty()) return "";
    unordered_map<char, int> need, window;
    for (char c : t) ++need[c];
    int required = need.size();
    int formed = 0;
    int left = 0, right = 0;
    int minLen = INT_MAX, start = 0;
    // Expand window
    while (right < s.size()) {
        char c = s[right++];
        if (++window[c] == need[c]) 
            ++formed;
        // Contract window when all chars matched
        while (left < right && formed == required) {
            if (right - left < minLen) {
                minLen = right - left;
                start = left;
            }
            char d = s[left++];
            if (--window[d] < need[d]) 
                --formed;
        }
    }
    return (minLen == INT_MAX) ? "" : s.substr(start, minLen);
}
```

---

## 6. Sliding Window Maximum  
Link: https://leetcode.com/problems/sliding-window-maximum/

### Description  
Given an array `nums` and window size `k`, return an array of the maximum value in each sliding window.

### Core Idea: Monotonic Deque  
1. Maintain a deque of indices whose corresponding values are in **decreasing** order.  
2. For each index `i`:  
   - Remove indices from back while `nums[i]` ≥ `nums[deque.back()]`.  
   - Push `i` to back.  
   - Remove front if it’s out of window (`i - deque.front() >= k`).  
   - When `i >= k - 1`, record `nums[deque.front()]` as the window’s max.

### C++ Snippet: Deque‑Based O(n) Max Tracking  
```cpp
// Returns maximum of each sliding window of size k
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;             // stores indices, front = max
    vector<int> res;
    for (int i = 0; i < nums.size(); ++i) {
        // Pop smaller values from back
        while (!dq.empty() && nums[i] >= nums[dq.back()]) 
            dq.pop_back();
        dq.push_back(i);
        // Remove out-of-window indices
        if (dq.front() <= i - k) 
            dq.pop_front();
        // Record max once the first window is complete
        if (i >= k - 1) 
            res.push_back(nums[dq.front()]);
    }
    return res;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Print a vector of ints
void printVec(const vector<int>& v) {
    for (int x : v) cout << x << ' ';
    cout << '\n';
}

// Print a string with quotes
void printStr(const string& s) {
    cout << '"' << s << '"' << '\n';
}
```

---

## 🔍 Algorithmic Paradigms  
- Variable‑size sliding window (Longest Substring, Min Window)  
- Fixed‑size sliding window (Permutation in String, Sliding Window Maximum)  
- Greedy one‑pass (Best Time to Buy & Sell)  
- Monotonic deque (Max Sliding Window)  

### Complexity Summary  
| Problem                                  | Time            | Space             |
|------------------------------------------|-----------------|-------------------|
| Best Time to Buy and Sell Stock          | O(n)            | O(1)              |
| Longest Substring Without Repeats       | O(n)            | O(1) (256‑char)   |
| Longest Repeating Character Replacement  | O(n)            | O(1) (26‑char)    |
| Permutation in String                    | O(n)            | O(1) (26‑char)    |
| Minimum Window Substring                 | O(|s| + |t|)    | O(|s| + |t|)      |
| Sliding Window Maximum                   | O(n)            | O(k)              |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: For fixed-length windows, maintain counts/structures in O(1) per step.  
- **Medium**: Dynamically expand/contract and track validity (`formed` vs. `required`).  
- **Hard**: Use specialized structures (monotonic deque) to optimize queries.

### Follow‑Up Problems  
- At Most K Distinct Characters  
- Subarrays with Sum = K  
- Longest Substring with At Most Two Distinct  
- Sliding Window Median  
- Shortest Subarray with Sum at Least K  

### Common Pitfalls & Debugging  
- Off‑by‑one when contracting or recording window result.  
- Forgetting to update counts when moving `left`.  
- Comparing whole arrays (O(26) each) vs. tracking a match count.  
- Handling empty or edge‑case inputs (`k > n`, empty strings).  

Happy sliding‑window coding!  