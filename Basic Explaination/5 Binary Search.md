<!--markdownlint-disable-->
# 📚 Binary Search

This guide covers seven classic LeetCode problems under the **Binary Search** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of the algorithmic paradigms  
6. Advanced tips & practice

---

## 1. Binary Search  
Link: https://leetcode.com/problems/binary-search/

### Description  
Given a sorted array `nums` and a target value, return the index of the target if found; otherwise, return -1.

### Core Idea: Classic Binary Search  
1. Maintain two pointers: `low = 0`, `high = n - 1`.  
2. While `low <= high`:  
   - Compute `mid = low + (high - low) / 2` to avoid overflow.  
   - If `nums[mid] == target`, return `mid`.  
   - If `nums[mid] < target`, search right half (`low = mid + 1`).  
   - Else, search left half (`high = mid - 1`).  
3. If loop ends, target is not present.

### C++ Code Snippet: Classic Binary Search  
```cpp
// Performs standard binary search on a sorted vector.
// Returns the index of target or -1 if not found.
int binarySearch(const vector<int>& nums, int target) {
    int low = 0, high = nums.size() - 1;
    while (low <= high) {
        // Prevent (low+high) overflow
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            return mid;              // target found
        } else if (nums[mid] < target) {
            low = mid + 1;           // search right half
        } else {
            high = mid - 1;          // search left half
        }
    }
    return -1;                       // target not in array
}
```

---

## 2. Search a 2D Matrix  
Link: https://leetcode.com/problems/search-a-2d-matrix/

### Description  
Given an `m x n` matrix where each row is sorted and the first integer of each row is greater than the last integer of the previous row, search for a target value.

### Core Idea: Treat 2D as 1D + Binary Search  
1. View the matrix as a flat array of length `m*n`.  
2. Map `index` → row `r = idx / n` and col `c = idx % n`.  
3. Apply classic binary search on indices `0` to `m*n - 1`.  
4. Translate each `mid` to `(r, c)` and compare.

### C++ Code Snippet: Flattened Binary Search  
```cpp
// Searches target in a row-wise sorted matrix.
bool searchMatrix(const vector<vector<int>>& mat, int target) {
    int m = mat.size(), n = mat[0].size();
    int low = 0, high = m * n - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        int r = mid / n;            // row index
        int c = mid % n;            // column index
        int val = mat[r][c];        
        if (val == target) {
            return true;            // found
        } else if (val < target) {
            low = mid + 1;          // search right
        } else {
            high = mid - 1;         // search left
        }
    }
    return false;                   // not found
}
```

---

## 3. Koko Eating Bananas  
Link: https://leetcode.com/problems/koko-eating-bananas/

### Description  
Koko has piles of bananas and wants to eat all within `H` hours. She eats at speed `k` bananas/hour. Find the minimum integer `k` so she can finish in time.

### Core Idea: Binary Search on Answer Space  
1. Define `canFinish(k)`: sum of `ceil(pile / k)` ≤ `H`.  
2. The valid region of `k` is monotonic: if she can finish at speed `k`, she can finish at any `k' > k`.  
3. Binary search `k` from `1` to `max(piles)`.  
4. Return the smallest `k` that returns true.

### C++ Code Snippet: Parametric Search  
```cpp
// Checks if Koko can eat all piles at speed k within H hours.
bool canFinish(const vector<int>& piles, int k, int H) {
    long hours = 0;
    for (int p : piles) {
        // hours needed for this pile
        hours += (p + k - 1) / k;  // ceil(p / k)
        if (hours > H) return false; 
    }
    return true;
}

// Finds minimum k using binary search on speed.
int minEatingSpeed(vector<int>& piles, int H) {
    int low = 1, high = *max_element(piles.begin(), piles.end());
    int ans = high;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (canFinish(piles, mid, H)) {
            ans = mid;              // mid works, try slower
            high = mid - 1;
        } else {
            low = mid + 1;          // mid too slow, speed up
        }
    }
    return ans;
}
```

---

## 4. Find Minimum in Rotated Sorted Array  
Link: https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/

