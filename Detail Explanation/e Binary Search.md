<!--markdownlint-disable-->
# 📚 Binary Search (Advanced Beginner‑Friendly Master Guide)

This master guide preserves the original structure—problem titles, links, and code blocks—while teaching each concept from first principles. Each problem section includes:

1. **Theory**: Why and how binary search (or its variant) applies.  
2. **Approach Walkthrough**: A concrete, small example you can trace by hand.  
3. **Detailed Code Breakdown**: Every line of the C++ snippet explained in plain English.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  

After all problems, you'll find:

- **Expanded Helper Functions** with usage guidance.  
- **Deep Dive** into binary‐search paradigms.  
- **Next Steps to Mastery** with follow‑up problems, advanced variants, and debugging checklists.

---

## 1. Binary Search  
Link: https://leetcode.com/problems/binary-search/

### Theory: Classic Divide‑and‑Conquer on a Sorted Array  
Binary search repeatedly cuts the search interval in half, exploiting the fact that the array is sorted. We maintain a left (`low`) and right (`high`) index. Each step, we compare the middle element to the target and discard the half that cannot contain the target.

### Approach Walkthrough  
Example: `nums = [1,3,5,7,9,11]`, `target = 7`  
1. `low=0`, `high=5`, `mid = (0+5)/2 = 2`, `nums[2]=5` < 7 → discard left half including mid → `low=3`.  
2. Now `low=3`, `high=5`, `mid=(3+5)/2=4`, `nums[4]=9` > 7 → discard right half including mid → `high=3`.  
3. `low=3`, `high=3`, `mid=3`, `nums[3]=7` == target → return index **3**.

### Detailed Code Breakdown  
```cpp
int binarySearch(const vector<int>& nums, int target) {
    int low = 0, high = nums.size() - 1;        // 1) Initialize search bounds.
    while (low <= high) {                       // 2) Continue until bounds cross.
        int mid = low + (high - low) / 2;       // 3) Compute mid safely to avoid overflow.
        if (nums[mid] == target) {              // 4) Check for match.
            return mid;                         //    Found target.
        } else if (nums[mid] < target) {        // 5) If mid is too small…
            low = mid + 1;                      //    Search right half.
        } else {                                // 6) Else mid is too large…
            high = mid - 1;                     //    Search left half.
        }
    }
    return -1;                                  // 7) Not found.
}
```
- Step 1: Set initial bounds to the full array.  
- Step 3: `low + (high - low)/2` prevents integer overflow.  
- Steps 5–6: Discard the half that cannot contain the target.

#### Pitfalls & Tips  
- Off‑by‑one in loop condition (`low <= high` vs. `low < high`).  
- Forgetting to update `low` or `high` correctly can lead to infinite loops.  
- Always use the overflow‑safe `mid` formula.

---

## 2. Search a 2D Matrix  
Link: https://leetcode.com/problems/search-a-2d-matrix/

### Theory: Treat the 2D Grid as a 1D Sorted Array  
Since each row is sorted and every row’s first element exceeds the previous row’s last, you can imagine the entire matrix flattened into one sorted array of length `m*n`. Then a classic binary search on indices 0…`m*n-1` works—mapping each midpoint back to a `(row, col)`.

### Approach Walkthrough  
Matrix:
```
[ [1,  3,  5],
  [7,  9, 11],
  [13,15,17] ]
target = 9
```
1. `low=0`, `high=8`, `mid=4`, map `r=4/3=1`, `c=4%3=1`, `val=9` → match → return **true**.

### Detailed Code Breakdown  
```cpp
bool searchMatrix(const vector<vector<int>>& mat, int target) {
    int m = mat.size(), n = mat[0].size();       // 1) Dimensions.
    int low = 0, high = m * n - 1;               // 2) Flattened bounds.
    while (low <= high) {                        // 3) Binary search on range.
        int mid = low + (high - low) / 2;        // 4) Midpoint index.
        int r = mid / n;                         // 5) Map to row.
        int c = mid % n;                         // 6) Map to column.
        int val = mat[r][c];                     // 7) Value at mid.
        if (val == target)                       // 8) Found?
            return true;
        else if (val < target)                   // 9) Too small?
            low = mid + 1;                       //    Move right.
        else                                     // 10) Too large?
            high = mid - 1;                      //    Move left.
    }
    return false;                                // 11) Not found.
}
```

