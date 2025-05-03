<!--markdownlint-disable-->
# 📚 Sliding Window (Advanced Beginner‑Friendly Master Guide)

This guide preserves the original structure of six classic LeetCode **Sliding Window** problems, while teaching every concept from first principles. For each problem:

1. **Theory**: Underlying data structure and algorithmic idea in plain English.  
2. **Approach Walkthrough**: A small concrete example illustrating each step.  
3. **Detailed Code Breakdown**: Line‑by‑line commentary on the C++ snippet.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.

After the problems, you’ll find:

- **Expanded Helper Functions** with usage guidance.  
- **Deep Dive** into each sliding‑window paradigm.  
- **Next Steps to Mastery**: follow‑up problems, advanced variants, and debugging checklists.

---

## 1. Best Time to Buy and Sell Stock  
Link: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

### Theory: One‑Pass Tracking of Minimum and Profit  
We want the maximum difference `prices[j] - prices[i]` with `j > i`. As we scan daily prices left to right, we keep:

- `minPrice`: the lowest price seen so far (best day to buy).  
- `maxProfit`: the largest profit achievable by selling on the current day.

At each price `p`, compute `potential = p - minPrice`. Update `maxProfit` if `potential` is higher. Then update `minPrice` if `p` is lower.

This is a **greedy sliding‑min** approach, O(n) time, O(1) space.

### Approach Walkthrough  
Given `prices = [7,1,5,3,6,4]`:

1. Initialize `minPrice = +∞`, `maxProfit = 0`.  
2. Day 0, price=7:  
   - `minPrice = min(+∞,7) = 7`  
   - `potential = 7 - 7 = 0`, `maxProfit = max(0,0) = 0`  
3. Day 1, price=1:  
   - `minPrice = min(7,1) = 1`  
   - `potential = 1 - 1 = 0`, `maxProfit = 0`  
4. Day 2, price=5:  
   - `potential = 5 - 1 = 4`, `maxProfit = max(0,4) = 4`  
5. Day 3, price=3:  
   - `potential = 3 - 1 = 2`, `maxProfit = 4`  
6. Day 4, price=6:  
   - `potential = 6 - 1 = 5`, `maxProfit = 5`  
7. Day 5, price=4:  
   - `potential = 4 - 1 = 3`, `maxProfit = 5`  
Final answer: **5**.

### Detailed Code Breakdown  
```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = INT_MAX;     // 1) Start with a huge minPrice.
    int maxProfit = 0;          // 2) Best profit seen so far.

    for (int p : prices) {      // 3) Process each day's price.
        // 4) Update the lowest purchase price.
        minPrice = min(minPrice, p);
        // 5) Compute profit if we sell at today's price.
        int potential = p - minPrice;
        // 6) Keep the best profit.
        maxProfit = max(maxProfit, potential);
    }
    return maxProfit;           // 7) Return max profit after scanning all days.
}
```
- Line 1–2: Initialize trackers.  
- Line 4: `minPrice` holds the smallest price seen so far.  
- Line 5: `potential` is the profit if we buy at `minPrice` and sell today.  
- Line 6: Update `maxProfit` greedily.

#### Pitfalls & Tips  
- Don’t swap update order: you must compute `potential` after updating `minPrice`.  
- Watch integer limits when prices are near `INT_MAX`.  
- If the array is empty, return 0 by default.

---

## 2. Longest Substring Without Repeating Characters  
Link: https://leetcode.com/problems/longest-substring-without-repeating-characters/

### Theory: Variable‑Size Window with Last‑Seen Index  
We maintain a sliding window `[left..right]` of unique characters. When we see a character that last appeared at index `j ≥ left`, we must move `left` to `j+1` to eliminate the repeat. A `lastIndex` array (size 256) remembers the most recent position of each character.

Time: O(n), Space: O(1) (fixed 256‑element array).

### Approach Walkthrough  
`"abcabcbb"`:

- Start `left=0`, `maxLen=0`.  
- `right=0`: char='a', lastIndex['a']=-1 < left → window=[0..0], len=1.  
- `right=1`: 'b' → unique, len=2.  
- `right=2`: 'c' → unique, len=3.  
- `right=3`: 'a', lastIndex['a']=0 ≥ left → shift `left=0+1=1`. Window now [1..3], len=3.  
- Continue scanning, updating `maxLen=3`.