### Description  
A sorted array is rotated at an unknown pivot. Find the minimum element in O(log n) time.

### Core Idea: Modified Binary Search with Pivot Detection  
1. If `nums[mid] > nums[high]`, the min lies in right half (`low = mid + 1`).  
2. Else, the min is in left half including `mid` (`high = mid`).  
3. Loop until `low == high`.

### C++ Code Snippet: Pivot‑based Search  
```cpp
// Finds minimum value in a rotated sorted array.
int findMin(vector<int>& nums) {
    int low = 0, high = nums.size() - 1;
    while (low < high) {
        int mid = low + (high - low) / 2;
        // pivot in right half
        if (nums[mid] > nums[high]) {
            low = mid + 1;
        } else {
            high = mid;           // min could be at mid
        }
    }
    return nums[low];            // low == high
}
```

---

## 5. Search in Rotated Sorted Array  
Link: https://leetcode.com/problems/search-in-rotated-sorted-array/

### Description  
Search for a target in a rotated sorted array in O(log n). Return its index or -1 if not found.

### Core Idea: Determine Sorted Half Each Step  
1. Compute `mid`.  
2. If `nums[mid] == target`, return `mid`.  
3. Check which half is sorted:
   - If `nums[low] <= nums[mid]`, left half is sorted.
     - If `target` in `[nums[low], nums[mid]]`, search left; else right.
   - Else, right half is sorted.
     - If `target` in `[nums[mid], nums[high]]`, search right; else left.
4. Adjust `low`/`high` accordingly.

### C++ Code Snippet: Rotated Search Logic  
```cpp
// Searches target in rotated sorted array.
int searchRotated(const vector<int>& nums, int target) {
    int low = 0, high = nums.size() - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) return mid;
        // left half is sorted
        if (nums[low] <= nums[mid]) {
            if (nums[low] <= target && target < nums[mid]) {
                high = mid - 1;  // target in left
            } else {
                low = mid + 1;   // target in right
            }
        } else {
            // right half is sorted
            if (nums[mid] < target && target <= nums[high]) {
                low = mid + 1;   // target in right
            } else {
                high = mid - 1;  // target in left
            }
        }
    }
    return -1;               // not found
}
```

---

## 6. Time Based Key-Value Store  
Link: https://leetcode.com/problems/time-based-key-value-store/

### Description  
Design a data structure that stores key-value pairs with timestamps, and retrieves the value for a given key at the largest timestamp ≤ the query timestamp.

### Core Idea: Map + Binary Search on Timestamp  
1. Use `unordered_map<string, vector<pair<int,string>>>` to store `(timestamp, value)` lists per key.  
2. `set(key, value, t)` → append `(t, value)` to the vector (timestamps strictly increasing).  
3. `get(key, t)` → binary search that vector for the largest `timestamp ≤ t`: use `upper_bound` then step back one.

### C++ Code Snippet: Timestamped KV Store  
```cpp
class TimeMap {
    unordered_map<string, vector<pair<int,string>>> mp;
public:
    TimeMap() {}
    // store the pair with increasing timestamp
    void set(const string& key, const string& value, int t) {
        mp[key].emplace_back(t, value);
    }
    // retrieve value with largest timestamp ≤ t
    string get(const string& key, int t) {
        auto &vec = mp[key];
        // comparator for upper_bound on pair<int,string>
        auto it = upper_bound(vec.begin(), vec.end(), 
            make_pair(t, string("\x7F")), 
            [](auto &a, auto &b){ return a.first < b.first; });
        if (it == vec.begin()) return "";  // no valid timestamp
        return prev(it)->second;           // step back
    }
};
```

---

## 7. Median of Two Sorted Arrays  
Link: https://leetcode.com/problems/median-of-two-sorted-arrays/

### Description  
Given two sorted arrays of sizes `m` and `n`, return the median of the two arrays in O(log(min(m,n))) time.