#### Pitfalls & Tips  
- Ensure the matrix is nonempty before accessing `mat[0]`.  
- Be careful with integer division when mapping indices.

---

## 3. Koko Eating Bananas  
Link: https://leetcode.com/problems/koko-eating-bananas/

### Theory: Binary Search on the Answer (Parametric Search)  
We search for the smallest eating speed `k` in the range `[1, maxPile]` such that Koko finishes within `H` hours. Because the feasibility function `canFinish(k)` is **monotonic**—if she can finish at speed `k`, she can certainly finish at any `k' > k`—we can binary‑search the answer space.

### Approach Walkthrough  
Piles = `[3,6,7,11]`, `H=8`:

- Speeds range 1…11.  
- Test `mid = 6`: hours = `ceil(3/6)+ceil(6/6)+ceil(7/6)+ceil(11/6)=1+1+2+2=6≤8` → feasible → try slower (high=5).  
- Continue until low > high → answer **4**.

### Detailed Code Breakdown  
```cpp
bool canFinish(const vector<int>& piles, int k, int H) {
    long hours = 0;                             // 1) Accumulated hours.
    for (int p : piles) {                       // 2) For each pile…
        hours += (p + k - 1) / k;               //    Ceil division: hours to finish pile.
        if (hours > H) return false;            //    Bail early if too slow.
    }
    return true;                                // 3) Finished in time.
}

int minEatingSpeed(vector<int>& piles, int H) {
    int low = 1, high = *max_element(piles.begin(), piles.end());
    int ans = high;                             // 4) Initialize answer to max possible.
    while (low <= high) {                       // 5) Binary search on speed.
        int mid = low + (high - low) / 2;
        if (canFinish(piles, mid, H)) {         // 6) If feasible at mid…
            ans = mid;                          //    Record and try lower speed.
            high = mid - 1;
        } else {                                // 7) Too slow at mid…
            low = mid + 1;                      //    Increase speed.
        }
    }
    return ans;                                 // 8) Smallest feasible speed.
}
```

#### Pitfalls & Tips  
- Use `long` for cumulative hours to avoid overflow.  
- Always bail early if `hours` exceeds `H` for efficiency.

---

## 4. Find Minimum in Rotated Sorted Array  
Link: https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/

### Theory: Pivot‑Based Binary Search  
When the sorted array is rotated, the minimum lies at the pivot point. By comparing `nums[mid]` to `nums[high]`, we decide which half contains the pivot:

- If `nums[mid] > nums[high]`, pivot is in the right half.  
- Otherwise, pivot (minimum) is in the left half (including `mid`).

### Approach Walkthrough  
`nums = [4,5,6,7,0,1,2]`:

1. `low=0`, `high=6`, `mid=3`, `nums[3]=7` > `nums[6]=2` → pivot in right → `low=4`.  
2. `low=4`, `high=6`, `mid=5`, `nums[5]=1` ≤ `2` → pivot in left → `high=5`.  
3. Continue until `low` meets `high` at index 4 → **0**.

### Detailed Code Breakdown  
```cpp
int findMin(vector<int>& nums) {
    int low = 0, high = nums.size() - 1;      // 1) Bounds.
    while (low < high) {                      // 2) While more than one element.
        int mid = low + (high - low) / 2;     // 3) Midpoint.
        if (nums[mid] > nums[high])           // 4) Pivot in right half?
            low = mid + 1;                    //    Discard left half.
        else                                   // 5) Pivot in left half including mid.
            high = mid;
    }
    return nums[low];                         // 6) low == high → min index.
}
```

#### Pitfalls & Tips  
- Use `< high` in loop to ensure termination when one element remains.  
- Don’t return `mid` inside loop; wait until `low == high`.

---

## 5. Search in Rotated Sorted Array  
Link: https://leetcode.com/problems/search-in-rotated-sorted-array/

### Theory: Identify a Sorted Half Each Step  
At each iteration, one half (left or right of `mid`) is guaranteed to be sorted. Check if the target lies in that sorted half; if so, discard the other half. Otherwise, search the unsorted half.

