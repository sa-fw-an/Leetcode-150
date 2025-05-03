<!--markdownlint-disable-->
# 📚 Intervals (Advanced Beginner‑Friendly Master Guide)

This guide covers six classic LeetCode **Intervals** problems with clear theory, step‑by‑step examples, line‑by‑line code breakdowns, pitfalls, complexity analysis, and advanced tips.

Each section includes:
1. **Theory**: Why this interval technique works.  
2. **Approach Walkthrough**: A concrete example you can trace.  
3. **Line‑by‑Line Code Breakdown**: Detailed commentary on the C++ snippet.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  
5. **Complexity Analysis**: Time and space costs.  
6. **Advanced Tips & Practice**: Further optimizations and related problems.

---

## 1. Insert Interval  
Link: https://leetcode.com/problems/insert-interval/

### Theory: One‑Pass Merge  
When intervals are sorted and non‑overlapping, you can insert a new interval by:
1. Skipping all that end before it.
2. Merging any that overlap by expanding the new interval’s bounds.
3. Appending the rest.

### Approach Walkthrough  
intervals = [[1,3],[6,9]]  
newInt = [2,5]  
- Add [1,3] (ends before merge region?). Actually, [1,3] overlaps → skip to merge step.  
- Merge with [1,3]: start=min(1,2)=1, end=max(3,5)=5 → newInt=[1,5].  
- No more overlaps; append [1,5].  
- Append remaining [6,9] → result [[1,5],[6,9]].

### Code Breakdown  
```cpp
vector<vector<int>> insert(
    vector<vector<int>>& intervals, vector<int>& newInt) {
    vector<vector<int>> res;
    int i = 0, n = intervals.size();
    // 1) Add all intervals ending before newInt starts.
    while (i < n && intervals[i][1] < newInt[0])
        res.push_back(intervals[i++]);
    // 2) Merge any overlapping intervals.
    while (i < n && intervals[i][0] <= newInt[1]) {
        newInt[0] = min(newInt[0], intervals[i][0]);
        newInt[1] = max(newInt[1], intervals[i][1]);
        i++;
    }
    // 3) Insert the merged new interval.
    res.push_back(newInt);
    // 4) Append the rest.
    while (i < n)
        res.push_back(intervals[i++]);
    return res;
}
```
1. The first loop collects all intervals strictly before the new one.  
2. The second loop updates `newInt` to cover all overlapping intervals.  
3. Pushing `newInt` incorporates the entire merged block.  
4. Finally, we append any intervals that start after `newInt`.

Pitfalls & Tips  
- Ensure the comparison in loop one uses `< newInt[0]` and loop two uses `<= newInt[1]`.  
- Working with references avoids extra copies but be cautious when modifying `newInt`.

Complexity Analysis  
- Time: O(n) – a single pass through the list.  
- Space: O(n) – result may contain all original intervals plus one.

Advanced Tips & Practice  
- If intervals are unsorted, sort by start first: O(n log n).  
- Related problems: “Merge Intervals” when no insertion, or dynamic interval trees.

---

## 2. Merge Intervals  
Link: https://leetcode.com/problems/merge-intervals/

### Theory: Sort + Sweep  
By sorting intervals by start time, you only need to compare each interval with the last merged one.

### Approach Walkthrough  
intervals = [[2,6],[1,3],[8,10],[15,18]]  
- Sort → [[1,3],[2,6],[8,10],[15,18]]  
- Merge [1,3] with [2,6] → [1,6]  
- [8,10] doesn't overlap → push  
- [15,18] → push → [[1,6],[8,10],[15,18]]

### Code Breakdown  
```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    if (intervals.empty()) return {};
    // 1) Sort by start time.
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> res;
    res.push_back(intervals[0]);
    for (int i = 1; i < intervals.size(); ++i) {
        auto &last = res.back();
        if (intervals[i][0] <= last[1]) {
            // 2) Overlap: extend end.
            last[1] = max(last[1], intervals[i][1]);
        } else {
            // 3) No overlap: add as new interval.
            res.push_back(intervals[i]);
        }
    }
    return res;
}
```
1. Sorting brings overlapping intervals together.  
2. Compare current start with `last[1]` to decide merge or push.  
3. In-place merge avoids extra space beyond the result.

