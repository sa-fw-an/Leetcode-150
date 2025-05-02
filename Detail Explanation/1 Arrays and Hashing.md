<!--markdownlint-disable-->
# 📚 Arrays & Hashing (Advanced Beginner‑Friendly Master Guide)

Welcome to your step‑by‑step master guide for **Arrays & Hashing**. We will preserve the original structure—problem titles, links, and code blocks—while teaching you everything from first principles. Each section includes:

1. **Theory**: The data structures and algorithmic idea in plain English.  
2. **Approach Walkthrough**: A small example illustrating each step.  
3. **Detailed Code Breakdown**: Line‑by‑line commentary on the C++ snippet.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  

After all problems, you’ll find:

- **Expanded Helper Functions**  
- **Deep Dive** into each paradigm (Hashing, Sorting, Heaps, Prefix/Suffix, Delimiter Parsing)  
- **Next Steps to Mastery** with follow‑up problems, advanced variants, and debugging checklists.

Let’s get started!

---

## 1. Contains Duplicate  
Link: https://leetcode.com/problems/contains-duplicate/

### Theory: Detecting Repeats with a Hash Set  
A **hash set** is a collection that stores **unique** items and lets you check membership in **average constant time** (O(1)). As you scan an array, you insert each number into the set. If insertion fails (because it’s already there), you’ve found a duplicate.

### Approach Walkthrough  
Consider `nums = [1, 3, 2, 3]`:
1. Start with an empty set `seen = {}`.
2. Read **1**: not in `seen`, insert → `seen = {1}`.
3. Read **3**: not in `seen`, insert → `seen = {1,3}`.
4. Read **2**: not in `seen`, insert → `seen = {1,2,3}`.
5. Read **3**: **3 is already in** `seen` → duplicate found → return **true**.

### Detailed Code Breakdown  
```cpp
bool containsDuplicate(const vector<int>& nums) {
    unordered_set<int> seen;               // 1) Create an empty hash set.
    for (int x : nums) {                   // 2) Loop over each element in the input.
        // 3) Try to insert x into 'seen'. insert() returns a pair,
        //    .second is true if insertion succeeded (x was new),
        //    false if x already existed.
        if (!seen.insert(x).second)        
            return true;                   // 4) Duplicate found: exit immediately.
    }
    return false;                          // 5) No duplicates after full scan.
}
```
- Line 1: `bool containsDuplicate(...)` declares a function returning `true`/`false`.  
- Line 2: `unordered_set<int> seen;` chooses a hash set keyed by `int`.  
- Line 3: The **range‐based for** loops through `nums` as `x`.  
- Line 4: `seen.insert(x).second` tries to add `x`. If `.second == false`, `x` was already present.  
- Line 5: Returning `true` early saves time—no need to scan the rest.  

#### Pitfalls & Tips  
- Forgetting to include `<unordered_set>`.  
- Using `set<int>` instead of `unordered_set<int>` gives O(log n) ops instead of O(1).  
- Worst‐case time can degrade if the hash function clusters badly, but C++’s default is adequate for interview problems.

---

## 2. Valid Anagram  
Link: https://leetcode.com/problems/valid-anagram/

### Theory: Character Frequency Counting  
If two strings are anagrams, they contain the same letters in possibly different orders. We can tally how many times each character appears in `s`, then subtract counts when scanning `t`. If any count goes negative or we finish with nonzero counts, they’re not an anagram.

### Approach Walkthrough  
Example: `s = "abbc"`, `t = "cbab"`  
1. Lengths match (both 4)? Yes.  
2. Initialize `count[26] = {0}` for letters `'a'`–`'z'`.  
3. For each `c` in `"abbc"`, do `count[c - 'a']++`:  
   - `'a'` → `count[0] = 1`  
   - `'b'` → `count[1] = 1`, then `2`  
   - `'c'` → `count[2] = 1`  
   Final `count = {1,2,1,0,0,...}`  
4. For each `c` in `"cbab"`, do `--count[c - 'a']`:  
   - `'c'` → `count[2] = 0`  
   - `'b'` → `count[1] = 1`  
   - `'a'` → `count[0] = 0`  
   - `'b'` → `count[1] = 0`  
   No count went negative → they match → return **true**.

