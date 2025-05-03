<!--markdownlint-disable-->
# 📚 Greedy (Advanced Beginner‑Friendly Master Guide)

This guide deepens eight classic LeetCode **Greedy** problems with:

1. **Theory** – Why the greedy choice works.  
2. **Approach Walkthrough** – A concrete example you can trace.  
3. **Line‑by‑Line Code Breakdown** – Detailed commentary on each snippet.  
4. **Pitfalls & Tips** – Common mistakes and how to avoid them.  
5. **Complexity Analysis** – Time and space costs.  
6. **Advanced Tips & Practice** – Further optimizations and related problems.

---

## 1. Maximum Subarray  
Link: https://leetcode.com/problems/maximum-subarray/

### Theory: Kadane’s Local Optimum  
At each position, the best subarray ending here is either extend the previous one or start fresh at the current element.

### Approach Walkthrough  
nums = [-2,1,-3,4,-1,2,1,-5,4]  
- curr starts 0, maxSum = -2.  
- i=0: curr=max(-2,0–2)=-2, maxSum=−2  
- i=1: curr=max(1,−2+1)=1,  maxSum=1  
- ...  
- Final maxSum = 6 from subarray [4,-1,2,1].

### Code Breakdown  
```cpp
int maxSubArray(vector<int>& nums) {
    int maxSum = nums[0], curr = 0;
    // 1) maxSum tracks global best; curr is best ending here.
    for (int x : nums) {
        curr = max(x, curr + x);
        // 2) Either start new at x or extend previous by x.
        maxSum = max(maxSum, curr);
        // 3) Update global best if needed.
    }
    return maxSum; 
    // 4) Return highest subarray sum.
}
```

Pitfalls & Tips  
- Initialize `maxSum` to `nums[0]` to handle all-negative arrays.  
- Track start/end indices if you need the actual subarray.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 2. Jump Game  
Link: https://leetcode.com/problems/jump-game/

### Theory: Greedy Furthest Reach  
Keep the farthest index you can reach. If you reach beyond the last index, return true; if you hit a gap you can’t cross, return false.

### Approach Walkthrough  
nums = [2,3,1,1,4]  
- reach=0  
- i=0: reach=max(0,0+2)=2  
- i=1: reach=max(2,1+3)=4 → reach ≥ last index → return true.

### Code Breakdown  
```cpp
bool canJump(vector<int>& nums) {
    int reach = 0;
    // 1) reach = farthest index reachable so far.
    for (int i = 0; i < nums.size(); ++i) {
        if (i > reach) return false;
        // 2) If current index beyond reach, fail.
        reach = max(reach, i + nums[i]);
        // 3) Update furthest reachable.
        if (reach >= nums.size() - 1)
            return true;
        // 4) Early exit when you can already reach end.
    }
    return true;
    // 5) All indices were within reach.
}
```

Pitfalls & Tips  
- Check `i > reach` before updating.  
- Early exit when `reach` covers the end.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 3. Jump Game II  
Link: https://leetcode.com/problems/jump-game-ii/

### Theory: Greedy Layer‑by‑Layer  
Treat each range of indices reachable in the same number of jumps as a “layer.” When you exhaust the current layer, you make a jump and extend to the next layer.

### Approach Walkthrough  
nums = [2,3,1,1,4]  
- layer 0–0: furthest=2 → jumps=1, next layer 1–2  
- scan 1–2, update furthest=4 → jumps=2 → end reached.

### Code Breakdown  
```cpp
int jump(vector<int>& nums) {
    int jumps = 0, currEnd = 0, furthest = 0;
    // 1) currEnd = end of current layer; furthest = max reach in layer.
    for (int i = 0; i < nums.size() - 1; ++i) {
        furthest = max(furthest, i + nums[i]);
        // 2) Track potential reach.
        if (i == currEnd) {
            ++jumps;               // 3) End of layer ⇒ make a jump
            currEnd = furthest;    // 4) Next layer end
        }
    }
    return jumps;
    // 5) Minimum jumps to reach last index.
}
```

Pitfalls & Tips  
- Do not increment jumps on the last index.  
- Ensure loop up to `size()-2` to avoid extra jump.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 4. Gas Station  
Link: https://leetcode.com/problems/gas-station/

### Theory: Total vs. Local Net Sum  
If total gas minus cost is negative, no solution exists. Otherwise, the station after any point where your tank goes negative is the next candidate start.

### Approach Walkthrough  
gas = [1,2,3,4,5], cost = [3,4,5,1,2]  
- total sum = 0 → possible  
- local tank drops below 0 at i=0 → start=1 → final start=3.  

### Code Breakdown  
```cpp
int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int total = 0, tank = 0, start = 0;
    // 1) total = sum of net gas for feasibility.
    for (int i = 0; i < gas.size(); ++i) {
        int diff = gas[i] - cost[i];
        total += diff;  
        tank  += diff;  // 2) local tank for current start.
        if (tank < 0) {
            tank = 0;     // 3) reset if negative
            start = i + 1;  
            // 4) next station is new candidate
        }
    }
    return (total >= 0) ? start : -1;
    // 5) return -1 if total < 0.
}
```

Pitfalls & Tips  
- Reset only the local tank, not total.  
- If multiple failures, only the first reset matters.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 5. Hand of Straights  
Link: https://leetcode.com/problems/hand-of-straights/

### Theory: Sequential Dequeue on Counts  
Sort by card value via a map. For each smallest card, remove that frequency from the next `W` consecutive values.

### Approach Walkthrough  
hand = [1,2,3,6,2,3,4,7,8], W = 3  
- counts: 1→1, 2→2, 3→2, 4→1, 6→1,7→1,8→1  
- at card=1 freq=1, remove one each of [1,2,3] → updated counts  
- continue until all grouped.