Pitfalls & Tips  
- Make sure to handle the empty input case.  
- Sorting by default uses start first, then end as tiebreaker.

Complexity Analysis  
- Time: O(n log n) for sorting + O(n) sweep.  
- Space: O(n) for the output.

Advanced Tips & Practice  
- In-place merging (overwrite the input vector) can reduce memory.  
- Related: “Insert Interval” extends this idea to dynamic inserts.

---

## 3. Non‑Overlapping Intervals  
Link: https://leetcode.com/problems/non-overlapping-intervals/

### Theory: Greedy by Earliest End  
To minimize removals, always keep the interval that ends earliest; any interval overlapping it must be removed.

### Approach Walkthrough  
intervals = [[1,2],[2,3],[3,4],[1,3]]  
- Sort by end → [[1,2],[2,3],[3,4],[1,3]]  
- Keep [1,2]. Next [2,3] starts ≥2 → keep.  
- [3,4] ≥3 → keep.  
- [1,3] starts <4 → remove → count=1.

### Code Breakdown  
```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    if (intervals.empty()) return 0;
    // 1) Sort by end time ascending.
    sort(intervals.begin(), intervals.end(),
         [](auto &a, auto &b){ return a[1] < b[1]; });
    int removed = 0;
    int lastEnd = intervals[0][1];
    for (int i = 1; i < intervals.size(); ++i) {
        if (intervals[i][0] < lastEnd) {
            // 2) Overlap: remove current.
            ++removed;
        } else {
            // 3) No overlap: update lastEnd.
            lastEnd = intervals[i][1];
        }
    }
    return removed;
}
```
1. Sorting by the interval’s end time ensures the earliest finishing interval is kept.  
2. If an interval overlaps, remove it (increment `removed`).  
3. Otherwise, move the “end” pointer forward.

Pitfalls & Tips  
- If ties in end times, any tie‑break rule still yields optimal removals.  
- Removing intervals in place can be implemented by skipping pushes.

Complexity Analysis  
- Time: O(n log n) + O(n) sweep.  
- Space: O(1) extra beyond sort.

Advanced Tips & Practice  
- Similar to activity selection / maximum non‑overlapping intervals.  
- Related problem: “Minimum Number of Arrows to Burst Balloons” uses same greedy.

---

## 4. Meeting Rooms  
Link: https://leetcode.com/problems/meeting-rooms/

### Theory: Sort + Adjacent Check  
If you sort by start time, any overlap will appear between adjacent intervals.

### Approach Walkthrough  
intervals = [[0,30],[5,10],[15,20]]  
- Sort → [[0,30],[5,10],[15,20]]  
- Compare [5,10].start < [0,30].end → false (cannot attend all).  

### Code Breakdown  
```cpp
bool canAttendMeetings(vector<vector<int>>& intervals) {
    // 1) Sort by start time.
    sort(intervals.begin(), intervals.end());
    for (int i = 1; i < intervals.size(); ++i) {
        // 2) If current starts before previous ends, overlap.
        if (intervals[i][0] < intervals[i-1][1])
            return false;
    }
    return true;
    // 3) No overlaps found.
}
```

Pitfalls & Tips  
- Sorting default compares first then second elements.  
- Return early on first conflict.

Complexity Analysis  
- Time: O(n log n)  
- Space: O(1)

Advanced Tips & Practice  
- Extend to multiple people by checking resource concurrency.  
- Related: “Calendar I/II/III” for booking queries.

---

## 5. Meeting Rooms II  
Link: https://leetcode.com/problems/meeting-rooms-ii/

### Theory: Min‑Heap of End Times  
Track the end time of each allocated room in a min‑heap; reuse rooms whose meetings have ended.

### Approach Walkthrough  
intervals = [[0,30],[5,10],[15,20]]  
- Sort → same  
- Push 30.  
- [5,10]: 10 < 30? No pop → push 10 → rooms=2.  
- [15,20]: 15 <10? pop 10 → push 20 → rooms=2.  