### Detailed Code Breakdown  
```cpp
bool isAnagram(const string& s, const string& t) {
    if (s.size() != t.size())             // 1) Quick check: different lengths → not anagrams.
        return false;
    int count[26] = {0};                   // 2) Array of 26 zeros for 'a'–'z'.
    for (char c : s)                       // 3) Build frequency from first string.
        ++count[c - 'a'];
    for (char c : t) {                     // 4) Subtract frequencies using second string.
        if (--count[c - 'a'] < 0)          // 5) If any goes negative, t has extra char → false.
            return false;
    }
    return true;                           // 6) All counts zero → valid anagram.
}
```
- Lines 1–2: Length check guards against mismatched input.  
- Line 3: `count[c - 'a']` maps `'a'`→0, `'b'`→1, … `'z'`→25.  
- Line 4–5: Decrement then test `< 0` to catch extra characters quickly.  

#### Pitfalls & Tips  
- Only works for lowercase English letters. For full ASCII, use `unordered_map<char,int>`.  
- Reset the count array if you reuse this in multiple test cases in one run.

---

## 3. Longest Consecutive Sequence  
Link: https://leetcode.com/problems/longest-consecutive-sequence/

### Theory: Sequence Building from Hash Set  
We want the longest run of `x, x+1, x+2, …`. First, insert all numbers into a hash set for O(1) membership checks. Then only start a scan at a **sequence start**—when `num - 1` is missing. From there, count forward until the gap.

### Approach Walkthrough  
Given `nums = [100, 4, 200, 1, 3, 2]`:
1. `st = {100,4,200,1,3,2}`.  
2. Consider `100`: `99` not in `st` → start a run. Count `100` only → length=1.  
3. `4`: `3` is in `st` → skip (not a start).  
4. `200`: `199` not in `st` → start run of one.  
5. `1`: `0` not in `st` → start: count `1,2,3,4` → length=4 → record best=4.  
… final answer 4.

### Detailed Code Breakdown  
```cpp
int longestConsecutive(const vector<int>& nums) {
    unordered_set<int> st(nums.begin(), nums.end());  // 1) Build hash set of all nums.
    int best = 0;                                     // 2) Track max run length.
    for (int num : nums) {                            // 3) Examine each number.
        if (!st.count(num - 1)) {                     // 4) Only if it's sequence start.
            int length = 1;                           //    run length at least 1.
            while (st.count(num + length))            // 5) Continue while next in set.
                ++length;
            best = max(best, length);                 // 6) Update best if needed.
        }
    }
    return best;                                      // 7) Return longest found.
}
```
- Line 1: Bulk‐construct a set from the vector in O(n).  
- Line 4: `st.count(num-1) == 0` means no predecessor → start.  
- Lines 5–6: Count up until gap, track max.

#### Pitfalls & Tips  
- Skipping numbers that are not sequence starts saves O(n²) brute force.  
- Watch out for integer overflow if values near `INT_MAX`.

---

## 4. Two Sum  
Link: https://leetcode.com/problems/two-sum/

### Theory: Complement Lookup with Hash Map  
As you scan, store each value’s index in a hash map. For current value `x`, compute `need = target - x`. If `need` is already in the map, you’ve found the pair; otherwise, continue.

### Approach Walkthrough  
`nums = [2, 7, 11, 15]`, `target = 9`:
1. Map `mp = {}`.  
2. `i=0, x=2`: `need=7`, not in `mp`. Store `mp[2]=0`.  
3. `i=1, x=7`: `need=2`, **2 is in** `mp` (index 0) → return `{0,1}`.

### Detailed Code Breakdown  
```cpp
vector<int> twoSum(const vector<int>& nums, int target) {
    unordered_map<int,int> mp;             // 1) Map value → index.
    for (int i = 0; i < nums.size(); ++i) { // 2) One‑pass scan.
        int need = target - nums[i];       // 3) Complement needed.
        if (mp.count(need))                // 4) Found earlier index?
            return {mp[need], i};          // 5) Return pair instantly.
        mp[nums[i]] = i;                   // 6) Otherwise store current value.
    }
    return {};                             // 7) Problem guarantees a solution.
}
```
- Line 1: `unordered_map<int,int>` for average O(1) lookup.  
- Line 4: `mp.count(need)` checks existence in O(1).  

#### Pitfalls & Tips  
- Don’t insert before checking, or you might pair an element with itself.  
- Clear `mp` between test cases.

---

