<!--markdownlint-disable-->
# 📚 Intervals

This guide covers six classic LeetCode problems under the **Intervals** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Core algorithmic idea  
3. Step‑by‑step approach  
4. A concise C++ snippet with inline comments  
5. Complexity analysis  
6. Advanced tips & related variants  

---

## 1. Insert Interval  
Link: https://leetcode.com/problems/insert-interval/

### Description  
Given a sorted list of non‑overlapping intervals and a new interval, insert it into the list and merge if necessary.

### Core Idea: One‑Pass Merge  
1. Append all intervals ending before the new one.  
2. Merge all overlapping intervals with the new interval by expanding its start/end.  
3. Append the rest.

### C++ Snippet  
```cpp
vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInt) {
    vector<vector<int>> res;
    int i = 0, n = intervals.size();
    // 1) add intervals before newInt
    while (i < n && intervals[i][1] < newInt[0])
        res.push_back(intervals[i++]);
    // 2) merge overlapping
    while (i < n && intervals[i][0] <= newInt[1]) {
        newInt[0] = min(newInt[0], intervals[i][0]);
        newInt[1] = max(newInt[1], intervals[i][1]);
        i++;
    }
    res.push_back(newInt);
    // 3) add remaining
    while (i < n)
        res.push_back(intervals[i++]);
    return res;
}
```

Time: O(n) Space: O(n)

---

## 2. Merge Intervals  
Link: https://leetcode.com/problems/merge-intervals/

### Description  
Given a list of unsorted intervals, merge all overlapping intervals.

### Core Idea: Sort + Sweep  
1. Sort intervals by start time.  
2. Iterate, merging current into the last in result if they overlap.

### C++ Snippet  
```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    if (intervals.empty()) return {};
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> res;
    res.push_back(intervals[0]);
    for (int i = 1; i < intervals.size(); ++i) {
        auto &last = res.back();
        if (intervals[i][0] <= last[1]) {
            // overlap: extend end
            last[1] = max(last[1], intervals[i][1]);
        } else {
            res.push_back(intervals[i]);
        }
    }
    return res;
}
```

Time: O(n log n) Space: O(n)

---

## 3. Non‑Overlapping Intervals  
Link: https://leetcode.com/problems/non-overlapping-intervals/

### Description  
Given a list of intervals, return the minimum number you must remove so that the rest are non‑overlapping.

### Core Idea: Greedy by Earliest End  
1. Sort by end time.  
2. Keep track of last end; for each interval, if start ≥ last end, keep it and update end; else remove it.

### C++ Snippet  
```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    if (intervals.empty()) return 0;
    sort(intervals.begin(), intervals.end(),
         [](auto &a, auto &b){ return a[1] < b[1]; });
    int removed = 0, lastEnd = intervals[0][1];
    for (int i = 1; i < intervals.size(); ++i) {
        if (intervals[i][0] < lastEnd) {
            removed++;
        } else {
            lastEnd = intervals[i][1];
        }
    }
    return removed;
}
```

Time: O(n log n) Space: O(1)

---

## 4. Meeting Rooms  
Link: https://leetcode.com/problems/meeting-rooms/

### Description  
Given an array of meeting time intervals, determine if a single person can attend all meetings (no overlaps).

### Core Idea: Sort + Adjacent Check  
Sort intervals by start time and verify each start ≥ previous end.

### C++ Snippet  
```cpp
bool canAttendMeetings(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    for (int i = 1; i < intervals.size(); ++i) {
        if (intervals[i][0] < intervals[i-1][1])
            return false;
    }
    return true;
}
```

Time: O(n log n) Space: O(1)

---

## 5. Meeting Rooms II  
Link: https://leetcode.com/problems/meeting-rooms-ii/

### Description  
Find the minimum number of conference rooms required to hold all meetings given their intervals.

### Core Idea: Min‑Heap of End Times  
1. Sort intervals by start.  
2. Use a min‑heap to track ongoing meetings’ end times.  
3. For each meeting, pop all ended rooms (`heap.top() ≤ start`), then push current end.  
4. Max heap size = rooms needed.

### C++ Snippet  
```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    if (intervals.empty()) return 0;
    sort(intervals.begin(), intervals.end());
    priority_queue<int, vector<int>, greater<>> pq;
    int rooms = 0;
    for (auto &intv : intervals) {
        while (!pq.empty() && pq.top() <= intv[0])
            pq.pop();
        pq.push(intv[1]);
        rooms = max(rooms, (int)pq.size());
    }
    return rooms;
}
```

Time: O(n log n) Space: O(n)

---

## 6. Minimum Interval to Include Each Query  
Link: https://leetcode.com/problems/minimum-interval-to-include-each-query/

### Description  
Given intervals and queries, for each query find the size of the smallest interval that covers it, or –1.

### Core Idea: Two‑Pointer + Min‑Heap  
1. Sort intervals by start.  
2. Sort queries (with original indices).  
3. Sweep queries in ascending order, pushing intervals whose start ≤ query into a min‑heap keyed by length, and pop those with end < query.  
4. Heap top gives smallest covering interval.

### C++ Snippet  
```cpp
vector<int> minInterval(vector<vector<int>>& intervals, vector<int>& queries) {
    int n = intervals.size(), m = queries.size();
    vector<pair<int,int>> qs(m);
    for (int i = 0; i < m; ++i) qs[i] = {queries[i], i};
    sort(intervals.begin(), intervals.end());
    sort(qs.begin(), qs.end());
    vector<int> ans(m, -1);
    priority_queue<array<int,3>, vector<array<int,3>>, greater<>> pq;
    int i = 0;
    for (auto &[q, idx] : qs) {
        while (i < n && intervals[i][0] <= q) {
            int len = intervals[i][1] - intervals[i][0] + 1;
            pq.push({len, intervals[i][1], intervals[i][0]});
            i++;
        }
        while (!pq.empty() && pq.top()[1] < q)
            pq.pop();
        if (!pq.empty())
            ans[idx] = pq.top()[0];
    }
    return ans;
}
```

Time: O(n log n + m log n) Space: O(n)

---

## 🔍 Algorithmic Paradigms  

| Problem                                | Paradigm                     |
|----------------------------------------|------------------------------|
| Insert Interval                        | One‑pass merge               |
| Merge Intervals                        | Sort + sweep                 |
| Non‑Overlapping Intervals              | Greedy by earliest end       |
| Meeting Rooms                          | Sort + adjacent check        |
| Meeting Rooms II                       | Heap of end times            |
| Minimum Interval to Include Queries    | Sweep line + min‑heap        |

---

## 🚀 Advanced Tips & Practice  

- Always sort intervals by the appropriate key (`start` or `end`).  
- When merging in place, watch for reference vs. copy mistakes.  
- For removal/minimum‑rooms variants, greedy choices on end times are optimal.  
- In sweep‑line with queries, maintain two pointers (intervals and queries) and a heap for active intervals.  
- Related problems:  
  - “Employee Free Time” (multi‑list interval intersection)  
  - “Calendar I/II/III” (dynamic booking, max overlap)  
  - “Range Module” (add/remove/queries intervals)  

Happy coding with intervals!  