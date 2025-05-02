<!--markdownlint-disable-->
# 📚 Bit Manipulation

This guide covers seven classic LeetCode problems under the **Bit Manipulation** topic. For each problem, you’ll find:

1. A brief description  
2. Core bitwise idea  
3. Step‑by‑step approach  
4. A concise C++ snippet with inline comments  
5. Time & space complexity  
6. Advanced tips & related variants  

---

## 1. Single Number  
Link: https://leetcode.com/problems/single-number/

### Description  
Given a non‑empty array of integers where every element appears twice except one, find that single one.

### Core Idea: XOR Cancels Pairs  
- `x ^ x = 0`, and `x ^ 0 = x`. XORing all numbers leaves the unique one.

### Approach  
1. Initialize `res = 0`.  
2. Loop through each `num` in `nums`: `res ^= num`.  
3. Return `res`.

### C++ Snippet  
```cpp
int singleNumber(vector<int>& nums) {
    int res = 0;
    for (int x : nums) {
        res ^= x;          // cancel pairs, keep unique
    }
    return res;
}
```

Time: O(n)  
Space: O(1)

---

## 2. Number of 1 Bits  
Link: https://leetcode.com/problems/number-of-1-bits/

### Description  
Write a function that takes an unsigned integer and returns the number of ’1’ bits it has (Hamming weight).

### Core Idea: Brian Kernighan’s Trick  
- Repeatedly do `n &= (n – 1)` to clear the lowest set bit, counting iterations.

### Approach  
1. Initialize `count = 0`.  
2. While `n != 0`:  
   - `n &= (n – 1)`  
   - `++count`  
3. Return `count`.

### C++ Snippet  
```cpp
int hammingWeight(uint32_t n) {
    int count = 0;
    while (n) {
        n &= (n - 1);     // drop lowest set bit
        ++count;
    }
    return count;
}
```

Time: O(k) where k = number of set bits  
Space: O(1)

---

## 3. Counting Bits  
Link: https://leetcode.com/problems/counting-bits/

### Description  
Given `n`, return an array `ans` of length `n + 1` where `ans[i]` is the number of 1’s in the binary representation of `i`.

### Core Idea: DP by Right‑Shift  
- `ans[i] = ans[i >> 1] + (i & 1)`

### Approach  
1. Initialize `ans` of size `n+1`, `ans[0] = 0`.  
2. For `i` from 1 to `n`:  
   - `ans[i] = ans[i >> 1] + (i & 1)`.  
3. Return `ans`.

### C++ Snippet  
```cpp
vector<int> countBits(int n) {
    vector<int> ans(n + 1);
    for (int i = 1; i <= n; ++i) {
        ans[i] = ans[i >> 1] + (i & 1);
    }
    return ans;
}
```

Time: O(n)  
Space: O(n)

---

## 4. Reverse Bits  
Link: https://leetcode.com/problems/reverse-bits/

### Description  
Reverse the bits of a given 32‑bit unsigned integer.

### Core Idea: Bit‑by‑Bit Reversal  
- Extract the lowest bit, append it to result’s LSB, then shift.

### Approach  
1. Initialize `res = 0`.  
2. For 32 iterations:  
   - `res = (res << 1) | (n & 1)`  
   - `n >>= 1`  
3. Return `res`.

### C++ Snippet  
```cpp
uint32_t reverseBits(uint32_t n) {
    uint32_t res = 0;
    for (int i = 0; i < 32; ++i) {
        res = (res << 1) | (n & 1);
        n >>= 1;
    }
    return res;
}
```

Time: O(1) (32 loops)  
Space: O(1)

---

## 5. Missing Number  
Link: https://leetcode.com/problems/missing-number/

### Description  
Given an array containing `n` distinct numbers taken from `0, 1, 2, …, n`, find the one that is missing.

### Core Idea: XOR Full Range  
- XOR all indices `0…n` and all elements; the result is the missing number.

### Approach  
1. Let `n = nums.size()`.  
2. Initialize `res = 0`.  
3. For `i` from 0 to `n`:  
   - `res ^= i`  
   - If `i < n`, `res ^= nums[i]`.  
4. Return `res`.

### C++ Snippet  
```cpp
int missingNumber(vector<int>& nums) {
    int n = nums.size(), res = 0;
    for (int i = 0; i <= n; ++i) {
        res ^= i;                      // include index
        if (i < n) res ^= nums[i];    // include value
    }
    return res;
}
```

Time: O(n)  
Space: O(1)

---

## 6. Sum of Two Integers  
Link: https://leetcode.com/problems/sum-of-two-integers/

### Description  
Compute the sum of two integers `a` and `b`, but you are not allowed to use `+` or `-` operators.

### Core Idea: Bitwise Addition  
- Use XOR for sum without carry, AND + shift for carry; iterate until no carry.

### Approach  
1. While `b != 0`:  
   - `carry = (unsigned)(a & b) << 1`  
   - `a = a ^ b`  
   - `b = carry`  
2. Return `a`.

### C++ Snippet  
```cpp
int getSum(int a, int b) {
    while (b != 0) {
        unsigned carry = (unsigned)(a & b) << 1;
        a = a ^ b;       // partial sum
        b = carry;       // carry
    }
    return a;
}
```

Time: O(1) (at most 32 iterations)  
Space: O(1)

---

## 7. Reverse Integer  
Link: https://leetcode.com/problems/reverse-integer/

### Description  
Given a 32‑bit signed integer, reverse its digits. Return 0 on overflow.

### Core Idea: Pop & Push with Overflow Check  
- Pop last digit via `x % 10`, push to result via `res * 10 + digit`, guarding against overflow.

### Approach  
1. Initialize `res = 0`.  
2. While `x != 0`:  
   - `digit = x % 10`; `x /= 10`.  
   - If `res > INT_MAX/10` or `< INT_MIN/10`, return 0.  
   - `res = res * 10 + digit`.  
3. Return `res`.

### C++ Snippet  
```cpp
int reverse(int x) {
    int res = 0;
    while (x != 0) {
        int d = x % 10;
        x /= 10;
        if (res > INT_MAX/10 || res < INT_MIN/10) return 0;
        res = res * 10 + d;
    }
    return res;
}
```

Time: O(log |x|)  
Space: O(1)

---

## 🔍 Common Bit Manipulation Patterns  
- XOR for pair cancellation, parity, swapping without temp (`a ^= b; b ^= a; a ^= b;`).  
- Mask & shift for bit extraction and assembly.  
- Brian Kernighan’s trick to count or clear bits.  
- DP on bits (e.g., `ans[i] = ans[i>>1] + (i&1)`).  
- Summation via bitwise addition loop.  
- Overflow guards when pushing digits.

---

## 🚀 Advanced Tips & Variants  
- Use compiler intrinsics: `__builtin_popcount`, `__builtin_clz`.  
- Precompute reverse‑byte table to speed up `reverseBits`.  
- Solve “Single Number II” with bit‑count mod 3.  
- Generate subsets/Gray codes by toggling one bit at a time.  
- Use bit DP for state‑compressed DP on subsets.  
- Fast exponentiation by exponent bits for pow in integers.

Happy bit‑twiddling and mastering binary tricks!  