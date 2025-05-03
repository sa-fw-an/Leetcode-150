<!--markdownlint-disable-->
# 📚 Math & Geometry (Advanced Beginner‑Friendly Master Guide)

This guide covers eight essential LeetCode **Math & Geometry** problems with in-depth theory, step‑by‑-step walkthroughs, line‑by‑line code explanations, pitfalls, complexity analysis, and advanced variants.

---

## 1. Rotate Image  
Link: https://leetcode.com/problems/rotate-image/

### Theory  
A 90° clockwise rotation of an `n×n` matrix can be done in-place by two reflections:
1. **Transpose** across the main diagonal (`A[i][j]` ↔ `A[j][i]`).
2. **Reverse** each row (swap left and right).

This avoids extra memory and uses simple element swaps.

### Approach Walkthrough  
```
A = [ [1,2,3],
      [4,5,6],
      [7,8,9] ]
```
1. Transpose →  
   ```
   [1,4,7]
   [2,5,8]
   [3,6,9]
   ```
2. Reverse each row →  
   ```
   [7,4,1]
   [8,5,2]
   [9,6,3]
   ```

### Code Breakdown  
```cpp
void rotate(vector<vector<int>>& A) {
    int n = A.size();
    // 1) Transpose: swap across diagonal
    for (int i = 0; i < n; ++i)
        for (int j = i+1; j < n; ++j)
            swap(A[i][j], A[j][i]);

    // 2) Reverse each row to complete rotation
    for (auto &row : A)
        reverse(row.begin(), row.end());
}
```

Pitfalls & Tips  
- Ensure `j` starts at `i+1` to avoid double-swapping.  
- For anti-clockwise rotation: reverse rows first, then transpose.

**Complexity**  
- Time: O(n²)  
- Space: O(1)

---

## 2. Spiral Matrix  
Link: https://leetcode.com/problems/spiral-matrix/

### Theory  
Traverse the matrix in concentric “layers” by maintaining four boundaries—top, bottom, left, right—and moving in four directions, then shrinking the boundaries.

### Approach Walkthrough  
```
A = [
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
```
- Layer 1: right → down → left → up  
  Collect [1,2,3,6,9,8,7,4], then shrink boundaries to center → [5].

### Code Breakdown  
```cpp
vector<int> spiralOrder(vector<vector<int>>& A) {
    int m = A.size(), n = A[0].size();
    int top = 0, bottom = m-1, left = 0, right = n-1;
    vector<int> res;
    while (top <= bottom && left <= right) {
        // 1) Traverse left→right
        for (int j = left; j <= right; ++j) res.push_back(A[top][j]);
        ++top;
        // 2) Traverse top→bottom
        for (int i = top; i <= bottom; ++i) res.push_back(A[i][right]);
        --right;
        if (top <= bottom) {
            // 3) Traverse right→left
            for (int j = right; j >= left; --j) res.push_back(A[bottom][j]);
            --bottom;
        }
        if (left <= right) {
            // 4) Traverse bottom→top
            for (int i = bottom; i >= top; --i) res.push_back(A[i][left]);
            ++left;
        }
    }
    return res;
}
```

Pitfalls & Tips  
- Check boundaries before each directional pass to avoid duplicates.  
- The same logic can fill a matrix in spiral order by reversing pushes.

**Complexity**  
- Time: O(m·n)  
- Space: O(m·n) for the result

---

## 3. Set Matrix Zeroes  
Link: https://leetcode.com/problems/set-matrix-zeroes/

### Theory  
When a cell is zero, its entire row and column become zero. Use the first row and column as in-place marker arrays to avoid extra memory.

### Approach Walkthrough  
```
A = [
 [1,1,1],
 [1,0,1],
 [1,1,1]
]
```
- Scan and mark row 1 and col 1.  
- Second pass zeroes cells with markers →  
```
[1,0,1]
[0,0,0]
[1,0,1]
```

