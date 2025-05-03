<!--markdownlint-disable-->
# 📚 Stack

This guide covers seven classic LeetCode problems under the **Stack** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with detailed inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Valid Parentheses  
Link: https://leetcode.com/problems/valid-parentheses/

### Description  
Given a string containing only the characters `()[]{}`, determine if the input is a valid sequence of open/close brackets.

### Core Idea: Stack for Matching Pairs  
1. Iterate characters one by one.  
2. When you see an opening bracket `(`, `[`, or `{`, push it onto a stack.  
3. On a closing bracket, check if the stack top is the corresponding opening bracket.  
4. If it matches, pop the stack; otherwise the sequence is invalid.  
5. At end, the stack must be empty for a valid string.

### C++ Snippet: Bracket Matching with Stack  
```cpp
// Uses a stack to verify matching parentheses in a string.
bool isValid(const string& s) {
    stack<char> st;  // holds opening brackets
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);               // push any opening bracket
        } else {
            if (st.empty()) return false;  // nothing to match
            char top = st.top();          
            // check matching pair
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{')) {
                return false;
            }
            st.pop();                // valid match → pop
        }
    }
    return st.empty();            // all matched if stack empty
}
```

---

## 2. Min Stack  
Link: https://leetcode.com/problems/min-stack/

### Description  
Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

### Core Idea: Two‑Value Stack or Auxiliary Stack  
- **Approach**: Pair each value with the current minimum so far.  

### C++ Snippet: Single Stack of Pairs  
```cpp
// A stack where each element stores its value and the min up to that point.
class MinStack {
    stack<pair<int,int>> st;  // pair: {value, minSoFar}
public:
    MinStack() {}
    void push(int x) {
        int curMin = st.empty() ? x : min(x, st.top().second);
        // store the value and the updated minimum
        st.emplace(x, curMin);
    }
    void pop() {
        st.pop();                // remove top pair
    }
    int top() {
        return st.top().first;   // return stored value
    }
    int getMin() {
        return st.top().second;  // return stored minSoFar
    }
};
```

---

## 3. Evaluate Reverse Polish Notation  
Link: https://leetcode.com/problems/evaluate-reverse-polish-notation/

### Description  
Evaluate the value of an arithmetic expression in Reverse Polish Notation (RPN).

### Core Idea: Stack for Computation  
1. Iterate tokens.  
2. Push numbers onto a stack.  
3. On an operator, pop two operands, apply the operator, and push the result.  
4. Final stack top is the answer.

### C++ Snippet: RPN Evaluation  
```cpp
// Evaluates an RPN expression using a stack of integers.
int evalRPN(vector<string>& tokens) {
    stack<int> st;
    for (auto& t : tokens) {
        if (t == "+" || t == "-" || t == "*" || t == "/") {
            // pop right then left operand
            int b = st.top(); st.pop();
            int a = st.top(); st.pop();
            int res = 0;
            // apply operator
            if (t == "+") res = a + b;
            else if (t == "-") res = a - b;
            else if (t == "*") res = a * b;
            else res = a / b;      // assume valid non-zero b
            st.push(res);          // push result back
        } else {
            // convert numeric string to int and push
            st.push(stoi(t));
        }
    }
    return st.top();            // final result
}
```

---

## 4. Generate Parentheses  
Link: https://leetcode.com/problems/generate-parentheses/

### Description  
Given `n`, generate all combinations of well‑formed parentheses of length `2n`.

### Core Idea: DFS Backtracking with Constraints  
1. Build strings recursively.  
2. Keep counts `open` and `close` used so far.  
3. You may add `'('` if `open < n`.  
4. You may add `')'` if `close < open`.  
5. When length reaches `2n`, record the string.

### C++ Snippet: Backtracking Generator  
```cpp
// Generates all valid parentheses combinations using DFS + backtracking.
void backtrack(int n, int open, int close,
               string& cur, vector<string>& res) {
    if (cur.size() == 2*n) {
        res.push_back(cur);      // one valid combo complete
        return;
    }
    // try adding '(' if still available
    if (open < n) {
        cur.push_back('(');
        backtrack(n, open+1, close, cur, res);
        cur.pop_back();          // backtrack
    }
    // try adding ')' if it won't invalidate
    if (close < open) {
        cur.push_back(')');
        backtrack(n, open, close+1, cur, res);
        cur.pop_back();          // backtrack
    }
}
vector<string> generateParenthesis(int n) {
    vector<string> res;
    string cur;
    backtrack(n, 0, 0, cur, res);
    return res;
}
```

---

## 5. Daily Temperatures  
Link: https://leetcode.com/problems/daily-temperatures/

### Description  
Given a list of daily temperatures, return a list where each entry tells how many days you’d have to wait until a warmer temperature. Use `0` if none.

### Core Idea: Monotonic Decreasing Stack  
1. Use a stack to store indices of unresolved days, with temperatures in decreasing order.  
2. As you visit day `i`, pop until current temp ≤ previous; for each popped index `j`, answer `i - j`.  
3. Push `i` onto stack.  
4. Unresolved ones remain with `0`.

