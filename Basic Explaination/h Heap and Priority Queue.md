<!--markdownlint-disable-->
# 📚 Heap / Priority Queue

This guide covers seven classic LeetCode problems under the **Heap / Priority Queue** topic. For each problem, you'll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Kth Largest Element in a Stream  
Link: https://leetcode.com/problems/kth-largest-element-in-a-stream/

### Description  
Design a class that maintains the kth largest element in a stream of integers.

### Core Idea: Min‑Heap of K Largest Elements  
1. Maintain a min‑heap of at most `k` elements.  
2. When adding a new element:  
   - If heap size < k, add directly.  
   - If value > heap.top(), replace top with new element.  
3. The heap top always contains the kth largest element.

### C++ Code Snippet: Stream Min‑Heap  
```cpp
// Efficiently maintains the kth largest element in a stream.
class KthLargest {
    priority_queue<int, vector<int>, greater<int>> pq; // min-heap
    int k;
public:
    KthLargest(int k, vector<int>& nums) : k(k) {
        // Initialize with existing elements
        for (int x : nums) {
            add(x);  // use existing add logic
        }
    }
    
    int add(int val) {
        if (pq.size() < k) {
            // Heap not full yet, add directly
            pq.push(val);
        } else if (val > pq.top()) {
            // New element larger than kth largest
            pq.pop();        // remove smallest element
            pq.push(val);    // add new element
        }
        return pq.top();     // top is kth largest
    }
};
```

---

## 2. Last Stone Weight  
Link: https://leetcode.com/problems/last-stone-weight/

### Description  
Stone weights are given in an array. Each turn, take two heaviest stones and smash them. If weights are equal, both are destroyed. If different, smaller is destroyed and larger becomes `y-x`. Return weight of last stone or 0 if all destroyed.

### Core Idea: Max‑Heap for Largest Elements  
1. Insert all weights into a max‑heap.  
2. While heap has at least 2 elements:  
   - Extract two largest weights.  
   - If different, push back difference.  
3. Return top of heap if nonempty, else 0.

### C++ Code Snippet: Smash Simulation with Heap  
```cpp
// Simulates stone smashing using a max heap.
int lastStoneWeight(vector<int>& stones) {
    // max-heap is default in C++ priority_queue
    priority_queue<int> pq(stones.begin(), stones.end());
    
    while (pq.size() > 1) {
        // Get heaviest stone
        int y = pq.top(); pq.pop();
        // Get second heaviest
        int x = pq.top(); pq.pop();
        
        // If weights differ, push back the difference
        if (y > x) {
            pq.push(y - x);
        }
        // If equal, both destroyed (do nothing)
    }
    
    // Return last stone or 0 if none left
    return pq.empty() ? 0 : pq.top();
}
```

---

## 3. K Closest Points to Origin  
Link: https://leetcode.com/problems/k-closest-points-to-origin/

### Description  
Given an array of points in a 2D plane, find the `k` closest points to the origin (0,0).

### Core Idea: Max‑Heap of K Closest  
1. Maintain a max‑heap of at most `k` points, ordered by distance.  
2. For each point, compute squared distance.  
3. If heap size < k, add.  
4. Else if distance < heap.top(), replace top.  
5. Extract all points from heap.

### C++ Code Snippet: K Closest with Max‑Heap  
```cpp
// Finds k closest points using a max-heap.
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    // max-heap of {distance, i} pairs
    priority_queue<pair<int, int>> pq;
    
    for (int i = 0; i < points.size(); ++i) {
        // Calculate squared Euclidean distance
        int dist = points[i][0] * points[i][0] + 
                   points[i][1] * points[i][1];
        
        if (pq.size() < k) {
            // Heap not full yet, add directly
            pq.push({dist, i});
        } else if (dist < pq.top().first) {
            // Found closer point than current kth closest
            pq.pop();
            pq.push({dist, i});
        }
    }
    
    // Extract results in any order (they're all top k)
    vector<vector<int>> result;
    while (!pq.empty()) {
        result.push_back(points[pq.top().second]);
        pq.pop();
    }
    
    return result;
}
```

---

## 4. Kth Largest Element in an Array  
Link: https://leetcode.com/problems/kth-largest-element-in-an-array/

### Description  
Find the kth largest element in an unsorted array.

### Core Idea: Min‑Heap of K Largest (or Quick Select)  
1. **Heap Approach**: Keep min‑heap of k largest elements.  
2. **Quick Select**: Partition array like quicksort, focus on relevant half.

### C++ Code Snippet: Heap Approach  
```cpp
// Finds kth largest using min-heap of k elements.
int findKthLargest(vector<int>& nums, int k) {
    // min-heap to keep k largest elements
    priority_queue<int, vector<int>, greater<int>> pq;
    
    for (int num : nums) {
        pq.push(num);        // add current
        if (pq.size() > k) {
            pq.pop();        // remove smallest if overflow
        }
    }
    
    return pq.top();         // top is kth largest
}
```