### Code Breakdown  
```cpp
void setZeroes(vector<vector<int>>& A) {
    int m = A.size(), n = A[0].size();
    bool row0 = false, col0 = false;
    // 1) Check if first row/col need zeroing
    for (int j = 0; j < n; ++j) if (A[0][j] == 0) row0 = true;
    for (int i = 0; i < m; ++i) if (A[i][0] == 0) col0 = true;
    // 2) Use markers in first row/col
    for (int i = 1; i < m; ++i)
        for (int j = 1; j < n; ++j)
            if (A[i][j] == 0)
                A[i][0] = A[0][j] = 0;
    // 3) Zero out based on markers
    for (int i = 1; i < m; ++i)
        for (int j = 1; j < n; ++j)
            if (A[i][0] == 0 || A[0][j] == 0)
                A[i][j] = 0;
    // 4) Finally zero first row/col if needed
    if (row0) fill(A[0].begin(), A[0].end(), 0);
    if (col0) for (int i = 0; i < m; ++i) A[i][0] = 0;
}
```

Pitfalls & Tips  
- Do not overwrite markers prematurely; handle row0/col0 last.  
- A simpler approach uses two boolean arrays of size m and n at O(m+n) extra space.

**Complexity**  
- Time: O(m·n)  
- Space: O(1)

---

## 4. Happy Number  
Link: https://leetcode.com/problems/happy-number/

### Theory  
A sequence defined by repeatedly replacing `n` with the sum of the squares of its digits will either reach 1 (happy) or enter a cycle. Use Floyd’s cycle detection to find loops without extra memory.

### Approach Walkthrough  
n = 19  
- next(19)=1²+9²=82  
- next(82)=68 → 100 → 1 → happy.

### Code Breakdown  
```cpp
int nextNum(int x) {
    int s = 0;
    while (x) {
        int d = x % 10;
        s += d * d;
        x /= 10;
    }
    return s;
}

bool isHappy(int n) {
    int slow = n, fast = nextNum(n);
    // 1) Advance slow by 1, fast by 2
    while (slow != fast) {
        slow = nextNum(slow);
        fast = nextNum(nextNum(fast));
    }
    // 2) If meeting point is 1, it's happy
    return slow == 1;
}
```

Pitfalls & Tips  
- Avoid using a hash set by leveraging cycle detection.  
- Alternatively, track seen numbers in a set until 1 or repeat.

**Complexity**  
- Time: O(log n) per step, converges quickly  
- Space: O(1)

---

## 5. Plus One  
Link: https://leetcode.com/problems/plus-one/

### Theory  
Simulate addition with carry from least significant digit, setting digits to zero on overflow.

### Approach Walkthrough  
digits = [9,9]  
- i=1: 9+1→0 carry1  
- i=0: 9+carry→0 carry1  
- Insert 1 at front → [1,0,0].

### Code Breakdown  
```cpp
vector<int> plusOne(vector<int>& D) {
    int n = D.size();
    // 1) Add one from the end
    for (int i = n-1; i >= 0; --i) {
        if (D[i] < 9) {
            ++D[i];
            return D;   // 2) No further carry
        }
        D[i] = 0;       // 3) Overflow, carry continues
    }
    // 4) All digits were 9 → new leading 1
    D.insert(D.begin(), 1);
    return D;
}
```

Pitfalls & Tips  
- Inserting at front is O(n); consider reserving extra space if used in tight loops.

**Complexity**  
- Time: O(n)  
- Space: O(1) extra (O(n) if front inserted)

---

## 6. Pow(x, n)  
Link: https://leetcode.com/problems/powx-n/

### Theory  
Binary exponentiation splits `n` into bits, achieving O(log n) multiplications by squaring.

### Approach Walkthrough  
Compute `x^13`:  
- 13 in binary = 1101 → multiply x^(8) * x^(4) * x^(1)

### Code Breakdown  
```cpp
double myPow(double x, long n) {
    double res = 1.0;
    if (n < 0) {
        x = 1/x;
        n = -n;
    }
    // 1) Loop until all bits consumed
    while (n) {
        if (n & 1) res *= x;   // 2) Multiply when low bit is 1
        x *= x;                // 3) Square base
        n >>= 1;               // 4) Shift to next bit
    }
    return res;
}
```

Pitfalls & Tips  
- For `n = INT_MIN`, converting `-n` overflows 32-bit; use 64-bit `long`.  
- Early return for `x == 1` or `n == 0`.

**Complexity**  
- Time: O(log |n|)  
- Space: O(1)

---

## 7. Multiply Strings  
Link: https://leetcode.com/problems/multiply-strings/

### Theory  
Perform grade‑school multiplication: each digit pair contributes to a result array at position `i+j+1`, then handle carries.

