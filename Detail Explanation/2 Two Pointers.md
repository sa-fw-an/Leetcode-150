<!--markdownlint-disable-->
# 📚 Two Pointers (Advanced Beginner‑Friendly Master Guide)

Welcome to your step‑by‑step master guide on **Two Pointers**. We’ll preserve the original structure—problem titles, links, and code blocks—while teaching every concept from first principles. Each problem section includes:

1. **Theory**: The underlying idea in plain English.  
2. **Approach Walkthrough**: A worked example to illustrate each step.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary on every line.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  

After the problems, you’ll find:

- **Expanded Helper Functions** with usage guidance.  
- **Deep Dive** into each algorithmic paradigm.  
- **Next Steps to Mastery** with follow‑up problems, advanced variants, and debugging checklists.

Let’s dive in!

---

## 1. Valid Palindrome  
Link: https://leetcode.com/problems/valid-palindrome/

### Theory: Two‐Pointer Scan Over a Filtered String  
A palindrome reads the same forwards and backwards. If we ignore non‑alphanumeric characters and case, we can check using two pointers:

- **Left pointer** starts at the beginning.  
- **Right pointer** starts at the end.  
- Skip over any characters that are not letters or digits.  
- Compare the lowercase of each character under the pointers.  
- Move inward until the pointers cross.

This runs in O(n) time and O(1) extra space.

### Approach Walkthrough  
Example: `s = "A man, a plan, a canal: Panama"`  
1. `i = 0`→'A', `j = 29`→'a'. Both alphanumeric: compare `tolower('A') == tolower('a')` → `a==a`.  
2. Move `i=1`→' ', skip until `i=2`→'m'; move `j` backward past punctuation to `j=27`→'m'. Compare → match.  
3. Continue skipping spaces/punctuation until all letters match.  
4. Pointers cross → it’s a palindrome → return **true**.

### Detailed Code Breakdown  
```cpp
// Checks if s is a palindrome ignoring non‑alphanumeric characters and case.
bool isPalindrome(string s) {
    int i = 0,                    // left pointer
        j = s.size() - 1;         // right pointer
    while (i < j) {               // process until pointers meet or cross
        // Skip left non‑alphanumeric
        while (i < j && !isalnum(s[i])) 
            ++i;
        // Skip right non‑alphanumeric
        while (i < j && !isalnum(s[j])) 
            --j;
        // Compare lowercase forms
        if (tolower(s[i]) != tolower(s[j])) 
            return false;         // mismatch → not a palindrome
        ++i;                     // move both pointers inward
        --j;
    }
    return true;                  // no mismatches → palindrome
}
```
Line‑by‑line:
1. Declare pointers `i` and `j`.  
2. Loop while `i < j`.  
3–4. Advance `i` until it points at alphanumeric or meets `j`.  
5–6. Retreat `j` similarly.  
7. Compare `tolower(s[i])` vs. `tolower(s[j])`.  
8. Return false on mismatch.  
9–10. Move pointers inward.  
11. Return true if finished without mismatches.

#### Pitfalls & Tips  
- `isalnum` and `tolower` require `<cctype>`.  
- Always check `i < j` in your skip loops to avoid going out of bounds.  
- Converting the entire string to a filtered lowercase array first is clear but uses O(n) extra space—this in‑place method uses O(1).

---

## 2. Two Sum II ‑ Input Array Is Sorted  
Link: https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/

### Theory: Two‐Pointer from Ends on a Sorted Array  
With a sorted array, you can find two numbers that sum to a target by:

- Setting **left** at index 0 and **right** at index n–1.  
- Compute sum = `nums[left] + nums[right]`.  
- If sum > target, decrease `right` to reduce sum.  
- If sum < target, increase `left` to increase sum.  
- Repeat until you find the exact target.

This runs in O(n) time and O(1) space.

### Approach Walkthrough  
Example: `nums = [1,2,4,6,10], target = 8`  
1. `left=0` (1), `right=4` (10) → sum=11 >8 → `--right`.  
2. `left=0` (1), `right=3` (6) → sum=7 <8 → `++left`.  
3. `left=1` (2), `right=3` (6) → sum=8 == target → return `{2,4}` (1‑based).

### Detailed Code Breakdown  
```cpp
// Finds indices (1‑based) of two numbers that add to target in sorted nums.
vector<int> twoSumSorted(const vector<int>& nums, int target) {
    int left = 0,                           // start pointer
        right = nums.size() - 1;            // end pointer
    while (left < right) {                  // until pointers meet
        int sum = nums[left] + nums[right]; // compute current sum
        if (sum == target) {
            // Found exact match: convert to 1‑based indices
            return {left + 1, right + 1};
        } else if (sum < target) {
            ++left;    // need a larger sum
        } else {
            --right;   // need a smaller sum
        }
    }
    return {};       // by problem statement, solution always exists
}
```
Key points:
- We never revisit a pair twice.  
- The sorted property lets us discard one end each step.  

