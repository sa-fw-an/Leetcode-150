<!--markdownlint-disable-->
# 📚 Arrays & Hashing

This guide covers nine classic LeetCode problems under the **Arrays & Hashing** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice

---

## 1. Contains Duplicate  
Link: https://leetcode.com/problems/contains-duplicate/  

### Description  
Given an integer array, return **true** if any value appears at least twice; otherwise, return **false**.

### Core Idea: Hash Set  
1. Iterate through the array.  
2. Keep a hash set of seen values.  
3. If you encounter a value already in the set, you have a duplicate → return true.  
4. Otherwise add it to the set.  
5. If no duplicates found, return false.

### C++ Code Snippet: Hash Set Duplicate Check  
```cpp
// Checks if nums contains any duplicate value.
bool containsDuplicate(const vector<int>& nums) {
    unordered_set<int> seen;               // stores all unique values encountered
    for (int x : nums) {
        // if insertion fails, x was already in set ⇒ duplicate
        if (!seen.insert(x).second) return true;
    }
    return false;                          // no duplicates found
}
```

---

## 2. Valid Anagram  
Link: https://leetcode.com/problems/valid-anagram/  

### Description  
Given two strings, determine if one is a permutation (anagram) of the other.

### Core Idea: Frequency Counting  
1. If lengths differ → not an anagram.  
2. Count frequency of each character in `s`.  
3. Decrease count using `t`.  
4. If any count goes negative → not an anagram.

### C++ Code Snippet: Character Frequency Map  
```cpp
// Returns true if t is an anagram of s.
bool isAnagram(const string& s, const string& t) {
    if (s.size() != t.size()) return false;
    int count[26] = {0};                   // counts for 'a' to 'z'
    for (char c : s) ++count[c - 'a'];     // increment for s
    for (char c : t) {
        if (--count[c - 'a'] < 0)          // decrement; negative ⇒ extra char
            return false;
    }
    return true;
}
```

---

## 3. Longest Consecutive Sequence  
Link: https://leetcode.com/problems/longest-consecutive-sequence/  

### Description  
Given an unsorted array of integers, find the length of the longest sequence of consecutive numbers.

### Core Idea: Hash Set + One‐Pass Sequence Build  
1. Insert all numbers into a hash set.  
2. For each number, only start counting if `num-1` is **not** in set (beginning of a sequence).  
3. Incrementally check `num+1`, `num+2`, … in set to measure sequence length.  
4. Track maximum length.

### C++ Code Snippet: Sequence Detection with Hash Set  
```cpp
// Finds length of the longest run of consecutive ints.
int longestConsecutive(const vector<int>& nums) {
    unordered_set<int> st(nums.begin(), nums.end());
    int best = 0;
    for (int num : nums) {
        // only attempt if num is start of a sequence
        if (!st.count(num - 1)) {
            int length = 0;
            // count forward until gap
            while (st.count(num + length)) {
                ++length;
            }
            best = max(best, length);
        }
    }
    return best;
}
```

---

## 4. Two Sum  
Link: https://leetcode.com/problems/two-sum/  

### Description  
Given an array and a target, return indices of the two numbers that add up to the target.

### Core Idea: One‐Pass Hash Map  
1. Traverse array once.  
2. For each element `x`, check if `target - x` exists in a hash map.  
3. If found, return current index and stored index.  
4. Otherwise, store `x → index` in the map.

### C++ Code Snippet: One‑Pass Two Sum  
```cpp
// Returns pair of indices that sum to target (assumes one solution).
vector<int> twoSum(const vector<int>& nums, int target) {
    unordered_map<int,int> mp;             // value → index
    for (int i = 0; i < nums.size(); ++i) {
        int need = target - nums[i];
        if (mp.count(need)) {
            return {mp[need], i};          // found complement
        }
        mp[nums[i]] = i;                   // store current value
    }
    return {};                             // no solution (by problem, never occurs)
}
```

