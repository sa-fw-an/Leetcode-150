<!--markdownlint-disable-->
# 📚 Math & Geometry

This guide covers eight LeetCode problems under the **Math & Geometry** topic. For each problem, you’ll find:

1. A brief description  
2. Core idea or formula  
3. Step-by-step approach  
4. Concise C++ snippet with inline comments  
5. Time & space complexity  
6. Advanced tips & variations  

---

## 1. Rotate Image  
Link: https://leetcode.com/problems/rotate-image/

### Description  
Rotate an `n×n` matrix by 90° clockwise in-place.

### Core Idea  
Transpose the matrix, then reverse each row.

### Approach  
1. **Transpose**: swap `A[i][j]` with `A[j][i]` for `i<j`.  
2. **Reverse rows**: for each row `i`, swap `A[i][l]` with `A[i][n-1-l]`.

### C++ Snippet  
```cpp
void rotate(vector<vector<int>>& A) {
    int n = A.size();
    // Transpose
    for (int i = 0; i < n; ++i)
        for (int j = i+1; j < n; ++j)
            swap(A[i][j], A[j][i]);
    // Reverse each row
    for (auto &row : A)
        reverse(row.begin(), row.end());
}
```

Time: O(n²)  
Space: O(1)

Advanced Tip:  
- For anti-clockwise rotation: transpose then reverse columns.

---

## 2. Spiral Matrix  
Link: https://leetcode.com/problems/spiral-matrix/

### Description  
Return all elements of an `m×n` matrix in spiral order.

### Core Idea  
Maintain four boundaries (top, bottom, left, right) and peel off layers.

### Approach  
1. Initialize `top=0, bottom=m-1, left=0, right=n-1`.  
2. While `top<=bottom` and `left<=right`:
   - Traverse left→right on row `top++`.  
   - Traverse top→bottom on column `right--`.  
   - If `top<=bottom`, traverse right→left on row `bottom--`.  
   - If `left<=right`, traverse bottom→top on column `left++`.

### C++ Snippet  
```cpp
vector<int> spiralOrder(vector<vector<int>>& A) {
    int m = A.size(), n = A[0].size();
    int top = 0, bottom = m-1, left = 0, right = n-1;
    vector<int> res;
    while (top <= bottom && left <= right) {
        // left to right
        for (int j = left; j <= right; ++j) res.push_back(A[top][j]);
        ++top;
        // top to bottom
        for (int i = top; i <= bottom; ++i) res.push_back(A[i][right]);
        --right;
        if (top <= bottom) {
            // right to left
            for (int j = right; j >= left; --j) res.push_back(A[bottom][j]);
            --bottom;
        }
        if (left <= right) {
            // bottom to top
            for (int i = bottom; i >= top; --i) res.push_back(A[i][left]);
            ++left;
        }
    }
    return res;
}
```

Time: O(m·n)  
Space: O(m·n) for output

Advanced Tip:  
- Same pattern applies to fill a spiral matrix (reverse roles).

---

## 3. Set Matrix Zeroes  
Link: https://leetcode.com/problems/set-matrix-zeroes/

### Description  
If an element in an `m×n` matrix is 0, set its entire row and column to 0 in-place.

### Core Idea  
Use first row and first column as markers to avoid extra space.

### Approach  
1. Scan matrix, record if first row or col should be zero.  
2. For `i=1…m-1`, `j=1…n-1`, if `A[i][j]==0`, mark `A[i][0]=0` and `A[0][j]=0`.  
3. Second pass: for `i=1…m-1`, `j=1…n-1`, if marker row or col is 0, set `A[i][j]=0`.  
4. Zero out first row/col if needed.

### C++ Snippet  
```cpp
void setZeroes(vector<vector<int>>& A) {
    int m = A.size(), n = A[0].size();
    bool row0 = false, col0 = false;
    // mark first row/col
    for (int j = 0; j < n; ++j) if (A[0][j]==0) row0 = true;
    for (int i = 0; i < m; ++i) if (A[i][0]==0) col0 = true;
    for (int i = 1; i < m; ++i)
        for (int j = 1; j < n; ++j)
            if (A[i][j] == 0)
                A[i][0] = A[0][j] = 0;
    // apply markers
    for (int i = 1; i < m; ++i)
        for (int j = 1; j < n; ++j)
            if (A[i][0]==0 || A[0][j]==0)
                A[i][j] = 0;
    if (row0) fill(A[0].begin(), A[0].end(), 0);
    if (col0) for (int i = 0; i < m; ++i) A[i][0] = 0;
}
```

Time: O(m·n)  
Space: O(1)

Advanced Tip:  
- Alternatively, use two boolean arrays of size m and n for clarity.

---

## 4. Happy Number  
Link: https://leetcode.com/problems/happy-number/

### Description  
Determine if a number is “happy”: repeatedly replace `n` with the sum of squares of its digits; it is happy if it eventually reaches 1, cycles otherwise.

### Core Idea  
Cycle detection on a sequence: use Floyd’s tortoise and hare.

### Approach  
1. Define `next(n) = sum of squares of digits of n`.  
2. Initialize `slow = n`, `fast = next(n)`.  
3. Loop:  
   - `slow = next(slow)`  
   - `fast = next(next(fast))`  
   - Stop if `slow==fast`.  
4. Return `slow==1`.