### Code Breakdown  
```cpp
bool isNStraightHand(vector<int>& hand, int W) {
    if (hand.size() % W) return false;  // 1) total size must be multiple of W
    map<int,int> cnt;
    for (int x : hand) cnt[x]++;
    for (auto& [card, freq] : cnt) {
        if (freq == 0) continue;          
        // 2) Attempt to build sequences starting at card
        for (int x = card; x < card + W; ++x) {
            if (cnt[x] < freq) return false;
            cnt[x] -= freq;              
            // 3) Remove freq cards in this range
        }
    }
    return true;                          
    // 4) All groups formed
}
```

Pitfalls & Tips  
- Use a queue of unique sorted keys to skip zeros quickly.  
- A `multiset` approach works but is slower.  

Complexity  
- Time: O(n log n + n·W)  
- Space: O(n)

---

## 6. Merge Triplets to Form Target Triplet  
Link: https://leetcode.com/problems/merge-triplets-to-form-target-triplet/

### Theory: Coordinate‑Wise Max under Constraints  
Only consider triplets that do not exceed target in any position. Track the maximum seen for each coordinate; if all match, you can merge to the target.

### Approach Walkthrough  
trips = [[2,5,3],[1,8,4],[1,7,5]]  
target = [2,7,5]  
- skip [1,8,4] (8>7)  
- best = max([2,5,3],[1,7,5]) = [2,7,5] → matches.

### Code Breakdown  
```cpp
bool mergeTriplets(vector<vector<int>>& trips, vector<int>& target) {
    vector<int> best(3, 0);
    for (auto& t : trips) {
        if (t[0] > target[0] || t[1] > target[1] || t[2] > target[2])
            continue;                   
        // 1) ignore invalid triplet
        for (int i = 0; i < 3; ++i)
            best[i] = max(best[i], t[i]);
            // 2) track coordinate maxima
    }
    return best == target;            
    // 3) success if all coords match
}
```

Pitfalls & Tips  
- No need to simulate every merge; max-tracking suffices.  
- If best[i] < target[i] for some i, impossible.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 7. Partition Labels  
Link: https://leetcode.com/problems/partition-labels/

### Theory: Interval Cut by Last Occurrence  
Each character defines an interval from its first to last index. Greedily form a partition by extending to the farthest last index seen in the current window.

### Approach Walkthrough  
S = "ababcbacadefegdehijhklij"  
- last['a']=8, extend end to 8 while scanning from 0.  
- At i=8, cut size=9. Next window starts at 9, ends at 15..., etc.

### Code Breakdown  
```cpp
vector<int> partitionLabels(string S) {
    vector<int> last(26, 0);
    for (int i = 0; i < S.size(); ++i)
        last[S[i] - 'a'] = i;
    // 1) record last index of each char

    vector<int> parts;
    int start = 0, end = 0;
    for (int i = 0; i < S.size(); ++i) {
        end = max(end, last[S[i] - 'a']);
        // 2) extend partition end
        if (i == end) {
            parts.push_back(end - start + 1);
            // 3) cut here
            start = i + 1;
        }
    }
    return parts;
    // 4) list of part sizes
}
```

Pitfalls & Tips  
- Precompute last indices in one pass.  
- This is equivalent to merging overlapping intervals.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 8. Valid Parenthesis String  
Link: https://leetcode.com/problems/valid-parenthesis-string/

### Theory: Possible Range of Open Count  
Track a range `[low, high]` of possible open‑parenthesis counts. Update on each char, and ensure the range never goes negative. Valid if `low` can be zero at the end.

### Approach Walkthrough  
s = "(*))"  
- '(' → [1,1]  
- '*' → [0,2]  
- ')' → [−1,1] clamp low=0 → [0,1]  
- ')' → [−1,0] clamp low=0, high=0 → valid.

### Code Breakdown  
```cpp
bool checkValidString(string s) {
    int low = 0, high = 0;
    for (char c : s) {
        if (c == '(') {
            ++low; ++high;
        } else if (c == ')') {
            --low; --high;
        } else { // '*'
            --low; ++high;
        }
        if (high < 0) return false;   // 1) too many ')'
        low = max(low, 0);            // 2) clamp
    }
    return low == 0;                  // 3) can close all opens
}
```

Pitfalls & Tips  
- Clamp `low` to zero after each step.  
- If `high` drops below zero, no possible valid sequence remains.  

Complexity  
- Time: O(n)  
- Space: O(1)

---

## 🔍 Algorithmic Paradigms  

| Problem                                | Greedy Pattern                   |
|----------------------------------------|----------------------------------|
| Maximum Subarray                       | Kadane’s local optimum           |
| Jump Game / Jump Game II               | Furthest reach & layered jumps   |
| Gas Station                            | Net sum & reset start            |
| Hand of Straights                      | Sequential dequeue on counts     |
| Merge Triplets to Form Target Triplet  | Coordinate-wise maxima           |
| Partition Labels                       | Interval cut by last occurrence  |
| Valid Parenthesis String               | Range tracking of possibilities  |

---

## 🚀 Advanced Tips & Practice  

- **Prove Greedy Correctness**: argue that each local choice cannot harm the global optimum.  
- **Combine with Sorting**: many greedy solutions start by sorting keys or intervals.  
- **Test Edge Cases**: empty input, minimal sizes, all negatives or all positives.  
- Related problems to master:  
  - “Candy” (two-pass greedy)  
  - “Minimum Number of Arrows to Burst Balloons” (interval scheduling)  
  - “Car Fleet” (rear-front grouping)  
  - “Score of Parentheses” (stack + greedy)  

Happy coding and conquering Greedy patterns!  