### C++ Code Snippet: Quick Select Approach  
```cpp
// Finds kth largest using quickselect with O(n) average.
int findKthLargest(vector<int>& nums, int k) {
    k = nums.size() - k;     // convert to 0-indexed from small
    return quickSelect(nums, 0, nums.size() - 1, k);
}

int quickSelect(vector<int>& nums, int l, int r, int k) {
    // partition around pivot (rightmost element)
    int pivot = nums[r], p = l;
    for (int i = l; i < r; ++i) {
        if (nums[i] <= pivot) {
            swap(nums[i], nums[p++]);
        }
    }
    swap(nums[p], nums[r]);  // put pivot in final position
    
    if (p < k) return quickSelect(nums, p + 1, r, k);
    if (p > k) return quickSelect(nums, l, p - 1, k);
    return nums[p];          // found kth smallest
}
```

---

## 5. Task Scheduler  
Link: https://leetcode.com/problems/task-scheduler/

### Description  
Given a sequence of tasks and a cooldown period `n`, determine the minimum time to complete all tasks, where each task takes 1 unit and same-type tasks must be separated by at least `n` units.

### Core Idea: Greedy Scheduling with Frequency Heap  
1. Count frequency of each task.  
2. Use max‑heap to prioritize most frequent tasks.  
3. For each time slot, try to schedule the most frequent tasks that are off cooldown.  
4. Keep a queue of [task, available_time] for tasks on cooldown.

### C++ Code Snippet: Task Scheduling with Heap  
```cpp
// Schedules tasks with cooldown using max-heap and queue.
int leastInterval(vector<char>& tasks, int n) {
    // Count frequency of each task
    vector<int> freq(26, 0);
    for (char c : tasks) {
        freq[c - 'A']++;
    }
    
    // max-heap prioritizes tasks by frequency
    priority_queue<int> pq;
    for (int f : freq) {
        if (f > 0) pq.push(f);
    }
    
    int time = 0;
    queue<pair<int, int>> cooling; // {freq, time_available}
    
    while (!pq.empty() || !cooling.empty()) {
        time++;
        
        if (!pq.empty()) {
            int f = pq.top() - 1; // process one instance
            pq.pop();
            
            if (f > 0) {
                cooling.push({f, time + n}); // put on cooldown
            }
        }
        
        // Check if any tasks are off cooldown
        if (!cooling.empty() && cooling.front().second <= time) {
            pq.push(cooling.front().first);
            cooling.pop();
        }
    }
    
    return time;
}
```

---

## 6. Design Twitter  
Link: https://leetcode.com/problems/design-twitter/

### Description  
Design a simplified Twitter interface to follow/unfollow users and see news feeds (10 most recent tweets from followed users).

### Core Idea: Heaps for Merging Tweet Streams  
1. Store tweets with timestamps in user-specific lists.  
2. Store followee sets for each user.  
3. For news feed, use a max‑heap to merge tweet streams from all followees, sorted by timestamp.

### C++ Code Snippet: Twitter Design  
```cpp
// Implements a simplified Twitter with follow and newsfeed.
class Twitter {
    int timestamp;
    // user_id -> vector of {time, tweet_id} pairs
    unordered_map<int, vector<pair<int, int>>> tweets;
    // user_id -> set of followed user_ids
    unordered_map<int, unordered_set<int>> follows;
    
public:
    Twitter() : timestamp(0) {}
    
    void postTweet(int userId, int tweetId) {
        tweets[userId].push_back({timestamp++, tweetId});
    }
    
    vector<int> getNewsFeed(int userId) {
        // Follow yourself to see your own tweets
        follows[userId].insert(userId);
        
        // max-heap to merge tweet streams by timestamp
        priority_queue<tuple<int, int, int>> pq; // {time, tweetId, userIdx}
        
        // Maps to track current index in each user's tweet list
        vector<pair<int, int>> userIdxPairs;
        
        // Initialize with latest tweet from each followed user
        for (int followeeId : follows[userId]) {
            if (!tweets[followeeId].empty()) {
                auto& userTweets = tweets[followeeId];
                int idx = userTweets.size() - 1;
                pq.push({userTweets[idx].first, 
                         userTweets[idx].second, 
                         userIdxPairs.size()});
                userIdxPairs.push_back({followeeId, idx - 1});
            }
        }
        
        // Extract top 10 tweets
        vector<int> feed;
        while (!pq.empty() && feed.size() < 10) {
            auto [time, tweetId, userIdx] = pq.top(); pq.pop();
            feed.push_back(tweetId);
            
            // Add next tweet from this user if exists
            auto [userId, idx] = userIdxPairs[userIdx];
            if (idx >= 0) {
                auto& userTweets = tweets[userId];
                pq.push({userTweets[idx].first, 
                        userTweets[idx].second, 
                        userIdx});
                userIdxPairs[userIdx].second--;
            }
        }
        
        return feed;
    }
    
    void follow(int followerId, int followeeId) {
        follows[followerId].insert(followeeId);
    }
    
    void unfollow(int followerId, int followeeId) {
        if (followerId != followeeId) { // can't unfollow yourself
            follows[followerId].erase(followeeId);
        }
    }
};
```

