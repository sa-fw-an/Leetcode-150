<!--markdownlint-disable-->
# 📚 Heap / Priority Queue (Advanced Beginner‑Friendly Master Guide)

This guide covers seven classic LeetCode problems under the **Heap / Priority Queue** topic. We preserve the original structure—problem titles, links, and code blocks—while teaching every concept from first principles. Each section includes:

1. **Theory**: The data structure and algorithmic idea in plain English.  
2. **Approach Walkthrough**: A small example you can trace by hand.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary explaining what each line or block does, how it implements the theory, and highlighting pitfalls.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  

After the problems, you’ll find:

- **Expanded Helper Functions**: Why each helper works, when to use it, and how to adapt it.  
- **🔍 Deep Dive** into core heap paradigms: formal invariants, intuitive justification, and time/space trade‑offs.  
- **🚀 Next Steps to Mastery**: Follow‑up problems (easy→medium→hard), advanced variants or real‑world applications, and a debugging checklist.

---

## 1. Kth Largest Element in a Stream  
Link: https://leetcode.com/problems/kth-largest-element-in-a-stream/

### Theory: Maintaining a Min‑Heap of Size K  
We want to know the kth largest element *so far* as new numbers arrive. A **min‑heap** of the k largest elements lets us do this in O(log k) per insertion. The heap’s top is always the smallest of those k—which is the kth largest overall.

### Approach Walkthrough  
Suppose `k = 3` and we start with stream `[4, 5, 8, 2]`:
1. Add 4 → heap = [4] → size < 3 → no removal. kth largest = 4.  
2. Add 5 → heap = [4,5] → size < 3 → kth largest = 4.  
3. Add 8 → heap = [4,5,8] → size = 3 → kth largest = heap.top() = 4.  
4. Add 2 → 2 < heap.top(4)? Yes → ignore 2 → kth largest remains 4.  
5. Add 9 → 9 > 4 → pop 4, push 9 → heap = [5,8,9] → kth largest = 5.

### Line‑by‑Line Code Breakdown  
```cpp
class KthLargest {
    priority_queue<int, vector<int>, greater<int>> pq; // 1) Min‑heap of ints.
    int k;                                             // 2) Desired rank.

public:
    KthLargest(int k, vector<int>& nums) : k(k) {
        // 3) Initialize: insert existing numbers via add()
        for (int x : nums) {
            add(x);  // reuse the same logic for heap maintenance
        }
    }
    
    int add(int val) {
        if (pq.size() < k) {      // 4) If fewer than k elements so far
            pq.push(val);         //    push directly
        } else if (val > pq.top()) { 
            // 5) If new val is larger than current kth largest
            pq.pop();             //    remove smallest of k
            pq.push(val);         //    push new candidate
        }
        return pq.top();          // 6) The heap top is the kth largest
    }
};
```
Lines explained:
1. Declare a `priority_queue` with `greater<int>` so `pq.top()` is the smallest element.
2. Store `k` for use in `add()`.
3. In the constructor, we insert each initial value via `add()`—this enforces heap size ≤ k.
4. If the heap has fewer than k items, we **always** push.
5. If it has k items and `val` is larger than the smallest among them (`pq.top()`), we pop then push. This keeps the *k* largest.
6. `pq.top()` is the current kth largest.

#### Pitfalls & Tips  
- Forgetting to handle the initial array may give incorrect initial state.  
- Using a max‑heap instead would require size > k and removing `pq.top()`, which flips logic.  
- Always check `pq.size() < k` before comparing to `pq.top()`.

---

## 2. Last Stone Weight  
Link: https://leetcode.com/problems/last-stone-weight/

### Theory: Repeatedly Extract Two Largest via Max‑Heap  
We model stone-smashing by always taking the two heaviest stones. A **max‑heap** (`priority_queue<int>`) lets us remove the two largest in O(log n) time each.

### Approach Walkthrough  
Stones: `[2,7,4,1,8,1]`  
1. Build max‑heap → top elements: 8 and 7 → smash → difference 1 → push 1.  
2. Heap now has `[4,2,1,1,1]` → top two 4 and 2 → smash → push 2.  
3. Continue until zero or one stone remains. Final weight = 1.