## 5. Group Anagrams  
Link: https://leetcode.com/problems/group-anagrams/

### Theory: Canonical Signature via Sorting  
Anagrams share the same letters. By sorting each string’s characters, you obtain a **signature**. Group strings in a map keyed by that signature.

### Approach Walkthrough  
`strs = ["eat","tea","tan","ate","nat","bat"]`:
1. For `"eat"`, sorted → `"aet"`. Map: `{ "aet": ["eat"] }`.
2. `"tea"` → `"aet"` → append → `{ "aet": ["eat","tea"] }`.
3. `"tan"` → `"ant"` → new → `{ ..., "ant":["tan"]}`  
… final groups: `[["eat","tea","ate"], ["tan","nat"], ["bat"]]`.

### Detailed Code Breakdown  
```cpp
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> mp;  // 1) Key: sorted str → group.
    for (auto& s : strs) {                     // 2) For each original string…
        string key = s;                        // 3) Copy it.
        sort(key.begin(), key.end());          // 4) Sort letters to form signature.
        mp[key].push_back(s);                  // 5) Group by signature.
    }
    vector<vector<string>> res;                // 6) Prepare output.
    for (auto& [_, group] : mp)                // 7) Collect each group.
        res.push_back(move(group));
    return res;                                // 8) Return list of anagram groups.
}
```
- Lines 3–4: Sorting takes O(k log k) per string of length k.  
- Line 5: `mp[key].push_back(s)` appends original.

#### Pitfalls & Tips  
- Sorting each string can be slow if strings are long; an alternative is a 26‑count signature.  
- Be careful to move groups (`move(group)`) to avoid extra copies.

---

## 6. Top K Frequent Elements  
Link: https://leetcode.com/problems/top-k-frequent-elements/

### Theory: Frequency Map + Min‑Heap  
1. Count frequencies in a hash map.  
2. Keep a **min‑heap** (priority queue) of size k keyed by frequency.  
   - Push each `(freq, value)` pair.  
   - If heap size exceeds k, pop the smallest frequency.  
3. The heap ends up holding the k most frequent elements.

### Approach Walkthrough  
`nums = [1,1,1,2,2,3], k=2`:
- `freq = {1:3, 2:2, 3:1}`.  
- Push `(3,1)`, `(2,2)`, `(1,3)` into min‑heap:  
  1. heap=[(3,1)]  
  2. heap=[(2,2),(3,1)]  
  3. push (1,3) → heap=[(1,3),(3,1),(2,2)] → size>2 → pop smallest `(1,3)` → heap=[(2,2),(3,1)].  
- Extract → `[2,1]`, reverse → `[1,2]`.

### Detailed Code Breakdown  
```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int,int> freq;             // 1) Count frequencies.
    for (int x : nums) ++freq[x];            // 2) O(n) tally.

    // 3) Min‑heap of pairs (frequency, value). The smallest freq is at top.
    priority_queue<pair<int,int>,
        vector<pair<int,int>>, greater<>> minHeap;

    for (auto& [val, f] : freq) {            // 4) For each unique number…
        minHeap.emplace(f, val);             //    push (freq, value).
        if (minHeap.size() > k)              // 5) If too many, remove smallest.
            minHeap.pop();
    }

    vector<int> res;                         // 6) Collect results.
    while (!minHeap.empty()) {
        res.push_back(minHeap.top().second);// 7) Extract value from top.
        minHeap.pop();
    }
    reverse(res.begin(), res.end());         // 8) Highest freq first.
    return res;                              // 9) Return top k.
}
```
- Using `greater<>` flips the default max‑heap into a min‑heap.  

#### Pitfalls & Tips  
- Forgetting to `pop()` when size > k leads to O(n log n) instead of O(n log k).  
- Use `greater<>` template argument instead of hand‑writing a comparator.

---

## 7. Encode and Decode Strings  
Link: https://leetcode.com/problems/encode-and-decode-strings/

### Theory: Length‑Prefix Encoding  
Concatenate strings safely by prefixing each with its length and a delimiter. During decode, read the length, then extract exactly that many characters.

### Approach Walkthrough  
`strs = ["hello","world"]`  
- **Encode**:  
  - `"hello"` → `"5#hello"`  
  - `"world"` → `"5#world"`  
  - Combined → `"5#hello5#world"`.  