---

## 5. Group Anagrams  
Link: https://leetcode.com/problems/group-anagrams/  

### Description  
Group a list of strings into anagrams (same letters, different order).

### Core Idea: Hash Map with Sorted Key  
1. For each string, sort its characters → “key”.  
2. Use `unordered_map<string, vector<string>>` to group originals by key.  
3. Collect map values.

### C++ Code Snippet: Grouping by Sorted Signature  
```cpp
// Groups words that are anagrams together.
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> mp;
    for (auto& s : strs) {
        string key = s;                   // copy
        sort(key.begin(), key.end());     // canonical form
        mp[key].push_back(s);             // group by signature
    }
    vector<vector<string>> res;
    for (auto& [_, group] : mp)
        res.push_back(move(group));
    return res;
}
```

---

## 6. Top K Frequent Elements  
Link: https://leetcode.com/problems/top-k-frequent-elements/  

### Description  
Return the k most frequent elements from an array.

### Core Idea: Hash Map + Min‐Heap  
1. Count frequencies in `unordered_map<int,int>`.  
2. Maintain a min‐heap of size `k` (pairs of `<freq, value>`).  
3. Push each `<freq, val>` into heap; if size > k, pop smallest.  
4. Extract heap values.

### C++ Code Snippet: Min‑Heap of Size K  
```cpp
// Returns k most frequent numbers.
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int,int> freq;
    for (int x : nums) ++freq[x];
    // min-heap compares by frequency
    priority_queue<pair<int,int>,
        vector<pair<int,int>>, greater<>> minHeap;
    for (auto& [val, f] : freq) {
        minHeap.emplace(f, val);
        if (minHeap.size() > k) minHeap.pop();
    }
    vector<int> res;
    while (!minHeap.empty()) {
        res.push_back(minHeap.top().second);
        minHeap.pop();
    }
    reverse(res.begin(), res.end());       // highest freq first
    return res;
}
```

---

## 7. Encode and Decode Strings  
Link: https://leetcode.com/problems/encode-and-decode-strings/  

### Description  
Design methods to encode a list of strings to a single string and decode it back.

### Core Idea: Length‑Prefix Encoding  
- **Encode** each string as: `<length>#<content>`.  
- **Decode** by reading an integer length up to `#`, then read that many characters.

### C++ Code Snippet: Length‑Prefixed Codec  
```cpp
// Encodes and decodes a vector of strings.
class Codec {
public:
    // encode list → single string
    string encode(const vector<string>& strs) {
        string out;
        for (auto& s : strs) {
            out += to_string(s.size()) + '#' + s;
        }
        return out;
    }
    // decode back to list
    vector<string> decode(const string& s) {
        vector<string> res;
        int i = 0, n = s.size();
        while (i < n) {
            // read length
            int j = i;
            while (s[j] != '#') ++j;
            int len = stoi(s.substr(i, j - i));
            // read content
            res.push_back(s.substr(j + 1, len));
            i = j + 1 + len;             // move past this segment
        }
        return res;
    }
};
```

---

## 8. Product of Array Except Self  
Link: https://leetcode.com/problems/product-of-array-except-self/  

### Description  
Given `nums`, produce `output` where `output[i]` = product of all elements except `nums[i]`, without division.

### Core Idea: Prefix & Suffix Products  
1. Compute `L[i]` = product of all to left of `i`.  
2. Compute `R[i]` = product of all to right of `i`.  
3. `output[i] = L[i] * R[i]`.  
4. Achieve O(1) extra space by reusing output array.

### C++ Code Snippet: Two‐Pass Prefix/Suffix  
```cpp
// Returns product of array except self.
vector<int> productExceptSelf(const vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, 1);
    // left products
    for (int i = 1; i < n; ++i)
        res[i] = res[i - 1] * nums[i - 1];
    // right products on the fly
    int right = 1;
    for (int i = n - 1; i >= 0; --i) {
        res[i] *= right;                  // multiply by product to right
        right *= nums[i];
    }
    return res;
}
```

