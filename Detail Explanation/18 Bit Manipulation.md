<!--markdownlint-disable-->
# 📚 Bit Manipulation (Advanced Beginner‑Friendly Master Guide)

This guide deepens your understanding of seven classic **Bit Manipulation** problems from LeetCode. Each section includes:

1. **Theory**: Why bitwise operations solve the problem.  
2. **Approach Walkthrough**: A small example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary on each part of the C++ solution.  
4. **Pitfalls & Tips**: Common mistakes and best practices.  
5. **Complexity Analysis**: Time and space costs.  
6. **Advanced Tips & Variants**: Further optimizations and related problems.

---

## 1. Single Number  
Link: https://leetcode.com/problems/single-number/

### Theory: XOR Cancels Pairs  
XOR (`^`) has the property `x ^ x = 0` and `x ^ 0 = x`. If every number appears twice except one, XORing them all yields the unique one.

### Approach Walkthrough  
nums = [2, 1, 4, 5, 2, 4, 1]  
- Start `res=0`  
- XOR each in turn:  
  ```
  res = 0 ^ 2 = 2  
  res = 2 ^ 1 = 3  
  res = 3 ^ 4 = 7  
  res = 7 ^ 5 = 2  
  res = 2 ^ 2 = 0  
  res = 0 ^ 4 = 4  
  res = 4 ^ 1 = 5  
  ```  
- Final `res=5`.

### Code Breakdown  
```cpp
int singleNumber(vector<int>& nums) {
    int res = 0;                       
    // 1) Initialize result to 0.
    for (int x : nums) {
        res ^= x;                      
        // 2) XOR with each element: pairs cancel out.
    }
    return res;                       
    // 3) Remaining value is the unique number.
}
```

Pitfalls & Tips  
- Works only when all duplicates appear exactly twice.  
- For “appear three times” variants, use bit‑count per position mod 3.

Complexity  
- Time: O(n)  
- Space: O(1)

Advanced Variants  
- **Single Number II**: every element appears thrice except one – track bit counts mod 3.  
- **Single Number III**: two numbers appear once – use XOR partition by a set bit.

---

## 2. Number of 1 Bits  
Link: https://leetcode.com/problems/number-of-1-bits/

### Theory: Kernighan’s Bit‑Clear Trick  
Each `n & (n - 1)` clears the lowest set bit. Counting how many times you can clear a bit equals the number of 1’s.

### Approach Walkthrough  
n = 13 (1101₂)  
- count=0  
- n=1101 → n & (n-1)=1100 → count=1  
- n=1100 → n & (n-1)=1000 → count=2  
- n=1000 → n & (n-1)=0000 → count=3  
- Done.

### Code Breakdown  
```cpp
int hammingWeight(uint32_t n) {
    int count = 0;                        
    // 1) Initialize count of set bits.
    while (n) {
        n &= (n - 1);                     
        // 2) Clear lowest set bit.
        ++count;                         
        // 3) Increment count.
    }
    return count;                        
    // 4) Return total number of 1 bits.
}
```

Pitfalls & Tips  
- If input is signed, cast to `uint32_t` to avoid infinite sign-extended loops.  
- Intrinsic `__builtin_popcount` can be faster.

Complexity  
- Time: O(k), k = number of set bits  
- Space: O(1)

Advanced Variants  
- **Leading/Trailing zeros**: use `__builtin_clz` / `__builtin_ctz`.  
- **Parity**: XOR reduction of all bits.

---

## 3. Counting Bits  
Link: https://leetcode.com/problems/counting-bits/

### Theory: DP with Right‑Shift  
The number of 1’s in `i` equals the number in `i >> 1` plus the lowest bit `i & 1`.

### Approach Walkthrough  
n = 5 → ans size 6  
- ans[0]=0  
- ans[1]=ans[0]+1=1  
- ans[2]=ans[1]+0=1  
- ans[3]=ans[1]+1=2  
- ans[4]=ans[2]+0=1  
- ans[5]=ans[2]+1=2

### Code Breakdown  
```cpp
vector<int> countBits(int n) {
    vector<int> ans(n + 1);
    // 1) ans[0] = 0 by default.
    for (int i = 1; i <= n; ++i) {
        ans[i] = ans[i >> 1] + (i & 1);
        // 2) dp formula: half’s bits + LSB.
    }
    return ans;                          
    // 3) Return array of bit counts.
}
```

Pitfalls & Tips  
- This is O(n); naive O(n log n) bit-count per number is slower.  
- Works for any fixed bit-length domain.

Complexity  
- Time: O(n)  
- Space: O(n)

Advanced Variants  
- Precompute in blocks of 8 bits for lookup-table speed.

---

## 4. Reverse Bits  
Link: https://leetcode.com/problems/reverse-bits/

### Theory: Bit‑by‑Bit Reversal  
Extract the lowest bit, shift result left, and append. Repeat for 32 bits.

### Approach Walkthrough  
n = 0b000...0101 (5)  
- res=0  
- Iteration 1: res=1, n>>=1  
- Iteration 2: res=2, n>>=1  
- ... final res has bits reversed.