### Line‑by‑Line Code Breakdown  
```cpp
int lastStoneWeight(vector<int>& stones) {
    priority_queue<int> pq(stones.begin(), stones.end()); 
    // 1) Build a max‑heap from the array.

    while (pq.size() > 1) {               // 2) Repeat until ≤1 stone left.
        int y = pq.top(); pq.pop();       // 3) Heaviest stone.
        int x = pq.top(); pq.pop();       // 4) Second heaviest.
        if (y > x)                        // 5) If they differ…
            pq.push(y - x);               //    push the remainder.
        // If equal, both are destroyed—do nothing.
    }

    // 6) If empty, return 0; else top is last weight.
    return pq.empty() ? 0 : pq.top();
}
```
Line highlights:
1. Construct heap in O(n) time from the range.  
2. Loop as long as at least two stones exist.  
3–4. Pop top two for smashing.  
5. Only push the difference if nonzero.  
6. Handle empty heap (all stones destroyed).

#### Pitfalls & Tips  
- Pushing differences can increase heap size if done incorrectly; ensure you only push when `y > x`.  
- For large n, repeated pops/pushes cost O(n log n) worst‑case.

---

## 3. K Closest Points to Origin  
Link: https://leetcode.com/problems/k-closest-points-to-origin/

### Theory: Max‑Heap of Size K on Squared Distance  
To find the k closest points, maintain a **max‑heap** of size k where each entry is `(distance, index)`. If a new point's distance is smaller than the heap’s top, replace the top. Finally, the heap contains the k nearest.

### Approach Walkthrough  
Points `[(1,3), ( -2,2), (5,8 ), (0,1)]`, `k = 2`:
1. Add (1,3): dist=10 → heap=[(10,0)].  
2. Add (-2,2): dist=8 → heap=[(10,0),(8,1)].  
3. Add (5,8): dist=89 > top(10)? Yes → pop (10), push (89) → heap=[(89,2),(8,1)].  
4. Add (0,1): dist=1 < top(89)? Pop 89, push 1 → heap=[(8,1),(1,3)].  
5. Result points are indices 1 and 3.

### Line‑by‑Line Code Breakdown  
```cpp
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    priority_queue<pair<int,int>> pq;   // 1) Max‑heap of (dist, index).

    for (int i = 0; i < points.size(); ++i) {
        int x = points[i][0], y = points[i][1];
        int dist = x*x + y*y;           // 2) Squared distance to (0,0).

        if (pq.size() < k) {            // 3) Fill heap until size k.
            pq.push({dist, i});
        } else if (dist < pq.top().first) {
            // 4) Found a closer point
            pq.pop();                   //    remove farthest among current k
            pq.push({dist, i});         //    add new closer point
        }
    }

    vector<vector<int>> result;         // 5) Extract k closest points.
    while (!pq.empty()) {
        result.push_back(points[pq.top().second]);
        pq.pop();
    }
    return result;
}
```
Explanations:
1. We store `(distance, index)` because C++ max‑heap orders by `first` by default.  
2. Use squared distance to avoid expensive `sqrt`.  
3–4. Maintain heap size ≤ k. Replace the largest when a smaller distance is found.  
5. Pop all to collect result in any order.

#### Pitfalls & Tips  
- Forgetting to compare `dist < pq.top().first` yields incorrect replacements.  
- Using Euclidean distance with `sqrt` is unnecessary and slower.

---

## 4. Kth Largest Element in an Array  
Link: https://leetcode.com/problems/kth-largest-element-in-an-array/

### Theory: Two Approaches  
- **Min‑Heap Approach**: Similar to stream approach but on a static array.  
- **Quickselect Approach**: Partition-based selection in average O(n).

### Heap Approach Walkthrough  
Array `nums = [3,2,1,5,6,4]`, `k = 2`:
1. Push 3,2 → heap=[2,3];  
2. Push 1 → heap=[1,3,2] then pop→heap=[2,3];  
3. Push 5 → heap=[2,3,5] pop→heap=[3,5];  
4. Push 6 → heap=[3,5,6] pop→heap=[5,6];  
5. Push 4 → heap=[4,6,5] pop→heap=[5,6];  
6. Top=5 is the 2nd largest.