### Approach Walkthrough  
`nums = [6,7,0,1,2,4,5]`, `target=2`:

1. `low=0,high=6,mid=3,nums[3]=1`. Left half `[6,7,0]` is not fully sorted (`6>0`), so right half `[1,2,4,5]` is sorted. Target 2 ∈ `[1,5]` → search right: `low=4`.  
2. Continue until found at index 4.

### Detailed Code Breakdown  
```cpp
int searchRotated(const vector<int>& nums, int target) {
    int low = 0, high = nums.size() - 1;
    while (low <= high) {                     // 1) Standard binary loop.
        int mid = low + (high - low) / 2;     // 2) Midpoint.
        if (nums[mid] == target)              // 3) Found?
            return mid;
        // 4) Determine which half is sorted.
        if (nums[low] <= nums[mid]) {         
            // 5) Left half is sorted.
            if (nums[low] <= target && target < nums[mid])
                high = mid - 1;               //    Target in left.
            else
                low = mid + 1;                //    Target in right.
        } else {
            // 6) Right half is sorted.
            if (nums[mid] < target && target <= nums[high])
                low = mid + 1;                //    Target in right.
            else
                high = mid - 1;               //    Target in left.
        }
    }
    return -1;                                // 7) Not found.
}
```

#### Pitfalls & Tips  
- Be precise with `<=` and `<` when checking sorted halves.  
- If `nums[low] == nums[mid]`, left half may be all duplicates—this version assumes no duplicates in input.

---

## 6. Time Based Key‑Value Store  
Link: https://leetcode.com/problems/time-based-key-value-store/

### Theory: Timestamped Entries + Binary Search  
We keep each key’s history as a sorted list of `(timestamp, value)` pairs. Since timestamps strictly increase, we can binary search that vector to find the entry with the largest timestamp ≤ query time.

### Approach Walkthrough  
Operations:
```
set("foo","bar",1)
get("foo",1) → "bar"
get("foo",3) → "bar"
set("foo","baz",4)
get("foo",4) → "baz"
get("foo",5) → "baz"
```
For `get("foo",3)`, we search pairs `[(1,"bar"),(4,"baz")]` for `t≤3`: `upper_bound` returns iterator to `(4,"baz")`, step back to `(1,"bar")`.

### Detailed Code Breakdown  
```cpp
class TimeMap {
    unordered_map<string, vector<pair<int,string>>> mp; 
public:
    TimeMap() {}                               // 1) Initialize map.

    void set(const string& key, const string& value, int t) {
        mp[key].emplace_back(t, value);        // 2) Append new timestamped value.
    }

    string get(const string& key, int t) {
        auto &vec = mp[key];                   // 3) Retrieve list for key.
        // 4) Find first pair with timestamp > t.
        auto it = upper_bound(
            vec.begin(), vec.end(), 
            make_pair(t, string("\x7F")),
            [](auto &a, auto &b){ return a.first < b.first; }
        );
        if (it == vec.begin())                 // 5) No valid timestamp.
            return "";
        return prev(it)->second;               // 6) Step back to valid entry.
    }
};
```

#### Pitfalls & Tips  
- Use a sentinel high string `"\x7F"` so that `upper_bound` compares only timestamps.  
- If the key does not exist, `mp[key]` will create an empty vector—`upper_bound` on empty returns `begin()`.

---

## 7. Median of Two Sorted Arrays  
Link: https://leetcode.com/problems/median-of-two-sorted-arrays/

### Theory: Binary Search Partitioning  
We binary‑search the smaller array for a partition index `i`. The goal is to split both arrays into left and right halves of equal total length. Ensuring `max(left) ≤ min(right)` on both sides gives the correct cut to compute the median.

### Approach Walkthrough  
A = `[1,3]`, B = `[2]`:

- `m=2,n=1` → swap so A is smaller: A=`[2]`, B=`[1,3]`.  
- `low=0,high=1,half=2`.  
- `i=0,j=2-0=2` → Aleft=-∞, Aright=2, Bleft=3, Bright=+∞ → Bleft>Aright → move low=1.  
- `i=1,j=1` → Aleft=2,Aright=+∞,Bleft=1,Bright=3 → Aleft>Bright? No; Bleft>Aright? No → correct partition → median = max(2,1)=2 (odd).