### C++ Snippet: Next Warmer Day with Stack  
```cpp
// Finds for each day how many days until a warmer temperature.
vector<int> dailyTemperatures(vector<int>& T) {
    int n = T.size();
    vector<int> res(n, 0);
    stack<int> st;  // stores indices, T[st.top()] decreasing
    for (int i = 0; i < n; ++i) {
        // resolve any earlier day colder than today
        while (!st.empty() && T[i] > T[st.top()]) {
            int j = st.top(); st.pop();
            res[j] = i - j;  // days waited
        }
        st.push(i);           // add current day
    }
    return res;
}
```

---

## 6. Car Fleet  
Link: https://leetcode.com/problems/car-fleet/

### Description  
N cars are driving towards a target. Each car has a position and speed. A car catches up to the slower one ahead and forms a fleet. Return the number of fleets that arrive.

### Core Idea: Sort by Position + Stack of Arrival Times  
1. Compute time to reach `target` for each car: `(target - pos) / speed`.  
2. Sort cars by starting position descending (closest to target first).  
3. Traverse sorted list, keep a “stack” of fleet times.  
4. If current car’s time > last fleet time, it forms a new fleet (push time).  
5. Otherwise it joins the previous fleet.

### C++ Snippet: Fleet Counting via Times Stack  
```cpp
// Counts car fleets by comparing arrival times in descending order.
int carFleet(int target, vector<int>& pos, vector<int>& speed) {
    int n = pos.size();
    vector<pair<int,double>> cars(n);
    // build array of {position, timeToTarget}
    for (int i = 0; i < n; ++i) {
        cars[i] = {pos[i], (double)(target - pos[i]) / speed[i]};
    }
    // sort by position descending
    sort(cars.begin(), cars.end(),
         [](auto &a, auto &b){ return a.first > b.first; });
    int fleets = 0;
    double curSlowest = 0.0;
    for (auto& car : cars) {
        double t = car.second;
        // slower (larger time) than any seen so far → new fleet
        if (t > curSlowest) {
            ++fleets;
            curSlowest = t;
        }
    }
    return fleets;
}
```

---

## 7. Largest Rectangle in Histogram  
Link: https://leetcode.com/problems/largest-rectangle-in-histogram/

### Description  
Given histogram bar heights, find the area of the largest rectangle that fits entirely under the histogram.

### Core Idea: Monotonic Increasing Stack  
1. Append a sentinel `0` height to flush stack at end.  
2. Maintain a stack of indices with increasing heights.  
3. For each index `i`, while current bar < bar at stack top, pop height index `h`.  
4. Compute width = `i - stack.top() - 1` after pop (or `i` if stack empty).  
5. Compute area = `height[h] * width`, track max.

### C++ Snippet: Histogram Max Area with Stack  
```cpp
// Computes max rectangle under histogram using a monotonic stack.
int largestRectangleArea(vector<int>& heights) {
    heights.push_back(0);          // sentinel to flush stack
    int n = heights.size();
    stack<int> st;                 // stores indices of increasing bars
    int maxArea = 0;
    for (int i = 0; i < n; ++i) {
        // if current bar shorter, resolve taller bars
        while (!st.empty() && heights[i] < heights[st.top()]) {
            int h = heights[st.top()]; st.pop();
            int left = st.empty() ? -1 : st.top();
            int width = i - left - 1;    // compute width
            maxArea = max(maxArea, h * width);
        }
        st.push(i);               // add current index
    }
    return maxArea;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Print a vector of ints
void printVec(const vector<int>& v) {
    for (int x : v) cout << x << ' ';
    cout << '\n';
}

// Print a vector of strings
void printStrVec(const vector<string>& v) {
    for (auto& s : v) cout << '"' << s << "\" ";
    cout << '\n';
}
```

---

## 🔍 Algorithmic Paradigms  
- **Basic Stack Operations**: Valid Parentheses, Min Stack, RPN  
- **Backtracking/DFS**: Generate Parentheses  
- **Monotonic Stack**: Daily Temperatures, Largest Rectangle in Histogram  
- **Sorting + Stack Logic**: Car Fleet  

### Complexity Summary  
| Problem                           | Time              | Space            |
|-----------------------------------|-------------------|------------------|
| Valid Parentheses                 | O(n)              | O(n)             |
| Min Stack                         | O(1) per op      | O(n)             |
| Evaluate RPN                      | O(n)              | O(n)             |
| Generate Parentheses              | Catalan number    | O(2ⁿ) output     |
| Daily Temperatures                | O(n)              | O(n)             |
| Car Fleet                         | O(n log n)        | O(n)             |
| Largest Rectangle in Histogram    | O(n)              | O(n)             |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Master basic stack push/pop and matching logic.  
- **Medium**: Learn to maintain auxiliary data (min, times, indices) alongside stack.  
- **Hard**: Apply monotonic stacks and backtracking for more complex flows.

### Follow‑Up Problems  
- **Stack Variants**: Min Queue, Max Stack, Sliding Window Maximum  
- **Backtracking**: Parentheses II (with extra pairs), Combination Sum  
- **Monotonic Stack**: Next Greater Element II, Trapping Rain Water II  
- **Fleet & Sorting**: Car Pooling, Queue Reconstruction by Height  

### Common Pitfalls & Debugging  
- Not handling empty‑stack checks before pop/top.  
- Forgetting to backtrack string state in DFS.  
- Off‑by‑one errors in width calculations (histogram).  
- Failing to append sentinel for monotonic-stack flush.  

Happy stacking and coding!  