### Approach Walkthrough  
num1="12", num2="34":  
- products: 1*3 at res[0+0+1], 1*4 at res[0+1+1]...  
- handle carry to finalize digits.

### Code Breakdown  
```cpp
string multiply(string a, string b) {
    int m = a.size(), n = b.size();
    vector<int> res(m+n);
    // 1) Multiply digit pairs
    for (int i = m-1; i >= 0; --i)
        for (int j = n-1; j >= 0; --j)
            res[i+j+1] += (a[i]-'0') * (b[j]-'0');
    // 2) Handle carries
    for (int k = m+n-1; k > 0; --k) {
        res[k-1] += res[k] / 10;
        res[k] %= 10;
    }
    // 3) Build string, skip leading zeros
    int idx = 0;
    while (idx < res.size()-1 && res[idx] == 0) ++idx;
    string s;
    for (; idx < res.size(); ++idx)
        s.push_back(res[idx] + '0');
    return s;
}
```

Pitfalls & Tips  
- Watch out for leading zeros—always leave at least one digit.  
- For very large inputs, consider FFT multiplication.

**Complexity**  
- Time: O(m·n)  
- Space: O(m+n)

---

## 8. Detect Squares  
Link: https://leetcode.com/problems/detect-squares/

### Theory  
Store counts of points and group by x-coordinate. For a query, fix one axis and iterate over candidate points to form squares, using counts to multiply corner possibilities.

### Approach Walkthrough  
Points added: (3,10), (11,2), (3,2)  
Query (11,10):
- p.y2 = 2 → dy = 8 → check (3,10),(3,2),(11,2) → 1 square.

### Code Breakdown  
```cpp
class DetectSquares {
    unordered_map<int, vector<int>> rows;
    unordered_map<long long, int> cnt;
    long long key(int x,int y){ return (1LL*x<<32)|(unsigned)y; }
public:
    void add(vector<int> p) {
        int x = p[0], y = p[1];
        rows[x].push_back(y);
        cnt[key(x,y)]++;
    }
    int count(vector<int> p) {
        int x = p[0], y = p[1], res = 0;
        // 1) Iterate all y2 at same x
        for (int y2 : rows[x]) {
            int d = y2 - y;
            if (d == 0) continue;
            // 2) Check both square orientations
            for (int sign : {1, -1}) {
                int x2 = x + sign * abs(d);
                res += cnt[key(x,y2)]
                     * cnt[key(x2,y)]
                     * cnt[key(x2,y2)];
            }
        }
        return res;
    }
};
```

Pitfalls & Tips  
- Combining `x` and `y` into a 64-bit key avoids nested maps.  
- Remove duplicate y-values by using a multiset if duplicates should be collapsed.

**Complexity**  
- `add`: O(1) amortized  
- `count`: O(k) where k = points sharing the same x  
- Space: O(N) points

---

## 🔍 Algorithmic Paradigms

| Problem             | Paradigm                           |
|---------------------|------------------------------------|
| Rotate Image        | Matrix reflection and reversal     |
| Spiral Matrix       | Boundary-based layer traversal     |
| Set Matrix Zeroes   | In-place marking via first row/col |
| Happy Number        | Cycle detection in sequence        |
| Plus One            | Digit-wise carry simulation        |
| Pow(x, n)           | Binary exponentiation              |
| Multiply Strings    | Grade-school big-integer multiply  |
| Detect Squares      | Hashing + combinatorial counting   |

---

## 🚀 Advanced Tips & Variations

- **Matrix Operations**: Extend rotate and spiral patterns to non-square or 3D arrays.  
- **Marker Techniques**: Use bitmasks or sign flips for in-place marking.  
- **Cycle Detection**: Apply Floyd’s algorithm to any iterative numeric sequence.  
- **Arbitrary Precision**: Implement addition, subtraction, and multiplication on strings.  
- **Spatial Hashing**: For geometric queries, grid‑based hashing can speed up neighbor lookups.  
- **Related Problems**:  
  - “Rotate List” (linked-list rotation)  
  - “Spiral Matrix II” (matrix fill)  
  - “Game of Life” (in-place state transitions)  
  - “Power of Two” variations  
  - “Four Sum” (combinatorial enumeration)  

Happy mastering Math & Geometry algorithms!  