---

## 7. Find Median from Data Stream  
Link: https://leetcode.com/problems/find-median-from-data-stream/

### Description  
Design a data structure that supports adding integers and finding the median efficiently.

### Core Idea: Two Heaps (Small & Large)  
1. Maintain two heaps:  
   - `small` = max‑heap of elements smaller than median
   - `large` = min‑heap of elements larger than median
2. Balance the heaps such that their sizes differ by at most 1.  
3. Median is:  
   - Top of larger heap, if sizes differ
   - Average of tops, if sizes equal

### C++ Code Snippet: Median with Two Heaps  
```cpp
// Maintains running median using two balanced heaps.
class MedianFinder {
    // max-heap for smaller half of elements
    priority_queue<int> small;
    // min-heap for larger half of elements
    priority_queue<int, vector<int>, greater<int>> large;
    
public:
    MedianFinder() {}
    
    void addNum(int num) {
        // Default: add to small heap
        small.push(num);
        
        // Ensure small's top is smaller than large's top
        if (!small.empty() && !large.empty() && 
            small.top() > large.top()) {
            // Fix heap property by moving largest small to large
            large.push(small.top());
            small.pop();
        }
        
        // Balance heap sizes (may differ by at most 1)
        if (small.size() > large.size() + 1) {
            large.push(small.top());
            small.pop();
        } else if (large.size() > small.size() + 1) {
            small.push(large.top());
            large.pop();
        }
    }
    
    double findMedian() {
        if (small.size() > large.size()) {
            return small.top();
        } else if (large.size() > small.size()) {
            return large.top();
        } else {
            // Equal sizes, average the two middle values
            return (small.top() + large.top()) / 2.0;
        }
    }
};
```

---

## 🔧 Common Helper Functions  
```cpp
// Print a min-heap (destructively)
template <typename T, typename Container=vector<T>, typename Compare=greater<T>>
void printMinHeap(priority_queue<T, Container, Compare> pq) {
    cout << "Min-Heap: ";
    while (!pq.empty()) {
        cout << pq.top() << " ";
        pq.pop();
    }
    cout << endl;
}

// Print a max-heap (destructively)
template <typename T>
void printMaxHeap(priority_queue<T> pq) {
    cout << "Max-Heap: ";
    while (!pq.empty()) {
        cout << pq.top() << " ";
        pq.pop();
    }
    cout << endl;
}

// Build a min-heap from vector
template <typename T>
priority_queue<T, vector<T>, greater<T>> buildMinHeap(const vector<T>& v) {
    return priority_queue<T, vector<T>, greater<T>>(v.begin(), v.end());
}
```

---

## 🔍 Algorithmic Paradigms  
- **K-Selection**: Keep a size-k heap to find kth element.  
- **Multi-way Merge**: Use a heap to merge multiple sorted streams.  
- **Greedy Scheduling**: Prioritize based on frequency/deadline.  
- **Balancing Heaps**: Maintain state across a median.  
- **Custom Comparators**: Order elements by composite logic.  

### Complexity Summary  
| Problem                                | Time                  | Space              |
|----------------------------------------|-----------------------|--------------------|
| Kth Largest in Stream                 | O(log k) per add     | O(k)               |
| Last Stone Weight                     | O(n log n)            | O(n)               |
| K Closest Points                      | O(n log k)            | O(k)               |
| Kth Largest Element (Heap)            | O(n log k)            | O(k)               |
| Kth Largest Element (QuickSelect)     | O(n) average          | O(1)              |
| Task Scheduler                        | O(t log 26)           | O(26)              |
| Design Twitter                        | O(f log f) per feed   | O(u + t)           |
| Find Median from Data Stream          | O(log n) per op       | O(n)               |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Identify when to use min-heap vs. max-heap.  
- **Medium**: Optimize K-selection with heap maintenance to avoid sorting all elements.  
- **Hard**: Combine heaps with other data structures for complex operations (Twitter, Median).

### Follow‑Up Problems  
- Top K Frequent Elements/Words  
- Sort a Nearly Sorted Array  
- Minimum Cost to Connect Sticks  
- Reorganize String  
- Meeting Rooms II  
- Swim in Rising Water  

### Common Pitfalls & Debugging  
- Forgetting to balance heaps in median finding.  
- Using the wrong heap type (min vs. max) for the problem.  
- Not handling edge cases (empty heaps, k=1, etc.).  
- Inefficient pushes/pops when maintaining a k-sized heap.  
- Missing optimizations in custom heap comparators.  

Happy coding with Heaps & Priority Queues!  