### Detailed Code Breakdown  
```cpp
int lengthOfLongestSubstring(const string& s) {
    vector<int> lastIndex(256, -1);  // 1) Initialize last seen positions to -1.
    int maxLen = 0;                  // 2) Track longest window length.
    int left = 0;                    // 3) Left boundary of window.

    for (int right = 0; right < s.size(); ++right) {
        char c = s[right];
        // 4) If c seen after or at left, move left past its prior index.
        if (lastIndex[c] >= left) {
            left = lastIndex[c] + 1;
        }
        // 5) Update last seen index for c.
        lastIndex[c] = right;
        // 6) Compute current window length.
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;                   // 7) Return the maximum length found.
}
```
- Line 4: Ensures window contains no duplicates.  
- Line 6: Window size is `right - left + 1`.

#### Pitfalls & Tips  
- Using a map instead of fixed array works for Unicode but costs log n per lookup.  
- Always compare `lastIndex[c]` with `left` to avoid shrinking incorrectly.  
- Reset `lastIndex` when processing multiple test cases.

---

## 3. Longest Repeating Character Replacement  
Link: https://leetcode.com/problems/longest-repeating-character-replacement/

### Theory: Window + Greedy on Maximum Frequency  
We expand a window and maintain:

- `count[26]`: frequency of each uppercase letter in window.  
- `maxCount`: the highest frequency in the current window.  

If `(window_size - maxCount) > k`, we need more than `k` replacements to make all characters equal, so shrink from the left by decrementing counts.

Time: O(n), Space: O(1) (26‑length array).

### Approach Walkthrough  
`s = "AABABBA", k = 1`:

1. Expand right to include `"AAB"`. `count['A']=2`, `count['B']=1`, `maxCount=2`, window size=3, replacements needed=1 ≤ k → len=3.  
2. Expand to `"AABA"`. `count['A']=3`, `maxCount=3`, size=4, replace=1 ≤ k → len=4.  
3. Expand to `"AABAB"`. `count['B']=2`, `maxCount=3`, size=5, replace=2 > k → shrink left to `"ABAB"`. Continue similarly → answer=4.

### Detailed Code Breakdown  
```cpp
int characterReplacement(const string& s, int k) {
    vector<int> count(26, 0);       // 1) Frequency array for 'A'–'Z'.
    int left = 0;                   // 2) Window start.
    int maxCount = 0;               // 3) Highest freq in window.
    int maxLen = 0;                 // 4) Best window length.

    for (int right = 0; right < s.size(); ++right) {
        // 5) Include new char in window.
        char c = s[right];
        count[c - 'A']++;
        // 6) Update maxCount if this char is now most frequent.
        maxCount = max(maxCount, count[c - 'A']);

        // 7) If we need more than k replacements, shrink window.
        while ((right - left + 1) - maxCount > k) {
            count[s[left] - 'A']--; // remove left char
            left++;                 // shrink from left
        }
        // 8) Update answer after valid window.
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;                  // 9) Return best length.
}
```
- Line 6: We never decrease `maxCount` when shrinking; this is safe and keeps O(n) time.  
- The window always represents a valid solution after each iteration.

#### Pitfalls & Tips  
- Don’t recalculate `maxCount` by scanning the array inside the loop—that becomes O(n²).  
- Ensure `while` condition uses **current** window size minus `maxCount`.

---

## 4. Permutation in String  
Link: https://leetcode.com/problems/permutation-in-string/

### Theory: Fixed‑Size Window + Frequency Comparison  
We check every substring of length `n = s1.size()` in `s2` to see if it’s a permutation of `s1`. We maintain two 26‑element arrays:

- `need`: frequency of each letter in `s1`.  
- `window`: frequency of current window in `s2`.

Slide the window one character at a time and compare arrays in O(26)=O(1).

### Approach Walkthrough  
`s1="ab"`, `s2="eidbaooo"`:

1. `need = {a:1,b:1}`.  
2. Initial window `"ei"`: `window={e:1,i:1}` ≠ `need`.  
3. Slide to `"id"`, `"db"`, `"ba"`: at `"ba"`, `window={a:1,b:1}` = `need` → return **true**.

