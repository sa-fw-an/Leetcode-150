<!--markdownlint-disable-->
# 📚 Two Pointers

This guide covers five classic LeetCode problems under the **Two Pointers** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Valid Palindrome  
Link: https://leetcode.com/problems/valid-palindrome/  

### Description  
Given a string, determine whether it reads the same forwards and backwards after removing non‑alphanumeric characters and ignoring case.

### Core Idea: Two‐Pointer Scan  
1. Initialize left pointer `i` at start, right pointer `j` at end.  
2. Advance `i` until it points to an alphanumeric character.  
3. Retreat `j` until it points to an alphanumeric character.  
4. Compare lowercase forms of `s[i]` and `s[j]`. If mismatch → not a palindrome.  
5. Move both pointers inward and repeat until they cross.

### C++ Code Snippet: Alphanumeric Palindrome Check  
```cpp
// Checks if s is a palindrome ignoring non-alphanumerics and case.
bool isPalindrome(string s) {
    int i = 0, j = s.size() - 1;
    while (i < j) {
        // Skip left non-alphanumeric
        while (i < j && !isalnum(s[i])) ++i;
        // Skip right non-alphanumeric
        while (i < j && !isalnum(s[j])) --j;
        // Compare lowercase characters
        if (tolower(s[i]) != tolower(s[j])) return false;
        ++i;  // move inward
        --j;
    }
    return true;
}
```

---

## 2. Two Sum II ‑ Input Array Is Sorted  
Link: https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/  

### Description  
Given a **sorted** array and a target, return 1‑based indices of the two numbers that add up to the target.

### Core Idea: Two‐Pointer from Ends  
1. Set `left = 0`, `right = n-1`.  
2. Compute sum = `nums[left] + nums[right]`.  
3. If sum matches target, return `{left+1, right+1}`.  
4. If sum < target, increment `left`; else decrement `right`.  
5. Repeat until found.

### C++ Code Snippet: Sorted Two‑Sum with Two Pointers  
```cpp
// Finds two indices (1-based) in a sorted array that sum to target.
vector<int> twoSumSorted(const vector<int>& nums, int target) {
    int left = 0, right = nums.size() - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            return {left + 1, right + 1};  // convert to 1-based
        } else if (sum < target) {
            ++left;   // need larger sum
        } else {
            --right;  // need smaller sum
        }
    }
    return {};  // guaranteed one solution by problem
}
```

---

## 3. 3Sum  
Link: https://leetcode.com/problems/3sum/  

### Description  
Given an array, find all unique triplets `[a,b,c]` such that `a + b + c = 0`, with no duplicate triplets.

### Core Idea: Sort + Two‐Pointer for Each Anchor  
1. Sort the array.  
2. For each index `i` (anchor), skip duplicates.  
3. Set two pointers `l = i+1`, `r = n-1`.  
4. Sum = `nums[i] + nums[l] + nums[r]`.  
   - If zero, record triplet, move `l` & `r` skipping duplicates.  
   - If sum < 0, `++l`; else `--r`.  
5. Continue until `l >= r`.

### C++ Code Snippet: Three‑Sum via Two Pointers  
```cpp
// Returns all unique triplets that sum to zero.
vector<vector<int>> threeSum(vector<int>& nums) {
    sort(nums.begin(), nums.end());           // sort for two-pointer
    vector<vector<int>> res;
    int n = nums.size();
    for (int i = 0; i < n - 2; ++i) {
        if (i && nums[i] == nums[i - 1]) continue;  // skip anchor duplicates
        int l = i + 1, r = n - 1;
        while (l < r) {
            int sum = nums[i] + nums[l] + nums[r];
            if (sum == 0) {
                res.push_back({nums[i], nums[l], nums[r]});
                // skip duplicates for l and r
                while (l < r && nums[l] == nums[l + 1]) ++l;
                while (l < r && nums[r] == nums[r - 1]) --r;
                ++l; --r;
            } else if (sum < 0) {
                ++l;  // need larger sum
            } else {
                --r;  // need smaller sum
            }
        }
    }
    return res;
}
```

---

## 4. Container With Most Water  
Link: https://leetcode.com/problems/container-with-most-water/  