- **Decode**:  
  1. Read up to `#` → `"5"`, parse to int = 5.  
  2. Read next 5 chars → `"hello"`.  
  3. Move pointer, repeat for `"5#world"`.

### Detailed Code Breakdown  
```cpp
class Codec {
public:
    string encode(const vector<string>& strs) {
        string out;
        for (auto& s : strs) {
            // 1) Append "<length>#<content>"
            out += to_string(s.size()) + '#' + s;
        }
        return out;                          // 2) Return combined string.
    }

    vector<string> decode(const string& s) {
        vector<string> res;
        int i = 0, n = s.size();
        while (i < n) {
            int j = i;
            // 3) Find delimiter '#'
            while (s[j] != '#') ++j;
            // 4) Parse integer length from s[i..j-1]
            int len = stoi(s.substr(i, j - i));
            // 5) Extract the next 'len' characters
            res.push_back(s.substr(j + 1, len));
            // 6) Advance i past this segment
            i = j + 1 + len;
        }
        return res;                          // 7) Return list of strings.
    }
};
```
- Lines 3–4: `stoi` converts substring to integer.  
- Line 5: `substr(pos,len)` extracts exactly `len` chars.

#### Pitfalls & Tips  
- Ensure delimiter `#` never appears in length string.  
- Watch index updates carefully to avoid infinite loops.

---

## 8. Product of Array Except Self  
Link: https://leetcode.com/problems/product-of-array-except-self/

### Theory: Prefix & Suffix Products In‑Place  
We want for each index `i`: product of all nums except `nums[i]`.  
1. Build `res[i]` = product of all elements **left** of `i`.  
2. Traverse from the right, keep a running `rightProd` = product of elements **right** of `i`.  
3. Multiply `res[i] *= rightProd`.  

This uses O(1) extra space aside from the output.

### Approach Walkthrough  
`nums = [1,2,3,4]`  
- First pass (left):  
  - `res = [1,1,2,6]` because:  
    - `res[0] = 1` by definition.  
    - `res[1] = res[0]*nums[0] = 1*1`.  
    - `res[2] = res[1]*nums[1] = 1*2`.  
    - `res[3] = res[2]*nums[2] = 2*3`.  
- Second pass (right):  
  - Start `right=1`.  
  - At `i=3`: `res[3]*= right` → `6*1`; then `right*= nums[3]` → `4`.  
  - `i=2`: `res[2]*=4` → `2*4=8`; `right*=3`→12.  
  - etc. → final `res=[24,12,8,6]`.

### Detailed Code Breakdown  
```cpp
vector<int> productExceptSelf(const vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, 1);                // 1) Initialize output with 1s.

    // 2) Build prefix products in res.
    for (int i = 1; i < n; ++i)
        res[i] = res[i - 1] * nums[i - 1];

    // 3) Multiply suffix products on the fly.
    int right = 1;
    for (int i = n - 1; i >= 0; --i) {
        res[i] *= right;                  //    combine with product to the right
        right *= nums[i];                 //    update running right product
    }
    return res;                           // 4) Return final array.
}
```
- First loop builds left products; second loop merges right products.  

#### Pitfalls & Tips  
- Don’t use division—even if zeros appear, division breaks.  
- Correctly initialize `res` to 1’s.

---

## 9. Valid Sudoku  
Link: https://leetcode.com/problems/valid-sudoku/

### Theory: Three‑Way Hashing for Rows, Columns, Boxes  
A 9×9 Sudoku is valid if no digit 1–9 repeats in any row, column, or 3×3 sub‑box. We maintain three arrays of hash sets:

- **rows[9]**, **cols[9]**, **boxes[9]**

As we scan cell `(r,c)`, we compute its box index `b = (r/3)*3 + (c/3)`. If digit `d` already exists in any of these sets, it’s invalid.

### Approach Walkthrough  
Given a partial board, when you see `'5'` at `(0,0)`:

1. Check `rows[0]`—empty? OK, insert `'5'`.  
2. Check `cols[0]`—empty? OK, insert.  
3. `b = 0`, check `boxes[0]`—empty? OK, insert.  
Proceed until the end or until you find a duplicate.