---

## 9. Valid Sudoku  
Link: https://leetcode.com/problems/valid-sudoku/  

### Description  
Check if a 9×9 board is valid: no duplicate digits (1–9) in any row, column, or 3×3 sub‐box.

### Core Idea: Three Hash Sets  
1. For each cell `(r,c)` with digit `d`:  
   - Check row `r` set  
   - Check column `c` set  
   - Check box `(r/3, c/3)` set  
2. If `d` already exists in any → invalid.  
3. Otherwise add to all three sets.

### C++ Code Snippet: Row/Col/Box Validation  
```cpp
// Checks if board is a valid Sudoku.
bool isValidSudoku(vector<vector<char>>& board) {
    vector<unordered_set<char>> rows(9), cols(9), boxes(9);
    for (int r = 0; r < 9; ++r) {
        for (int c = 0; c < 9; ++c) {
            char d = board[r][c];
            if (d == '.') continue;         // skip empty
            int b = (r / 3) * 3 + (c / 3);  // box index 0..8
            // if seen before in row/col/box → invalid
            if (!rows[r].insert(d).second ||
                !cols[c].insert(d).second ||
                !boxes[b].insert(d).second)
                return false;
        }
    }
    return true;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Print a 1D vector of ints
void printVec(const vector<int>& v) {
    for (int x : v) cout << x << ' ';
    cout << "\n";
}

// Print a 2D board
void printBoard(const vector<vector<char>>& b) {
    for (auto& row : b) {
        for (char c : row) cout << c << ' ';
        cout << "\n";
    }
}
```

---

## 🔍 Algorithmic Paradigms  
- **Hashing**: O(1) average inserts/lookups  
- **Sorting** (Group Anagrams key)  
- **Heap (priority_queue)** for Top K  
- **Two‐pass prefix/suffix** for products  
- **Delimiter parsing** for string codecs  

### Complexity Summary  
| Problem                      | Time            | Space             |
|------------------------------|-----------------|-------------------|
| Contains Duplicate           | O(n)            | O(n)              |
| Valid Anagram                | O(n)            | O(1) (26‐char)    |
| Longest Consecutive Sequence | O(n)            | O(n)              |
| Two Sum                      | O(n)            | O(n)              |
| Group Anagrams               | O(n·k log k)    | O(n·k)            |
| Top K Frequent Elements      | O(n log k)      | O(n + k)          |
| Encode/Decode Strings        | O(total chars)  | O(1)              |
| Product Except Self          | O(n)            | O(1) extra        |
| Valid Sudoku                 | O(9×9) = O(1)   | O(1)              |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Focus on correct use of hash sets/maps; watch off‐by‐one.  
- **Medium**: Optimize from brute‐force to one‐pass (e.g., Two Sum → one‐pass hashmap).  
- **Hard**: Combine multiple structures (Top K with heap; prefix/suffix in place).

### Follow‑Up Problems  
- **Duplicates & Hashing**: Intersection of Two Arrays, Happy Number  
- **Anagrams & Grouping**: Palindrome Permutation, Minimum Window Substring  
- **Heaps & Frequencies**: Kth Largest Element in an Array, Sliding Window Maximum  
- **Prefix/Suffix Tricks**: Subarray Product Except Self Variants  
- **Validation Grids**: Word Search, Battleships in a Board  

### Common Pitfalls & Debugging  
- Forgetting to reset counts/sets between test cases  
- Off‐by‐one in prefix/suffix loops  
- Miscomputing box index in Sudoku: `(r/3)*3 + (c/3)`  
- Heap orientation: min‐heap vs. max‐heap logic  
- String parsing boundaries when decoding  

---

Keep this guide handy as you practice Arrays & Hashing problems. Happy coding!  