### Code Breakdown  
```cpp
uint32_t reverseBits(uint32_t n) {
    uint32_t res = 0;
    // 1) Loop through all 32 bits.
    for (int i = 0; i < 32; ++i) {
        res = (res << 1) | (n & 1);
        // 2) Shift result left, add current LSB.
        n >>= 1;
        // 3) Drop LSB from input.
    }
    return res;                          
    // 4) Return reversed-bit integer.
}
```

Pitfalls & Tips  
- For repeated calls, precompute a 16‑bit reverse lookup table for O(1) time.  
- Beware of signed vs. unsigned shifts.

Complexity  
- Time: O(1) (constant 32 iterations)  
- Space: O(1)

Advanced Variants  
- **Reverse bytes**: swap within 8‑bit chunks via table or SWAR.

---

## 5. Missing Number  
Link: https://leetcode.com/problems/missing-number/

### Theory: XOR Full Range  
XOR indices `[0..n]` with array values cancels all present numbers, leaving the missing one.

### Approach Walkthrough  
nums = [3,0,1] (n=3)  
- res = 0  
- i=0: res^=0^3 = 3  
- i=1: res^=1^0 = 2  
- i=2: res^=2^1 = 3  
- i=3: res^=3^undefined( skip array) → res=3^2 = 1  
- Missing = 2.

### Code Breakdown  
```cpp
int missingNumber(vector<int>& nums) {
    int n = nums.size(), res = 0;
    // 1) XOR indices and values.
    for (int i = 0; i <= n; ++i) {
        res ^= i;
        if (i < n) res ^= nums[i];
        // 2) Include array element.
    }
    return res;                          
    // 3) Remaining value is missing number.
}
```

Pitfalls & Tips  
- Ensure loop covers `i = n` without accessing `nums[n]`.  
- Sum-based method `n(n+1)/2 - sum(nums)` also works but may overflow 32-bit.

Complexity  
- Time: O(n)  
- Space: O(1)

Advanced Variants  
- Find two missing numbers via XOR partition.

---

## 6. Sum of Two Integers  
Link: https://leetcode.com/problems/sum-of-two-integers/

### Theory: Bitwise Addition  
XOR produces sum without carry, AND then shift produces carry. Repeat until carry is zero.

### Approach Walkthrough  
a=1, b=2  
- carry = (1&2)<<1 = 0  
- a = 1^2 = 3, b = 0 → result = 3.

### Code Breakdown  
```cpp
int getSum(int a, int b) {
    while (b != 0) {
        unsigned carry = (unsigned)(a & b) << 1;
        // 1) Compute carry bits.
        a = a ^ b;
        // 2) Sum bits without carry.
        b = carry;
        // 3) Propagate carry.
    }
    return a;                           
    // 4) When carry is zero, a holds the sum.
}
```

Pitfalls & Tips  
- Cast to unsigned for safe left shift.  
- Loop runs at most 32 times.

Complexity  
- Time: O(1) (bounded by number of bits)  
- Space: O(1)

Advanced Variants  
- Extend to arbitrary-precision via word arrays and similar logic.

---

## 7. Reverse Integer  
Link: https://leetcode.com/problems/reverse-integer/

### Theory: Pop & Push with Overflow Guard  
Pop last digit with modulo, push into result with multiply‑by‑10, checking for overflow before the push.

### Approach Walkthrough  
x = 123  
- res=0 → d=3 → res=3  
- x=12 → d=2 → res=32  
- x=1 → d=1 → res=321

### Code Breakdown  
```cpp
int reverse(int x) {
    int res = 0;
    while (x != 0) {
        int d = x % 10;
        x /= 10;
        // 1) Check overflow before multiply
        if (res > INT_MAX/10 || res < INT_MIN/10)
            return 0;
        res = res * 10 + d;
        // 2) Append digit.
    }
    return res;                          
    // 3) Return reversed integer or 0 on overflow.
}
```

Pitfalls & Tips  
- Check both high and low overflow (positive and negative).  
- Use 64‑bit temp if unsure.

Complexity  
- Time: O(log |x|)  
- Space: O(1)

Advanced Variants  
- **String-based** reverse yields simpler code but uses extra space.  
- **Linked‑list** reverse for digit lists.

---

## 🔍 Common Bit Manipulation Patterns  
- XOR to cancel pairs or compute parity.  
- `n & (n−1)` to remove lowest set bit.  
- Right‑shift DP: `dp[i] = dp[i>>1] + (i&1)`.  
- Bit‑by‑bit reversal via shifts.  
- Carry propagation via XOR and AND‑shift for addition.  
- Overflow checks on multiply‑and‑add.

---

## 🚀 Advanced Tips & Variants  
- Utilize compiler intrinsics:  
  - `__builtin_popcount` / `__builtin_clz` / `__builtin_ctz`.  
- Precompute small lookup tables (e.g., 8‑bit reverse table).  
- Solve variant “Single Number II” (triplicates) with bit‑count mod 3.  
- Generate Gray code by `i ^ (i >> 1)`.  
- Bit‑DP for subset problems (state compression).  
- SIMD/vectorized bit ops for performance-critical loops.

Happy bit‑twiddling mastery!  