### Detailed Code Breakdown  
```cpp
bool isValidSudoku(vector<vector<char>>& board) {
    // 1) Prepare 9 sets for rows, cols, and boxes each.
    vector<unordered_set<char>> rows(9), cols(9), boxes(9);

    for (int r = 0; r < 9; ++r) {
        for (int c = 0; c < 9; ++c) {
            char d = board[r][c];
            if (d == '.') continue;          // 2) Skip empty cells.

            int b = (r / 3) * 3 + (c / 3);   // 3) Compute box index 0–8.

            // 4) If insertion into any set fails, duplicate found.
            if (!rows[r].insert(d).second ||
                !cols[c].insert(d).second ||
                !boxes[b].insert(d).second)
                return false;               // 5) Invalid board.
        }
    }
    return true;                            // 6) No conflicts detected.
}
```
- Sets automatically handle uniqueness checks.  

#### Pitfalls & Tips  
- Watch integer division when computing `b`.  
- Reset sets between multiple boards.

---

## 🔧 Expanded Common Helper Functions  

```cpp
// Print a 1D vector of ints (e.g., results from Two Sum, TopK).
void printVec(const vector<int>& v) {
    // Iterate and print each element separated by space.
    for (int x : v) 
        cout << x << ' ';
    cout << "\n";                         // Newline after full vector.
}

// Print a 2D board of chars (e.g., Sudoku board).
void printBoard(const vector<vector<char>>& b) {
    for (auto& row : b) {
        for (char c : row)
            cout << c << ' ';
        cout << "\n";
    }
}
```

• **When to use**: debugging outputs, verifying intermediate states.  
• **Adaptation**: change types (e.g., `vector<string>`) or delimiters.  
• **Pitfalls**: printing large arrays slows down performance—remove in final submission.

---

## 🔍 Deep Dive into Paradigms

### 1. Hashing (Sets & Maps)
- **Key Idea**: constant‑time membership, insertion, deletion on average.  
- **Invariant**: each key appears at most once in a set.  
- **Proof Sketch**: hashing distributes keys into buckets; average chain length is O(1).  
- **Complexities**:  
  - Insert/lookup: O(1) average, O(n) worst.  
  - Space: O(n) for n items.

### 2. Sorting for Signatures
- **Key Idea**: transform data into canonical form (e.g., sorted letters).  
- **Invariant**: anagrams share identical signatures.  
- **Proof Sketch**: sorting orders characters uniquely.  
- **Complexities**: O(k log k) time per string of length k, O(k) extra space.

### 3. Heaps (Priority Queue)
- **Key Idea**: always access the smallest or largest element in O(1), adjust in O(log n).  
- **Invariant**: heap structure maintains partial order.  
- **Complexities**:  
  - Push/pop: O(log k).  
  - Space: O(k).

### 4. Two‑Pass Prefix/Suffix
- **Key Idea**: break a product (or sum) into left and right accumulations.  
- **Invariant**: after first pass, `res[i]` holds product of all to the left.  
- **Proof Sketch**: each element’s contribution is applied exactly once.  
- **Complexities**: O(n) time, O(1) extra space.

### 5. Delimiter Parsing
- **Key Idea**: encode length to know segment boundaries.  
- **Invariant**: every segment is unambiguously parsed by reading `<length>#<data>`.  
- **Complexities**: O(total length) time and space.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problem Sets
- **Easy → Medium → Hard**  
  - Contains Duplicate → Intersection of Two Arrays → Happy Number  
  - Valid Anagram → Palindrome Permutation → Minimum Window Substring  
  - Two Sum → 3Sum → 4Sum  
  - Group Anagrams → Anagrams in Slices → K Anagram Groups  
  - Top K Frequent → Kth Largest Element → Sliding Window Maximum  
  - Product Except Self Variants (with zeros, modulo)  
  - Sudoku Validator → Word Search → Battleships in a Board  

### Advanced Variants & Real‑World Applications
- Hash collisions in cryptography.  
- Frequency analysis in text mining.  
- Heaps in streaming top‑k queries.  
- Prefix/suffix in signal processing.

### Debugging Tips & Mental Checklists
1. **Hashing**: Did you initialize your set/map? Are you clearing between tests?  
2. **Indices & Off‑By‑One**: Walk through small arrays.  
3. **Loop Invariants**: At each step, what does your partial result represent?  
4. **Memory Use**: Can you reduce space (in‑place)?  
5. **Edge Cases**: Empty input, single element, all equal values, very large values.

Keep practicing, experiment with variations, and soon these patterns will feel second nature. Happy coding!  