### Detailed Code Breakdown  
```cpp
bool checkInclusion(const string& s1, const string& s2) {
    int n = s1.size(), m = s2.size();
    if (n > m) return false;           // 1) s1 longer than s2 → impossible.

    vector<int> need(26, 0), window(26, 0);
    // 2) Build need from s1.
    for (char c : s1) 
        ++need[c - 'a'];

    // 3) Initialize window for first n characters of s2.
    for (int i = 0; i < n; ++i)
        ++window[s2[i] - 'a'];

    if (window == need) return true;    // 4) Check first window.

    // 5) Slide window across s2.
    for (int i = n; i < m; ++i) {
        ++window[s2[i] - 'a'];          // add new char on right
        --window[s2[i - n] - 'a'];      // remove old char on left
        if (window == need)             // compare arrays
            return true;
    }
    return false;                       // 6) No permutation found.
}
```
- Array comparison `window == need` is O(26).  
- Sliding adds/removes exactly one character per step.

#### Pitfalls & Tips  
- Failing to check the initial window before sliding.  
- Using maps instead of arrays increases overhead—arrays are faster for fixed alphabets.

---

## 5. Minimum Window Substring  
Link: https://leetcode.com/problems/minimum-window-substring/

### Theory: Expand and Contract Window with Matched Count  
We need the smallest substring in `s` containing all characters of `t` with correct frequencies. Maintain:

- `need`: map of required counts for each character in `t`.  
- `window`: map of counts in current window.  
- `formed`: number of characters whose window count meets the need.  
- `required`: total unique characters in `t`.

Expand `right` until `formed == required`, then contract `left` to minimize the window.

Time: O(|s| + |t|), Space: O(|s| + |t|).

### Approach Walkthrough  
`s = "ADOBECODEBANC", t = "ABC"`:

1. `need = {A:1,B:1,C:1}`, `required=3`.  
2. Expand to `"ADOBEC"`, `formed=3`.  
3. Contract from left to shorten: `"DOBEC"` still has all → `"BEC"` → `"BC"` (invalid when you remove 'E'). Best so far `"BEC"`.  
4. Continue scanning → `"BANC"` → best `"BANC"` length=4. Final answer `"BANC"`.

### Detailed Code Breakdown  
```cpp
string minWindow(const string& s, const string& t) {
    if (s.empty() || t.empty()) return ""; 
    unordered_map<char,int> need, window;
    // 1) Build need map.
    for (char c : t) 
        need[c]++;

    int required = need.size();   // 2) Unique chars needed.
    int formed = 0;               // 3) How many satisfy need.
    int left = 0, right = 0;
    int minLen = INT_MAX, start = 0;

    // 4) Expand window by moving right.
    while (right < s.size()) {
        char c = s[right++];
        window[c]++;
        if (window[c] == need[c]) 
            formed++;

        // 5) Contract window when fully formed.
        while (left < right && formed == required) {
            // Update answer if smaller window found.
            if (right - left < minLen) {
                minLen = right - left;
                start = left;
            }
            // Remove char at left from window.
            char d = s[left++];
            if (window[d]-- == need[d]) 
                formed--;
        }
    }
    return (minLen == INT_MAX) ? "" : s.substr(start, minLen);
}
```
- Lines 5–6: We only increment `formed` when a window count matches the exact need.  
- Contraction continues while the window remains valid.

#### Pitfalls & Tips  
- Off‑by‑one on substring boundaries: use `right - left`.  
- Always decrement `formed` only when `window[d]` drops below `need[d]`.

---

## 6. Sliding Window Maximum  
Link: https://leetcode.com/problems/sliding-window-maximum/

### Theory: Monotonic Deque for O(1) Max Query  
We maintain a deque of indices whose corresponding values are in **decreasing** order from front to back. The front always stores the index of the maximum in the current window. As we advance index `i`:

- **Evict** indices from front if they fall out of the window (`i - dq.front() >= k`).  
- **Pop** from back while the new value `nums[i]` ≥ `nums[dq.back()]`.  
- **Push** `i` onto the back.  
- Once `i >= k-1`, the current max is `nums[dq.front()]`.

Time: O(n), Space: O(k).

### Approach Walkthrough  
`nums = [1,3,-1,-3,5,3,6,7]`, `k=3`:

1. Build first window `[1,3,-1]` → deque holds indices `[1,0]` (values `[3,1]`), max=3.  
2. Slide to include `-3`: pop none, max stays 3.  
3. Slide to `5`: pop back until deque empty, then push index of 5 → max=5.  
Continue similarly → result `[3,3,5,5,6,7]`.

### Detailed Code Breakdown  
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;                // 1) Will store indices, front = max.
    vector<int> res;              // 2) Output array.

    for (int i = 0; i < nums.size(); ++i) {
        // 3) Remove indices out of this window's left bound.
        if (!dq.empty() && dq.front() <= i - k)
            dq.pop_front();

        // 4) Maintain decreasing order: pop back smaller values.
        while (!dq.empty() && nums[i] >= nums[dq.back()])
            dq.pop_back();

        // 5) Add current index.
        dq.push_back(i);

        // 6) Starting at i = k-1, record front as max.
        if (i >= k - 1)
            res.push_back(nums[dq.front()]);
    }
    return res;                   // 7) Return all window maxima.
}
```
- Step 3 ensures the max index is within `[i-k+1..i]`.  
- Step 4 enforces that the deque holds potential maxima in order.

#### Pitfalls & Tips  
- Forgetting to pop out‑of‑window indices before querying maximum.  
- Confusing values vs. indices when comparing `nums[i]` with `nums[dq.back()]`.

---

## 🔧 Expanded Helper Functions  

```cpp
// Print a vector of ints (e.g., maxSlidingWindow result).
void printVec(const vector<int>& v) {
    for (int x : v) 
        cout << x << ' ';
    cout << "\n";
}

// Print a pair of integers (e.g., result of two‑pointer problems).
void printPair(int a, int b) {
    cout << "(" << a << ", " << b << ")" << "\n";
}

// Print a string with quotes (e.g., substrings).
void printStr(const string& s) {
    cout << '"' << s << '"' << "\n";
}
```
- **Why it works**: simple iteration and formatted output.  
- **When to use**: debugging sliding window indices, window contents, or final results.  
- **Adaptation**: wrap in `#ifdef DEBUG` to disable in production.

---

## 🔍 Deep Dive into Paradigms

### 1. Greedy One‑Pass (Sliding Min)  
- **Key Idea**: maintain a running best (min or max) and update an aggregate difference.  
- **Invariant**: at each step, `minPrice` is the minimum of all previous values.

### 2. Variable‑Size Window  
- **Key Idea**: expand until validity breaks, then contract until it’s restored.  
- **Invariant**: window always either grows to satisfy or shrinks to restore validity.  

### 3. Fixed‑Size Window  
- **Key Idea**: slide a constant‑length window, maintain O(1) updates per step.  
- **Invariant**: window boundaries move in lockstep; data structures track entering/exiting elements.

### 4. Monotonic Deque  
- **Key Idea**: store only “candidates” for maximum in order, drop irrelevant ones.  
- **Invariant**: deque is always in strict decreasing order of `nums[]` values.  

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problem Progression  
- **Easy → Medium → Hard**  
  - Best Time to Buy/Sell → Best Time II (multiple trades) → Best Time with transaction fee  
  - Longest Unique Substring → At Most K Distinct → Longest Repeating after Replacement  
  - Permutation in String → Find All Anagrams → Subarrays with K Distinct  
  - Minimum Window → Substring with Concatenation of All Words → Shortest Subarray with Sum ≥ K  
  - Sliding Window Maximum → Sliding Window Minimum → Sliding Window Median  

### Advanced Variants & Applications  
- Real‑time stream processing: maintain sliding‑window aggregates in logs or telemetry.  
- Reservoir sampling combined with sliding windows.  
- Image processing: sliding window convolution.  

### Debugging Tips & Mental Checklists  
1. **Window Bounds**: confirm `left` and `right` indices correctly represent your window.  
2. **Data Structure Invariants**: for deques or counts, ensure you update both on expand and contract.  
3. **Edge Cases**: empty input, `k=0`, `k>n`, all identical elements, strictly increasing/decreasing sequences.  
4. **Performance**: check that inner loops (shrinking/cleaning deques) still guarantee overall O(n).  

Keep practicing these patterns until they become second nature. Happy coding!  