### Detailed Code Breakdown  
```cpp
double findMedianSortedArrays(const vector<int>& A, const vector<int>& B) {
    int m = A.size(), n = B.size();
    if (m > n)                               // 1) Ensure A is the smaller array.
        return findMedianSortedArrays(B, A);
    int low = 0, high = m, half = (m + n + 1) / 2;
    while (low <= high) {                    // 2) Binary loop on A's partition.
        int i = low + (high - low) / 2;      // 3) Partition in A.
        int j = half - i;                    // 4) Complement partition in B.

        int Aleft  = (i == 0 ? INT_MIN : A[i - 1]);
        int Aright = (i == m ? INT_MAX : A[i]);
        int Bleft  = (j == 0 ? INT_MIN : B[j - 1]);
        int Bright = (j == n ? INT_MAX : B[j]);

        // 5) Check if correct partition.
        if (Aleft <= Bright && Bleft <= Aright) {
            // 6a) Odd total length.
            if ((m + n) % 2)
                return max(Aleft, Bleft);
            // 6b) Even total length.
            return (max(Aleft, Bleft) + min(Aright, Bright)) / 2.0;
        }
        // 7) Adjust partitions.
        if (Aleft > Bright)
            high = i - 1;                    //    Move left in A.
        else
            low = i + 1;                     //    Move right in A.
    }
    return 0.0;                             // 8) Fallback (should not reach).
}
```

#### Pitfalls & Tips  
- Always swap arrays if `m > n` to keep `O(log min(m,n))`.  
- Use `INT_MIN`/`INT_MAX` as sentinels for empty partitions.

---

## 🔧 Expanded Helper Functions  

```cpp
// lower_bound: first index where arr[idx] ≥ target
template<typename T>
int lowerBound(const vector<T>& arr, T target) {
    int low = 0, high = arr.size();
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] < target) low = mid + 1;
        else high = mid;
    }
    return low;
}

// upper_bound: first index where arr[idx] > target
template<typename T>
int upperBound(const vector<T>& arr, T target) {
    int low = 0, high = arr.size();
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] <= target) low = mid + 1;
        else high = mid;
    }
    return low;
}
```
- **Why it works**: Repeatedly halves the search interval based on comparisons.  
- **When to use**: custom binary searches on any sorted container.  
- **Adaptation**: change comparison operators to find other bounds (e.g., first greater or equal).

---

## 🔍 Deep Dive into Paradigms

### 1. Classic Binary Search  
- **Invariant**: target ∈ [low, high].  
- **Key**: each iteration halves the search space.  

### 2. Flattened 2D Search  
- **Idea**: map 1D index to 2D coordinates with division and modulo.  

### 3. Parametric Search (Answer Space)  
- **Idea**: binary search on a monotonic predicate over possible answers.  

### 4. Pivot Detection in Rotated Arrays  
- **Idea**: compare `mid` to boundary to locate the pivot.  

### 5. Partition‑Based Search (Median)  
- **Invariant**: left partitions contain half the elements; `max(left) ≤ min(right)`.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Find Peak Element** (binary search on slope).  
- **Search in Mountain Array** (double binary search).  
- **Kth Smallest in Sorted Matrix** (parametric or heap + binary search).  
- **Split Array Largest Sum** (binary search max subarray sum).  
- **Find K Closest Elements** (binary search window start).

### Advanced Variants & Real‑World Applications  
- **Search in Infinite Sorted Array** (exponential + binary search).  
- **CPU Scheduling**: binary search on maximum load.  
- **Data Versioning**: time‑travel queries via binary search on commit history.

### Debugging Tips & Mental Checklists  
1. Write down the **invariant** before coding.  
2. Choose loop condition: `low <= high` vs. `low < high` based on return semantics.  
3. Use overflow‑safe mid calculation.  
4. Test edge cases: empty array, single element, all duplicates, extremes of answer space.  
5. Trace on small examples to verify boundary updates.

Happy searching—mastering these patterns will power your next algorithm challenge!  