### Description  
Given an array of heights, find two lines that together with the x-axis form a container holding the most water.

### Core Idea: Two‐Pointer Shrinking Window  
1. Place `left` at start, `right` at end.  
2. Compute area = `min(height[left], height[right]) * (right - left)`.  
3. Move the pointer at the shorter line inward (to possibly find taller line).  
4. Repeat, tracking max area, until pointers meet.

### C++ Code Snippet: Max Area Two Pointers  
```cpp
// Computes the maximum water container area.
int maxArea(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int best = 0;
    while (left < right) {
        // width * shorter height
        int area = min(height[left], height[right]) * (right - left);
        best = max(best, area);
        // move inward from shorter side
        if (height[left] < height[right]) {
            ++left;
        } else {
            --right;
        }
    }
    return best;
}
```

---

## 5. Trapping Rain Water  
Link: https://leetcode.com/problems/trapping-rain-water/  

### Description  
Given an elevation map, compute how much water it can trap after raining.

### Core Idea: Two‐Pointer with Left/Right Max  
1. Initialize `left=0`, `right=n-1`, `leftMax=0`, `rightMax=0`.  
2. While `left < right`:  
   - Update `leftMax = max(leftMax, height[left])`.  
   - Update `rightMax = max(rightMax, height[right])`.  
   - Whichever side has lower max:  
     - Add `leftMax - height[left]` if leftMax ≤ rightMax, then `++left`.  
     - Else add `rightMax - height[right]`, then `--right`.

### C++ Code Snippet: Two‐Pointer Rainwater Calculation  
```cpp
// Calculates total trapped rain water.
int trap(vector<int>& height) {
    int left = 0, right = height.size() - 1;
    int leftMax = 0, rightMax = 0;
    int total = 0;
    while (left < right) {
        // update bounds
        leftMax = max(leftMax, height[left]);
        rightMax = max(rightMax, height[right]);
        // trap on lower side
        if (leftMax <= rightMax) {
            total += leftMax - height[left];
            ++left;
        } else {
            total += rightMax - height[right];
            --right;
        }
    }
    return total;
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

// Print a 2D vector
void print2D(const vector<vector<int>>& a) {
    for (auto& row : a) {
        for (int x : row) cout << x << ' ';
        cout << '\n';
    }
}
```

---

## 🔍 Algorithmic Paradigms  
- Two‐Pointer Scanning (Valid Palindrome)  
- Two‐Pointer from Ends on Sorted Data (Two Sum II, Container With Most Water)  
- Sort + Two‐Pointer Window (3Sum)  
- Two‐Pointer with Running Max (Trapping Rain Water)  

### Complexity Summary  
| Problem                               | Time            | Space             |
|---------------------------------------|-----------------|-------------------|
| Valid Palindrome                      | O(n)            | O(1)              |
| Two Sum II (Sorted)                   | O(n)            | O(1)              |
| 3Sum                                  | O(n²)           | O(1) (output size)|
| Container With Most Water             | O(n)            | O(1)              |
| Trapping Rain Water                   | O(n)            | O(1)              |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Carefully handle pointer increments/decrements to avoid infinite loops.  
- **Medium**: Combine sorting with two pointers to reduce brute‐force from O(n³) to O(n²).  
- **Hard**: Maintain running state (e.g., leftMax/rightMax) to compute aggregates in one pass.

### Follow‑Up Problems  
- **Palindrome Variants**: Valid Palindrome II, Longest Palindromic Substring  
- **Two‐Sum Variants**: 4Sum, Two Sum IV – Input is a BST  
- **k‐Sum**: Generalize 3Sum to kSum with recursion + two pointers  
- **Max Containers**: Container With Most Water II (circular)  
- **Water Trapping**: Trapping Rain Water II (2D version)  

### Common Pitfalls & Debugging  
- Off‑by‑one when moving pointers.  
- Forgetting to skip duplicates in multi‑pointer loops (3Sum).  
- Miscomputing trapped water when updating leftMax/rightMax.  
- Assuming sorted inputs when they’re not.  

Happy coding and mastering Two Pointers!  