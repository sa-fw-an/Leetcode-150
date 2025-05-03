<!--markdownlint-disable-->
# 📚 Greedy

This guide covers eight classic LeetCode problems under the **Greedy** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Core greedy idea  
3. A concise C++ snippet with inline comments  
4. Complexity analysis  
5. Advanced tips & practice  

---

## 1. Maximum Subarray  
Link: https://leetcode.com/problems/maximum-subarray/

### Description  
Find the contiguous subarray (containing at least one number) which has the largest sum.

### Core Idea: Kadane’s Algorithm  
Maintain a running sum; reset to zero whenever it becomes negative, track the maximum.

### C++ Snippet  
```cpp
int maxSubArray(vector<int>& nums) {
    int maxSum = nums[0], curr = 0;
    for (int x : nums) {
        curr = max(x, curr + x);      // extend or restart
        maxSum = max(maxSum, curr);   // update best
    }
    return maxSum;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- For tracking indices, record start when curr resets and end when maxSum updates.

---

## 2. Jump Game  
Link: https://leetcode.com/problems/jump-game/

### Description  
Given an array where each element is the max jump length, determine if you can reach the last index.

### Core Idea: Furthest Reachable Index  
Iterate and keep the maximum index reachable; if at any point index exceeds reach, you cannot proceed.

### C++ Snippet  
```cpp
bool canJump(vector<int>& nums) {
    int reach = 0;
    for (int i = 0; i < nums.size(); ++i) {
        if (i > reach) return false;      // gap detected
        reach = max(reach, i + nums[i]);  // update furthest
    }
    return true;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- Early exit as soon as reach ≥ last index.

---

## 3. Jump Game II  
Link: https://leetcode.com/problems/jump-game-ii/

### Description  
Return the minimum number of jumps to reach the last index.

### Core Idea: Greedy Level‑by‑Level  
Maintain a current window `[start…end]`; within it track furthest reach; when you exhaust end, make a jump and extend window.

### C++ Snippet  
```cpp
int jump(vector<int>& nums) {
    int jumps = 0, currEnd = 0, furthest = 0;
    for (int i = 0; i < nums.size() - 1; ++i) {
        furthest = max(furthest, i + nums[i]);
        if (i == currEnd) {               // time to jump
            ++jumps;
            currEnd = furthest;
        }
    }
    return jumps;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- This simulates BFS layers without an explicit queue.

---

## 4. Gas Station  
Link: https://leetcode.com/problems/gas-station/

### Description  
Given `gas[i]` and `cost[i]`, find a start index so you can travel around the circuit once, or –1 if impossible.

### Core Idea: Net and Local Sum  
Track total net gas; if it’s negative, no solution. Meanwhile track local sum—when it drops below zero, reset start to next station.

### C++ Snippet  
```cpp
int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int total = 0, tank = 0, start = 0;
    for (int i = 0; i < gas.size(); ++i) {
        int diff = gas[i] - cost[i];
        total += diff;
        tank += diff;
        if (tank < 0) {    // cannot start from current start
            tank = 0;
            start = i + 1;
        }
    }
    return total >= 0 ? start : -1;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- The greedy reset works because any failure between start and i implies no station in between can be a valid start.

---

## 5. Hand of Straights  
Link: https://leetcode.com/problems/hand-of-straights/

### Description  
Determine if an array of cards can be rearranged into groups of size `W` consisting of consecutive cards.

### Core Idea: Count & Sequential Dequeue  
Count frequencies, then process cards in ascending order: for each smallest card, attempt to decrement the next `W` consecutive counts.

### C++ Snippet  
```cpp
bool isNStraightHand(vector<int>& hand, int W) {
    if (hand.size() % W) return false;
    map<int,int> cnt;
    for (int x : hand) cnt[x]++;
    for (auto& [card, freq] : cnt) {
        if (freq == 0) continue;
        for (int x = card; x < card + W; ++x) {
            if (cnt[x] < freq) return false; // not enough cards
            cnt[x] -= freq;
        }
    }
    return true;
}
```

Time: O(n log n + n·W)  
Space: O(n)

Advanced Tip:  
- Use a `queue` of unique cards to avoid iterating empty values in the map.

---

## 6. Merge Triplets to Form Target Triplet  
Link: https://leetcode.com/problems/merge-triplets-to-form-target-triplet/

### Description  
Given a list of triplets and a target triplet, you can “merge” two triplets by taking element-wise maxima. Determine if you can form the target by merging some of the given triplets.

### Core Idea: Track Maximums Under Constraints  
Ignore any triplet that exceeds the target in any position. For the rest, maintain the maximum seen for each coordinate. If all three match the target, return true.

### C++ Snippet  
```cpp
bool mergeTriplets(vector<vector<int>>& trips, vector<int>& target) {
    vector<int> best(3, 0);
    for (auto& t : trips) {
        if (t[0] > target[0] || t[1] > target[1] || t[2] > target[2])
            continue;                     // invalid triplet
        for (int i = 0; i < 3; ++i)
            best[i] = max(best[i], t[i]);
    }
    return best == target;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- One pass suffices; no need to simulate actual merges.

---

## 7. Partition Labels  
Link: https://leetcode.com/problems/partition-labels/

### Description  
Divide a string into as many parts as possible so that each letter appears in at most one part; return the list of part sizes.

### Core Idea: Last Occurrence & Greedy Cut  
Precompute each character’s last index. Scan and extend the current partition’s end to the furthest last index. When index reaches the end, cut and start a new part.

### C++ Snippet  
```cpp
vector<int> partitionLabels(string S) {
    vector<int> last(26);
    for (int i = 0; i < S.size(); ++i)
        last[S[i]-'a'] = i;
    vector<int> parts;
    int start = 0, end = 0;
    for (int i = 0; i < S.size(); ++i) {
        end = max(end, last[S[i]-'a']);
        if (i == end) {
            parts.push_back(end - start + 1);
            start = i + 1;
        }
    }
    return parts;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- This is an instance of interval scheduling/merging in one dimension.

---

## 8. Valid Parenthesis String  
Link: https://leetcode.com/problems/valid-parenthesis-string/

### Description  
Check if a string containing `'('`, `')'`, and `'*'` is valid, where `'*'` can be `'('`, `')'`, or empty.

### Core Idea: Range of Possible Opens  
Maintain two counters: `low` = min possible open count, `high` = max possible open count.  
- On `'('`: both increment.  
- On `')'`: both decrement.  
- On `'*'`: `low--` (treat as `')'`), `high++` (treat as `'('`).  
Clamp `low` ≥ 0. If `high` < 0 at any point, invalid. Valid if `low` == 0 at end.

### C++ Snippet  
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
        if (high < 0) return false;   // too many ')'
        low = max(low, 0);            // can't go below 0
    }
    return low == 0;
}
```

Time: O(n)  
Space: O(1)

Advanced Tip:  
- This “range” trick generalizes to other wildcard matching problems.

---

## 🔍 Algorithmic Paradigms  

| Problem                                  | Greedy Pattern                 |
|------------------------------------------|--------------------------------|
| Maximum Subarray                         | Kadane’s local optimum         |
| Jump Game / Jump Game II                 | Furthest reach & layer jumps   |
| Gas Station                             | Net-sum & reset                |
| Hand of Straights                        | Sequential dequeue             |
| Merge Triplets to Form Target            | Coordinate-wise maxima         |
| Partition Labels                         | Interval cut by last indices   |
| Valid Parenthesis String                 | Range tracking                 |

---

## 🚀 Advanced Tips & Practice  

- Validate greedy by proving local choice leads to global optimum.  
- Combine greedy with sorting or precomputation (last indices, counts).  
- When in doubt, test whether a greedy reset or cut discards any valid future solution.  
- Related problems:  
  - “Candy” (distribute in one or two passes)  
  - “Minimum Number of Arrows to Burst Balloons” (interval scheduling)  
  - “Car Fleet” (rear-front grouping)  
  - “Score of Parentheses” (stack/greedy hybrid)  

Happy coding and mastering Greedy patterns!  