#### Pitfalls & Tips  
- Remember to return 1‑based indices.  
- If you forget to check `left < right` in the while condition, you may overshoot.  
- This approach only works when the input is guaranteed sorted.

---

## 3. 3Sum  
Link: https://leetcode.com/problems/3sum/

### Theory: Sort + Two‐Pointer for Each Anchor  
To find unique triplets summing to zero:

1. **Sort** the array.  
2. Iterate an **anchor** index `i` over the array.  
3. Use two pointers `l = i+1` and `r = n-1` to search for `-(nums[i])`.  
4. Skip duplicates at both anchor and pointers to avoid repeating triplets.

Time: O(n²) due to the nested two‑pointer scan per anchor. Space: O(1) aside from output.

### Approach Walkthrough  
Example: `nums = [-1,0,1,2,-1,-4]`  
1. Sort → `[-4,-1,-1,0,1,2]`.  
2. `i=0` anchor=-4, need sum=4 with l=1, r=5 → -1+2=1 <4 → `++l` … until no match.  
3. `i=1` anchor=-1, need sum=1. l=2,r=5: -1+2=1 → found `[-1,-1,2]`. Skip duplicates: l→3,r→4. 0+1=1 → found `[-1,0,1]`.  
4. Continue, skipping duplicate anchors at `i=2`.  

Result: `[[-1,-1,2],[-1,0,1]]`.

### Detailed Code Breakdown  
```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    sort(nums.begin(), nums.end());         // 1) Sort to enable two‑pointer.
    vector<vector<int>> res;
    int n = nums.size();

    for (int i = 0; i < n - 2; ++i) {
        // Skip duplicate anchors to prevent repeating triplets
        if (i > 0 && nums[i] == nums[i - 1]) 
            continue;

        int l = i + 1,                      // left pointer
            r = n - 1;                      // right pointer

        while (l < r) {
            int sum = nums[i] + nums[l] + nums[r];
            if (sum == 0) {
                // Record valid triplet
                res.push_back({nums[i], nums[l], nums[r]});
                // Skip duplicates for left pointer
                while (l < r && nums[l] == nums[l + 1]) ++l;
                // Skip duplicates for right pointer
                while (l < r && nums[r] == nums[r - 1]) --r;
                ++l; --r;                   // move both inward after recording
            } else if (sum < 0) {
                ++l;  // sum too small → increase it
            } else {
                --r;  // sum too large → decrease it
            }
        }
    }
    return res;
}
```
Highlights:
- Sorting costs O(n log n); each anchor yields an O(n) two‑pointer scan.  
- Duplicate skipping ensures uniqueness.

#### Pitfalls & Tips  
- Forgetting to skip duplicate anchors or pointer values leads to duplicate triplets.  
- Always move both pointers after finding a match to avoid infinite loops.

---

## 4. Container With Most Water  
Link: https://leetcode.com/problems/container-with-most-water/

### Theory: Two‐Pointer Shrinking Window  
View the heights as vertical lines on the x‑axis. The container area between two lines at `i` and `j` is:
```
area = min(height[i], height[j]) * (j - i)
```
To maximize area:

- Start pointers at ends: widest width.  
- Compute area.  
- Move the pointer at the shorter line inward—this may increase the height but reduces width.  
- Repeat until pointers meet.

Runs in O(n) time, O(1) space.

### Approach Walkthrough  
Example: `height = [1,8,6,2,5,4,8,3,7]`  
1. `l=0 (1), r=8 (7)` → area = 1×8 = 8. Shorter is l → `++l`.  
2. `l=1 (8), r=8 (7)` → area = 7×7 = 49. Shorter is r → `--r`.  
3. Continue updating best area until pointers cross.  
Best = 49.

### Detailed Code Breakdown  
```cpp
int maxArea(vector<int>& height) {
    int left = 0,                       // left pointer
        right = height.size() - 1,     // right pointer
        best = 0;                      // track max area

    while (left < right) {
        int h = min(height[left], height[right]); 
        int w = right - left;
        best = max(best, h * w);       // update best if current area is larger

        // Move pointer at shorter line inward
        if (height[left] < height[right]) {
            ++left;
        } else {
            --right;
        }
    }
    return best;
}
```
Key observations:
- Always moving the shorter side is safe because moving the taller side cannot increase area.
- Width shrinks by 1 each time, ensuring O(n) steps.

#### Pitfalls & Tips  
- Confusing which pointer to move—remember: moving the taller side cannot yield a larger area.  
- Be careful with off‑by‑one in width calculation (`right - left`).

---

## 5. Trapping Rain Water  
Link: https://leetcode.com/problems/trapping-rain-water/