### Heap Approach Code Breakdown  
```cpp
int findKthLargest(vector<int>& nums, int k) {
    priority_queue<int, vector<int>, greater<int>> pq; 
    // 1) Min‑heap of k largest elements

    for (int num : nums) {
        pq.push(num);                  
        if (pq.size() > k)             // 2) Keep only k elements
            pq.pop();                  //    remove smallest
    }
    return pq.top();                  // 3) Top is kth largest
}
```

### Quickselect Approach Walkthrough  
We partition around a pivot so that elements ≤ pivot go left, > pivot go right. We then recurse into the side containing the kth largest.

### Quickselect Code Breakdown  
```cpp
int findKthLargest(vector<int>& nums, int k) {
    int target = nums.size() - k;     // 1) Convert to index of kth smallest
    return quickSelect(nums, 0, nums.size() - 1, target);
}

int quickSelect(vector<int>& nums, int l, int r, int k) {
    int pivot = nums[r], p = l;
    // 2) Partition: elements ≤ pivot to left
    for (int i = l; i < r; ++i) {
        if (nums[i] <= pivot) {
            swap(nums[i], nums[p++]);
        }
    }
    swap(nums[p], nums[r]);            // 3) Place pivot at correct spot
    
    if (p < k) return quickSelect(nums, p + 1, r, k);
    if (p > k) return quickSelect(nums, l, p - 1, k);
    return nums[p];                    // 4) Found target
}
```
Explanations:
1. `target` is 0‑based position for kth largest.  
2. Partition step is O(n) average.  
3. Pivot ends at index `p`.  
4. Recurse into correct half or return.

#### Pitfalls & Tips  
- Quickselect worst‑case O(n²) on sorted inputs—randomize pivot to avoid.  
- Converting `k` correctly to index is crucial.

---

## 5. Task Scheduler  
Link: https://leetcode.com/problems/task-scheduler/

### Theory: Greedy Scheduling with Max‑Heap and Cooldown Queue  
We repeatedly execute the most frequent available tasks, respecting a cooldown period `n`. A **max‑heap** orders tasks by remaining count. A **queue** holds tasks in cooldown until they become available again.

### Approach Walkthrough  
Tasks `["A","A","A","B","B","B"]`, `n=2`:
1. Count: A:3, B:3 → heap=[3A,3B].  
2. Time 1: run A→A:2→cooldown of A available at t=1+2=3.  
3. Time 2: run B→B:2→cool at 4.  
4. Time 3: A off cooldown (2) and B waiting; schedule A→1→cool4.  
5. Continue until all done. Idle slots inserted if heap empty but cooldown not ready.

### Line‑by‑Line Code Breakdown  
```cpp
int leastInterval(vector<char>& tasks, int n) {
    vector<int> freq(26, 0);
    for (char c : tasks) freq[c - 'A']++;  
    // 1) Count each task’s frequency.

    priority_queue<int> pq;
    for (int f : freq) if (f > 0) pq.push(f);  
    // 2) Max‑heap of frequencies.

    int time = 0;
    queue<pair<int,int>> cooling;      // {remaining, available_time}

    while (!pq.empty() || !cooling.empty()) {
        time++;                        // 3) Advance time slot.

        if (!pq.empty()) {
            int f = pq.top() - 1;     // 4) Execute one instance.
            pq.pop();
            if (f > 0)
                cooling.push({f, time + n}); 
            // 5) Put on cooldown until time+n.
        }

        if (!cooling.empty() && cooling.front().second == time) {
            // 6) Task’s cooldown ended; reinsert.
            pq.push(cooling.front().first);
            cooling.pop();
        }
    }

    return time;                      // 7) Total time including idles.
}
```
Line points:
1. Build frequency array in O(26).  
2. Initialize max‑heap.  
3. Each loop represents one time unit.  
4. Process highest‑frequency task.  
5. If more of that task remains, schedule it back after cooldown.  
6. When tasks come off cooldown, reinsert.  
7. `time` counts both work and idle slots.

#### Pitfalls & Tips  
- Idle intervals when `pq` is empty must still advance `time`.  
- Ensure cooldown queue uses correct `available_time = time + n`.

---

## 6. Design Twitter  
Link: https://leetcode.com/problems/design-twitter/