### Core Idea: Binary Search Partition  
1. Ensure `A` is the smaller array.  
2. Binary search partition index `i` in `A` (0…m). Let `j = (m + n + 1)/2 - i` in `B`.  
3. Let `Aleft = (i==0 ? -INF : A[i-1])`, `Aright = (i==m ? INF : A[i])`, similarly for `Bleft`, `Bright`.  
4. If `Aleft <= Bright` and `Bleft <= Aright`, found correct partition:
   - If `(m+n)` odd → max(`Aleft`, `Bleft`).
   - Else → average of max(`Aleft`,`Bleft`) and min(`Aright`,`Bright`).
5. Else if `Aleft > Bright` → move `high = i - 1`; else `low = i + 1`.

### C++ Code Snippet: Partition‑Based Median  
```cpp
// Finds median of two sorted arrays in O(log min(m,n)).
double findMedianSortedArrays(const vector<int>& A, const vector<int>& B) {
    int m = A.size(), n = B.size();
    // ensure A is smaller
    if (m > n) return findMedianSortedArrays(B, A);
    int low = 0, high = m, half = (m + n + 1) / 2;
    while (low <= high) {
        int i = low + (high - low) / 2;
        int j = half - i;
        int Aleft  = (i == 0 ? INT_MIN : A[i - 1]);
        int Aright = (i == m ? INT_MAX : A[i]);
        int Bleft  = (j == 0 ? INT_MIN : B[j - 1]);
        int Bright = (j == n ? INT_MAX : B[j]);
        // correct partition
        if (Aleft <= Bright && Bleft <= Aright) {
            if ((m + n) % 2)
                return max(Aleft, Bleft);  // odd total length
            return (max(Aleft, Bleft) + min(Aright, Bright)) / 2.0;
        }
        // shift partition
        if (Aleft > Bright)
            high = i - 1;
        else
            low = i + 1;
    }
    return 0.0;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Standard lower_bound wrapper
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

// Standard upper_bound wrapper
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

---

## 🔍 Algorithmic Paradigms  

- **Classic Binary Search**  
  - Invariant: target resides in `[low, high]`.  
  - Each step halves the search space → O(log n).  

- **Flattened 2D Search**  
  - Map 2D to 1D indices; same invariant holds.  

- **Parametric Search (Answer Space)**  
  - Define a boolean function `f(k)` that is monotonic.  
  - Binary search on `k` until the boundary.  

- **Pivot‑Detection in Rotated Arrays**  
  - Use comparisons between `mid` and `high` (or `low`) to detect which side is ordered.  

- **Partition‑Based Search**  
  - Partition smaller array such that left partitions contain half of the elements.  
  - Invariants on max‐left ≤ min‐right ensure correctness.

### Complexity Summary  

| Problem                                       | Time                  | Space              |
|-----------------------------------------------|-----------------------|--------------------|
| Binary Search                                | O(log n)              | O(1)               |
| Search a 2D Matrix                           | O(log (mn))           | O(1)               |
| Koko Eating Bananas                          | O(n log M)            | O(1)               |
| Find Min in Rotated Sorted Array             | O(log n)              | O(1)               |
| Search in Rotated Sorted Array               | O(log n)              | O(1)               |
| Time Based Key‑Value Store                   | O(log k) per get      | O(N) for storage  |
| Median of Two Sorted Arrays                  | O(log min(m,n))       | O(1)               |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Master classic binary search boundaries and overflow-safe `mid`.  
- **Medium**: Learn to adapt binary search to answer‐space (Koko), 2D, rotated arrays, and keyed lookups.  
- **Hard**: Understand partition logic for medians and other “find k-th” problems.

### Follow‑Up Problems  
- Find Peak Element  
- Search in Mountain Array  
- Kth Smallest Element in a Sorted Matrix  
- Split Array Largest Sum (binary search over maximum subarray sum)  
- Find K Closest Elements  

### Common Pitfalls & Debugging  
- Off‑by‑one in `low <= high` vs. `low < high` loops  
- Integer overflow in `(low + high) / 2` (use `low + (high - low)/2`)  
- Forgetting to handle empty arrays or single‐element edge cases  
- Misidentifying which half is sorted in rotated searches  
- Mixing up `lower_bound` vs. `upper_bound` semantics  

Happy searching and mastering binary search patterns!  