### C++ Snippet  
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
    while (fast != slow) {
        slow = nextNum(slow);
        fast = nextNum(nextNum(fast));
    }
    return slow == 1;
}
```

Time: O(log n) per step, cycles quickly  
Space: O(1)

Advanced Tip:  
- Alternatively, use a hash set to detect repetition.

---

## 5. Plus One  
Link: https://leetcode.com/problems/plus-one/

### Description  
Given a non-empty array of digits representing a non-negative integer, add one to the integer.

### Core Idea  
Simulate addition with carry from least significant digit.

### Approach  
1. Traverse from end to start:  
   - `digits[i]++`; if `<10`, return; else set `digits[i]=0` and continue.  
2. If carry remains, insert 1 at front.

### C++ Snippet  
```cpp
vector<int> plusOne(vector<int>& D) {
    int n = D.size();
    for (int i = n-1; i >= 0; --i) {
        if (D[i] < 9) {
            ++D[i];
            return D;
        }
        D[i] = 0;
    }
    // all 9's
    D.insert(D.begin(), 1);
    return D;
}
```

Time: O(n)  
Space: O(1) → O(n) if new digit

Advanced Tip:  
- For in-place with no insert, handle with a fixed extra buffer.

---

## 6. Pow(x, n)  
Link: https://leetcode.com/problems/powx-n/

### Description  
Implement `pow(x, n)` (x^n) efficiently.

### Core Idea  
Fast exponentiation (binary exponentiation).

### Approach  
1. Handle `n<0` by inverting `x` and making `n = -n`.  
2. Initialize `result = 1`, `base = x`.  
3. While `n > 0`:  
   - If `n & 1`, `result *= base`.  
   - `base *= base`; `n >>= 1`.  
4. Return `result`.

### C++ Snippet  
```cpp
double myPow(double x, long n) {
    double res = 1.0;
    if (n < 0) {
        x = 1 / x;
        n = -n;
    }
    while (n) {
        if (n & 1) res *= x;
        x *= x;
        n >>= 1;
    }
    return res;
}
```

Time: O(log n)  
Space: O(1)

Advanced Tip:  
- Watch out for overflow on `n = INT_MIN`.

---

## 7. Multiply Strings  
Link: https://leetcode.com/problems/multiply-strings/

### Description  
Given two non-negative integers as strings, return their product as a string without using built‑in big integers.

### Core Idea  
Simulate grade-school multiplication using digit arrays.

### Approach  
1. Let `m = num1.size(), n = num2.size()`.  
2. Create `vector<int> res(m+n)` zero.  
3. Double loop `i=m-1→0`, `j=n-1→0`:  
   - `res[i+j+1] += (num1[i]-'0') * (num2[j]-'0')`.  
4. Handle carry: for `k=m+n-1→1`,  
   - `res[k-1] += res[k]/10`; `res[k] %= 10`.  
5. Skip leading zeros and build string.

### C++ Snippet  
```cpp
string multiply(string a, string b) {
    int m = a.size(), n = b.size();
    vector<int> res(m+n, 0);
    for (int i = m-1; i >= 0; --i) {
        for (int j = n-1; j >= 0; --j) {
            res[i+j+1] += (a[i]-'0') * (b[j]-'0');
        }
    }
    for (int k = m+n-1; k > 0; --k) {
        res[k-1] += res[k] / 10;
        res[k] %= 10;
    }
    // skip leading zeros
    int i = 0;
    while (i < res.size()-1 && res[i] == 0) ++i;
    string s;
    for (; i < res.size(); ++i) s.push_back(res[i] + '0');
    return s;
}
```

Time: O(m·n)  
Space: O(m+n)

Advanced Tip:  
- For extremely long inputs, use FFT-based multiplication.

---

## 8. Detect Squares  
Link: https://leetcode.com/problems/detect-squares/

### Description  
Design a data structure that supports adding points in the plane and counting how many axis-aligned squares can be formed with a given query point.

### Core Idea  
Store counts by `(x,y)` and for each query, iterate over all points sharing the same `x` or `y`, use matching distances.

### Approach  
1. Maintain `map<pair<int,int>, int> count` and `map<int, vector<int>> rows` (x → list of y).  
2. **add(point)**: increment `count[point]` and append `y` to `rows[x]`.  
3. **count(p)**:  
   - For each `y2` in `rows[p.x]`, `dy = |y2 - p.y|`, skip if `dy==0`.  
   - Check points `(p.x±dy, p.y)` and `(p.x±dy, y2)`:  
     - `cnt = count[{p.x, y2}] * count[{p.x+dy, p.y}] * count[{p.x+dy, y2}]`  
     - Sum over both directions.

### C++ Snippet  
```cpp
class DetectSquares {
    unordered_map<int, vector<int>> rows;
    unordered_map<long long, int> cnt;
    long long key(int x, int y) { return (1LL*x<<32) | (unsigned)y; }
public:
    void add(vector<int> p) {
        int x = p[0], y = p[1];
        rows[x].push_back(y);
        cnt[key(x,y)]++;
    }
    int count(vector<int> p) {
        int x = p[0], y = p[1], res = 0;
        for (int y2 : rows[x]) {
            int d = y2 - y;
            if (d == 0) continue;
            // check both left and right
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

Time:  
- `add`: O(1) amortized  
- `count`: O(k) where k = number of points with same x  
Space: O(N)

Advanced Tip:  
- Use a hash of (x,y) pair or nested maps for clarity; prune duplicates with a set.

---

Happy coding with Math & Geometry problems!  