### Theory: Merge K Sorted Tweet Streams with a Heap  
Each user’s tweets form a time‑descending list. To build a news feed, we merge multiple lists (the user’s followees) by timestamp using a min‑ or max‑heap.

### Approach Walkthrough  
User 1 follows [2,1]. Tweets:
- 1: timings [t0→tweetA], [t2→tweetC]  
- 2: timings [t1→tweetB]  
Use a heap to always pick the highest timestamp among heads.

### Line‑by‑Line Code Breakdown  
```cpp
class Twitter {
    int timestamp = 0;  
    unordered_map<int, vector<pair<int,int>>> tweets;
    unordered_map<int, unordered_set<int>> follows;

public:
    void postTweet(int userId, int tweetId) {
        // 1) Append with current timestamp, then increment.
        tweets[userId].push_back({timestamp++, tweetId});
    }

    vector<int> getNewsFeed(int userId) {
        follows[userId].insert(userId);  
        // 2) Ensure user sees own tweets.

        // 3) Max‑heap: (time, tweetId, userIndex)
        priority_queue<tuple<int,int,int>> pq;
        vector<pair<int,int>> idx;  // {userId, nextIndex}

        // 4) Initialize heap with each followee’s latest tweet.
        for (int f : follows[userId]) {
            auto& vec = tweets[f];
            if (!vec.empty()) {
                int i = vec.size() - 1;
                pq.push({vec[i].first, vec[i].second, idx.size()});
                idx.push_back({f, i - 1});
            }
        }

        vector<int> feed;
        // 5) Pop up to 10 tweets.
        while (!pq.empty() && feed.size() < 10) {
            auto [time, tid, uidx] = pq.top(); pq.pop();
            feed.push_back(tid);
            auto [uid, i] = idx[uidx];
            if (i >= 0) {
                auto& vec = tweets[uid];
                pq.push({vec[i].first, vec[i].second, uidx});
                idx[uidx].second--;
            }
        }
        return feed;
    }

    void follow(int a, int b) { follows[a].insert(b); }  
    void unfollow(int a, int b) { if (a != b) follows[a].erase(b); }
};
```
Highlights:
1. We timestamp tweets for global ordering.  
2. Each user implicitly follows themselves.  
3–4. We merge multiple streams by pushing heads into a heap.  
5. Extract top 10 by timestamp, then push next from that user.

#### Pitfalls & Tips  
- Forgetting to insert the user into their own follow list hides self tweets.  
- Keep parallel `idx` vector to track each user’s next tweet index.

---

## 7. Find Median from Data Stream  
Link: https://leetcode.com/problems/find-median-from-data-stream/

### Theory: Two‑Heap Balance Maintains Median  
We keep two heaps:
- `small` (max‑heap) holds the lower half
- `large` (min‑heap) holds the upper half  
We rebalance so their sizes differ by at most one. The median is either one heap’s top or the average of both.

### Approach Walkthrough  
Insert `[1,2,3]`:
1. Add 1 → small=[1], large=[] → median=1.  
2. Add 2 → small=[1], large=[2] → median=(1+2)/2=1.5.  
3. Add 3 → small=[2,1], large=[3] → small has 2 vs large 1 → median=2.

### Line‑by‑Line Code Breakdown  
```cpp
class MedianFinder {
    priority_queue<int> small;                                // 1) Max‑heap.
    priority_queue<int, vector<int>, greater<int>> large;     // 2) Min‑heap.

public:
    void addNum(int num) {
        small.push(num);                                      // 3) Default push to small.
        if (!large.empty() && small.top() > large.top()) {    
            // 4) Fix order if max of small > min of large.
            large.push(small.top()); small.pop();
        }
        // 5) Balance sizes
        if (small.size() > large.size() + 1) {
            large.push(small.top()); small.pop();
        } else if (large.size() > small.size() + 1) {
            small.push(large.top()); large.pop();
        }
    }

    double findMedian() {
        if (small.size() > large.size()) 
            return small.top();                              // 6a
        if (large.size() > small.size()) 
            return large.top();                              // 6b
        return (small.top() + large.top()) / 2.0;            // 6c
    }
};
```
Line notes:
1. `small` stores lower half; top is maximum of lower half.  
2. `large` stores upper half; top is minimum of upper half.  
3. Insertion always starts in `small`.  
4. If ordering property violated, swap tops.  
5. Keep size difference ≤ 1.  
6. Median depends on which heap is larger or both equal.