### Code Breakdown  
```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    if (intervals.empty()) return 0;
    // 1) Sort by start.
    sort(intervals.begin(), intervals.end());
    priority_queue<int, vector<int>, greater<>> pq;
    int rooms = 0;
    for (auto &intv : intervals) {
        // 2) Free all rooms ended by intv[0].
        while (!pq.empty() && pq.top() <= intv[0])
            pq.pop();
        // 3) Allocate room ending at intv[1].
        pq.push(intv[1]);
        rooms = max(rooms, (int)pq.size());
    }
    return rooms;
}
```
1. Sorting ensures earliest meetings are assigned first.  
2. Pop all rooms that have freed up by the current start.  
3. The heap size is the number of rooms in use.

Pitfalls & Tips  
- Use `<=` to free rooms ending exactly at start time.  
- Track maximum heap size, not final size.

Complexity Analysis  
- Time: O(n log n) sort + O(n log n) heap ops  
- Space: O(n) for heap

Advanced Tips & Practice  
- Two‑pointer sweep (separate start/end arrays) can achieve O(n log n) without heap.  
- Related: “Car Fleet” groups by arrival times.

---

## 6. Minimum Interval to Include Each Query  
Link: https://leetcode.com/problems/minimum-interval-to-include-each-query/

### Theory: Two‑Pointer Sweep + Min‑Heap  
Sweep queries in sorted order, add all intervals that start ≤ query to a min‑heap by length, and remove those that end < query.

### Approach Walkthrough  
intervals = [[1,4],[2,4],[3,6]], queries = [2,5]  
- Sort intervals by start and queries by value.  
- q=2: push [1,4],[2,4] → heap=[len=4,…] → top=4.  
- q=5: push [3,6], pop those with end<5 ([1,4],[2,4]) → heap=[len=4] → top=4.

### Code Breakdown  
```cpp
vector<int> minInterval(
    vector<vector<int>>& intervals, vector<int>& queries) {
    int n = intervals.size(), m = queries.size();
    vector<pair<int,int>> qs(m);
    for (int i = 0; i < m; ++i) qs[i] = {queries[i], i};
    sort(intervals.begin(), intervals.end());
    sort(qs.begin(), qs.end());
    vector<int> ans(m, -1);
    // Min‑heap: {length, end, start}
    priority_queue<array<int,3>, vector<array<int,3>>, greater<>> pq;
    int i = 0;
    for (auto &[q, idx] : qs) {
        // 1) Add intervals starting on or before q.
        while (i < n && intervals[i][0] <= q) {
            int len = intervals[i][1] - intervals[i][0] + 1;
            pq.push({len, intervals[i][1], intervals[i][0]});
            i++;
        }
        // 2) Remove intervals ending before q.
        while (!pq.empty() && pq.top()[1] < q)
            pq.pop();
        // 3) Top is smallest covering interval.
        if (!pq.empty()) ans[idx] = pq.top()[0];
    }
    return ans;
}
```
1. Two pointers: one for interval insertion, one for query sweep.  
2. The heap orders intervals by length, then end/start as tie-breakers.  
3. Removing too‑small intervals ensures only valid ones remain.

Pitfalls & Tips  
- Preserve original query order via indices.  
- Tie-breakers in the heap guarantee consistent results.

Complexity Analysis  
- Time: O(n log n + m log n)  
- Space: O(n + m)

Advanced Tips & Practice  
- For static queries, segment trees or binary indexed trees can also solve range queries.  
- Related: “Employee Free Time” for multi‑calendar intersection.

---

## 🔍 Algorithmic Paradigms

| Problem                              | Paradigm                   |
|--------------------------------------|----------------------------|
| Insert Interval                      | One‑pass merge             |
| Merge Intervals                      | Sort + sweep               |
| Non‑Overlapping Intervals            | Greedy by earliest end     |
| Meeting Rooms                        | Sort + adjacent check      |
| Meeting Rooms II                     | Min‑heap of end times      |
| Minimum Interval for Queries         | Sweep line + min‑heap      |

---

## 🚀 Advanced Tips & Related Variants

- Always decide if sorting by start or end is more natural for your goal.  
- In one‑pass merges, be careful with index increments to avoid infinite loops.  
- For heap‑based solutions, choose keys (end time vs length) to match the question.  
- Related problems:  
  - **Employee Free Time** (interval intersection across schedules)  
  - **Calendar I/II/III** (dynamic booking and max overlap)  
  - **Range Module** (add/remove/query intervals)  
  - **Minimum Number of Arrows to Burst Balloons** (interval scheduling)

Happy coding with intervals!  