### Theory: Two‐Pointer with Running Maxes  
To compute trapped water, at each index `i`, water = `min(max_left, max_right) – height[i]`. We can maintain two pointers and two running maxima:

- **left** and **right** pointers.  
- **leftMax** = max height encountered so far from the left side.  
- **rightMax** = max height encountered so far from the right side.  
- Always move the side with the smaller max, because that side’s max bounds the water level.

This yields O(n) time and O(1) space.

### Approach Walkthrough  
Example: `height = [0,2,0,3,0,1,0,2]`  
Initialize `left=0, right=7, leftMax=0, rightMax=0, total=0`.  
1. `height[left]=0 ≤ height[right]=2`; update `leftMax=0`; add `0–0=0`; `++left`.  
2. `height[1]=2 > height[7]=2?` equal, treat as left side; `leftMax=2`; add `2–2=0`; `++left`.  
3. Next, `height[2]=0 ≤ rightMax(2)`... and so on. Sum contributes at positions where `height[i] < min(leftMax, rightMax)`.

### Detailed Code Breakdown  
```cpp
int trap(vector<int>& height) {
    int left = 0,                      // start pointer
        right = height.size() - 1,     // end pointer
        leftMax = 0,                   // highest wall from left
        rightMax = 0,                  // highest wall from right
        total = 0;                     // accumulated water

    while (left < right) {
        // Update the running maxima
        leftMax = max(leftMax, height[left]);
        rightMax = max(rightMax, height[right]);

        // The smaller max bounds the water level
        if (leftMax <= rightMax) {
            total += leftMax - height[left]; 
            ++left;                     // move left pointer inward
        } else {
            total += rightMax - height[right];
            --right;                    // move right pointer inward
        }
    }
    return total;
}
```
Why this works:
- Whichever side has the smaller max guarantees the water level on that side is limited by that max.
- Moving the smaller side ensures we never miss trapped water.

#### Pitfalls & Tips  
- Swapping `leftMax` and `rightMax` logic breaks correctness—always move the lower side.  
- Forgetting to update `leftMax`/`rightMax` before computing trapped water yields wrong totals.

---

## 🔧 Expanded Helper Functions

```cpp
// Print a 1D vector of ints (e.g., Two Sum, maxArea results).
void printVec(const vector<int>& v) {
    for (int x : v) cout << x << ' ';
    cout << '\n';
}

// Print a 2D vector of ints (e.g., 3Sum triplets).
void print2D(const vector<vector<int>>& a) {
    for (auto& row : a) {
        for (int x : row) cout << x << ' ';
        cout << '\n';
    }
}
```
- **Why it works**: simple loops over container elements.  
- **When to use**: debugging, visual verification of intermediate steps.  
- **How to adapt**:  
  - Change types (e.g., `vector<string>`).  
  - Add delimiters (commas, newlines).  
  - Wrap in conditional debug flags to remove in production.

---

## 🔍 Deep Dive into Paradigms

### Two‐Pointer Scan  
- **Invariant**: pointers move inward, skipping irrelevant data.  
- **Use when**: you compare pairs symmetrically (palindrome, linked list cycles).  

### Two‐Pointer from Ends on Sorted Data  
- **Invariant**: if sum too large, moving right inward decreases sum; if sum too small, moving left inward increases sum.  
- **Use when**: array is sorted and you search for pairs.

### Sort + Two‐Pointer Window  
- **Invariant**: sorting groups duplicates, then a sliding window finds combinations in O(n²) instead of O(n³).  
- **Use when**: looking for k‑sum with k ≥ 3 (anchor + two pointers).

### Two‐Pointer with Running Max  
- **Invariant**: the smaller of two running maxima determines water level on that side.  
- **Use when**: computing aggregates bounded by two moving borders (rainwater, trapping).

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Valid Palindrome II** (allow one deletion) → **Longest Palindromic Substring**  
- **4Sum**, **Two Sum IV – Input is a BST**  
- General **k‑Sum** with recursion + two pointers  
- **Container With Most Water II** (circular)  
- **Trapping Rain Water II** (2D elevation map)

### Advanced Variants & Applications  
- Two‑pointer pattern on linked lists (detect cycle, merge lists).  
- Streaming data: maintain running two‑pointer window over sliding window sums.  
- Geographic water retention modeling (3D version of rain trapping).

### Debugging Tips & Mental Checklists  
1. Write down the **pointer invariants**: what does each pointer represent at each step?  
2. For sorted two‑pointers, confirm the **direction** of pointer movement reduces your search space correctly.  
3. When skipping duplicates, ensure you **increment past** all repeated values.  
4. For rainwater, always **update maxima first**, then compute trapped water.  
5. Test edge cases: empty input, all equal values, strictly increasing/decreasing sequences.

Keep practicing these patterns, apply them to new scenarios, and soon the two‑pointer technique will be second nature. Happy coding!  