#### Pitfalls & Tips  
- Always rebalance after potential cross‑heap violation.  
- Beware integer vs double division when averaging.

---

## 🔧 Expanded Helper Functions  

```cpp
// Print and clear a min‑heap (destructively)
template<typename T>
void printMinHeap(priority_queue<T, vector<T>, greater<T>> pq) {
    cout << "MinHeap: ";
    while (!pq.empty()) {
        cout << pq.top() << ' ';
        pq.pop();
    }
    cout << '\n';
}

// Print and clear a max‑heap (destructively)
template<typename T>
void printMaxHeap(priority_queue<T> pq) {
    cout << "MaxHeap: ";
    while (!pq.empty()) {
        cout << pq.top() << ' ';
        pq.pop();
    }
    cout << '\n';
}

// Build a min‑heap from a vector in O(n)
template<typename T>
priority_queue<T, vector<T>, greater<T>> buildMinHeap(const vector<T>& v) {
    // Directly uses range constructor
    return priority_queue<T, vector<T>, greater<T>>(v.begin(), v.end());
}

// Build a max‑heap from a vector in O(n)
template<typename T>
priority_queue<T> buildMaxHeap(const vector<T>& v) {
    return priority_queue<T>(v.begin(), v.end());
}
```
- **Why it works**: Uses the heap property and C++’s range constructor.  
- **When to use**: Debug the contents of a heap or quickly build from existing data.  
- **Adaptation**: Change `greater<T>` to custom comparator for complex keys.

---

## 🔍 Deep Dive into Paradigms

### 1. K‑Selection (Min‑Heap of K Largest)
- **Key Invariant**: Heap always contains the k largest elements seen.  
- **Proof Sketch**: On each insert, if size ≤ k we keep all; if size > k we discard the smallest, so only top k remain.  
- **Time**: O(n log k), **Space**: O(k).

### 2. Merge and Repeated Extraction (Multi‑way / Pairwise)
- **Key Idea**: Always extract the next largest/smallest from a heap of streams.  
- **Invariant**: Heap contains the current candidate from each stream.  
- **Time**: O(N log k) for merging k streams of total length N.

### 3. Greedy Scheduling with Cooldown
- **Key Invariant**: Always execute the most frequent available task to minimize idle time.  
- **Intuition**: Scheduling high‑frequency tasks first reduces future idle gaps.  
- **Time**: O(T log M) where M≤26, **Space**: O(M).

### 4. Two‑Heap Balancing for Median
- **Key Invariant**: Size difference ≤ 1; all `small` ≤ all `large`.  
- **Justification**: Ensures median is at one or both heap tops.  
- **Time**: O(log N) per op, **Space**: O(N).

### 5. Custom Comparators
- **Key Idea**: Overload ordering to heapify complex objects (pairs, tuples).  
- **Use**: Prioritize by first element, fallback to second, etc.  
- **Trade‑off**: Comparator cost adds constant overhead per operation.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Easy → Medium → Hard**  
  - Top K Frequent Elements → Top K Frequent Words → Sliding Window Maximum  
  - Sort Nearly Sorted Array → Merge K Sorted Arrays → Kth Smallest in a Multiplication Table  
  - Reorganize String → Task Scheduler II (with different rules) → CPU Task Scheduling with Cooldowns  

### Advanced Variants & Real‑World Applications  
- Real‑time leaderboard merging via heaps.  
- External sorting of massive datasets using K‑way merge.  
- Event scheduling with variable cooldown windows.  
- Median maintenance in streaming analytics or financial tick data.

### Debugging Tips & Mental Checklist  
1. **Heap Type**: Confirm whether you need a **min‑heap** or **max‑heap**.  
2. **Size Invariant**: After every push/pop, verify heap size constraints.  
3. **Comparator Logic**: For custom types, test comparator ordering on edge cases.  
4. **Balance**: In two‑heap solutions, always rebalance after each insertion.  
5. **Edge Cases**: Check empty inputs, `k=0`, large `n`, duplicate values.

With these tools and patterns, you’re ready to tackle any heap or priority queue challenge. Happy coding!  