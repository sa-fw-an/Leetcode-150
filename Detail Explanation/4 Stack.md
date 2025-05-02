<!--markdownlint-disable-->
# 📚 Stack (Advanced Beginner‑Friendly Master Guide)

Welcome to your master guide on **Stack** problems. We’ll preserve the original structure—problem titles, links, and code blocks—while teaching each concept from first principles. For every problem you will find:

1. **Theory**: What is happening under the hood and why a stack is the right tool.  
2. **Approach Walkthrough**: A concrete example you can trace by hand.  
3. **Detailed Code Breakdown**: Every line explained in plain English.  
4. **Pitfalls & Tips**: Common mistakes and how to avoid them.  

After all problems, you’ll also get:

- **Expanded Helper Functions**: Why they work, how to adapt them.  
- **Deep Dive** into stack‑related paradigms.  
- **Next Steps to Mastery**: Follow‑up problems, advanced variants, real‑world uses, and debugging checklists.

Let’s build your stack skills from the ground up!

---

## 1. Valid Parentheses  
Link: https://leetcode.com/problems/valid-parentheses/

### Theory: Matching Pairs with a Stack  
A **stack** is a Last‑In‑First‑Out structure—think of plates stacked in your cupboard. When you see an opening bracket (`(`, `[`, `{`), you “push” it on top. When you see a closing bracket, you must “pop” the most recent opening bracket and check it matches. If it doesn’t, or the stack is empty, the sequence is invalid.

### Approach Walkthrough  
Let’s test `s = "{[()]}"`:

1. Read `{` → opening → push `'{` → stack: [`{`].  
2. Read `[` → push `[` → stack: [`{`, `[`].  
3. Read `(` → push `(` → stack: [`{`, `[`, `(`].  
4. Read `)` → closing → pop top (`(`). Do they match? Yes. → stack: [`{`, `[`].  
5. Read `]` → pop top (`[`). Match? Yes. → stack: [`{`].  
6. Read `}` → pop top (`{`). Match? Yes. → stack: [].  
7. End of string: stack is empty → valid.

### Detailed Code Breakdown  
```cpp
bool isValid(const string& s) {
    stack<char> st;                     // 1) Create empty stack of chars.
    for (char c : s) {                  // 2) Process each character in the string.
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);                 // 3) Opening bracket: push onto stack.
        } else {
            if (st.empty())             // 4) Nothing to match against?
                return false;           //    → invalid.
            char top = st.top();        // 5) Peek the most recent opening bracket.
            // 6) Check if it matches the current closing bracket.
            if ((c == ')' && top != '(') ||
                (c == ']' && top != '[') ||
                (c == '}' && top != '{'))
                return false;           //    → mismatch → invalid.
            st.pop();                   // 7) Valid match → remove opening from stack.
        }
    }
    return st.empty();                  // 8) True only if all opens were closed.
}
```
- Line 1: `stack<char> st;` initializes an empty stack.  
- Line 2: We loop through each character.  
- Lines 3–4: Opening vs. closing logic.  
- Line 4: `st.empty()` guard prevents popping from an empty stack.  
- Lines 5–6: Match check between opening and closing.  
- Line 7: `st.pop()` removes a matched opening bracket.  
- Line 8: Stack must be empty at end for a valid sequence.  

#### Pitfalls & Tips  
- Always check `st.empty()` before calling `top()` or `pop()`.  
- Forgetting to pop after a match leads to leftover items and false negatives.  
- Be careful to match each type of bracket exactly.

---

## 2. Min Stack  
Link: https://leetcode.com/problems/min-stack/

### Theory: Tracking the Running Minimum  
We need a stack that can return the **minimum** element in constant time. The trick is to store, alongside each value, the minimum value **so far** at that point in the stack. When you push, compute the new min; when you pop, simply discard that layer and the prior min restores itself.

### Approach Walkthrough  
Operations on sequence: push(3), push(5), getMin(), push(2), push(1), getMin(), pop(), getMin():

1. push(3): stack holds [(3,3)] → minSoFar=3.  
2. push(5): stack holds [(3,3),(5,3)] → minSoFar still 3.  
3. getMin() → returns 3.  
4. push(2): stack holds [(3,3),(5,3),(2,2)].  
5. push(1): stack holds [(3,3),(5,3),(2,2),(1,1)].  
6. getMin() → returns 1.  
7. pop() → removes (1,1) → top is now (2,2) → minSoFar=2.  
8. getMin() → returns 2.

### Detailed Code Breakdown  
```cpp
class MinStack {
    stack<pair<int,int>> st;           // 1) Each element: {value, minSoFar}
public:
    MinStack() {}                      // 2) Constructor: nothing special to do.

    void push(int x) {
        int curMin = st.empty()
                     ? x              // 3a) If stack empty, new min is x.
                     : min(x, st.top().second); 
        st.emplace(x, curMin);         // 3b) Push pair: value and updated min.
    }

    void pop() {
        st.pop();                       // 4) Remove the top pair.
    }

    int top() {
        return st.top().first;          // 5) Return only the stored value.
    }

    int getMin() {
        return st.top().second;         // 6) Return the stored minimum.
    }
};
```
- Line 1: `stack<pair<int,int>>` holds both value and minimum so far.  
- Lines 3a–3b: Compute the new running minimum before pushing.  
- Lines 4–6: Pop, top, and getMin run in constant time.

#### Pitfalls & Tips  
- Don’t forget to handle the empty‑stack case in `push`.  
- Avoid using two separate stacks unless it simplifies your logic.
- Always pair `value` and `minSoFar` atomically to keep them in sync.

---

## 3. Evaluate Reverse Polish Notation  
Link: https://leetcode.com/problems/evaluate-reverse-polish-notation/

### Theory: Compute with a Stack of Operands  
In Reverse Polish Notation, operators follow their operands. We push numbers on a stack. When we see an operator, we pop the top two values, apply the operation, then push the result back. At the end, the stack has exactly one value: the final result.

### Approach Walkthrough  
Tokens: `["2", "1", "+", "3", "*"]` represents (2 + 1) × 3:

1. `"2"` → push 2 → stack: [2].  
2. `"1"` → push 1 → stack: [2,1].  
3. `"+"` → pop b=1,a=2 → compute 2+1=3 → push 3 → stack: [3].  
4. `"3"` → push 3 → stack: [3,3].  
5. `"*"` → pop b=3,a=3 → compute 3×3=9 → push 9 → stack: [9].  
6. End → pop top → **9**.

### Detailed Code Breakdown  
```cpp
int evalRPN(vector<string>& tokens) {
    stack<int> st;                     // 1) Stack to hold integer operands.

    for (auto& t : tokens) {           // 2) Process each token.
        if (t == "+" || t == "-" ||    // 3) If token is an operator …
            t == "*" || t == "/") {
            int b = st.top(); st.pop();// 4a) Pop right operand.
            int a = st.top(); st.pop();// 4b) Pop left operand.
            int res = 0;
            if (t == "+") res = a + b; 
            else if (t == "-") res = a - b;
            else if (t == "*") res = a * b;
            else res = a / b;          // 4c) Apply operator.
            st.push(res);              // 4d) Push computed result.
        } else {
            st.push(stoi(t));          // 5) Otherwise: convert string to int and push.
        }
    }
    return st.top();                   // 6) Final result at stack top.
}
```
- Lines 3–4: Distinguish between operators and operands.  
- Lines 4a–4d: Pop in **right then left** order, apply operator, and push result.  
- Line 5: `stoi` transforms numeric string to integer.  
- Line 6: After processing all tokens, stack has one element: the answer.

#### Pitfalls & Tips  
- Always pop `b` then `a`: reversing pops reverses subtraction/division.  
- Division of negative numbers in C++ floors toward zero—assume input avoids fractional issues.  
- Guard against invalid tokens or empty stack for robust code.

---

## 4. Generate Parentheses  
Link: https://leetcode.com/problems/generate-parentheses/

### Theory: DFS Backtracking with Constraints  
We build all strings of length 2n. At each step, we can add `'('` if we still have opens left, or `')'` if it won’t unbalance the string (i.e., closes < opens). This is a classic **depth‑first search** (DFS) with backtracking: we try one choice, recurse, then undo (pop) before trying the other.

### Approach Walkthrough  
For n=2, we need all well‑formed strings of length 4:

1. Start with `""`, open=0, close=0.  
2. Can add `'('`: cur="(", open=1,close=0.  
3. From here, add `'('` again: cur="((", open=2,close=0.  
4. Now open==n, can only add `')'`: cur="(()", open=2,close=1.  
5. Add `')'`: cur="(())", open=2,close=2 → record it.  
6. Backtrack to cur="(", open=1,close=0, then try adding `')'`: cur="()", etc. → record `"()()"`.  

Result: `["(())","()()"]`.

### Detailed Code Breakdown  
```cpp
void backtrack(int n, int open, int close,
               string& cur, vector<string>& res) {
    if (cur.size() == 2*n) {           // 1) Base case: built full string.
        res.push_back(cur);             // 2) Save the valid combination.
        return;                         // 3) Backtrack.
    }
    // 4) If we can still add '(', do so.
    if (open < n) {
        cur.push_back('(');
        backtrack(n, open+1, close, cur, res);
        cur.pop_back();                 // 5) Undo addition.
    }
    // 6) If we can add ')', do so.
    if (close < open) {
        cur.push_back(')');
        backtrack(n, open, close+1, cur, res);
        cur.pop_back();                 // 7) Undo addition.
    }
}

vector<string> generateParenthesis(int n) {
    vector<string> res;                 // 8) To collect results.
    string cur;                         // 9) Building string.
    backtrack(n, 0, 0, cur, res);       // 10) Kick off recursion.
    return res;                         // 11) Return all combinations.
}
```
- Line 1–3: When we reach length 2n, we have one valid result.  
- Lines 4–5: Add `'('` if we haven’t used all opens.  
- Lines 6–7: Add `')'` only if it keeps the string valid (`close < open`).  
- Recursion + pop_back() implements the backtracking pattern.

#### Pitfalls & Tips  
- Forgetting to `pop_back()` leads to corrupt `cur` for subsequent branches.  
- Ensure you never add more `')'` than `'('` (maintains validity invariant).  
- Watch recursion depth for large n (n ≤ 8 is typical).

---

## 5. Daily Temperatures  
Link: https://leetcode.com/problems/daily-temperatures/

### Theory: Monotonic Decreasing Stack for Next Greater Element  
We want, for each day i, the next future day j where T[j] > T[i]. A **monotonic stack** keeps indices of days in **strictly decreasing** temperature order. When a new temperature is higher, we “resolve” all colder days on the stack by popping and computing `j - i`.

### Approach Walkthrough  
For `T = [73,74,75,71,69,72,76,73]`:

1. Day 0 (73): stack empty → push 0.  
2. Day 1 (74): 74 > T[0]=73 → pop 0 → res[0]=1−0=1. Then push 1.  
3. Day 2 (75): 75 > 74 → pop 1 → res[1]=2−1=1; then pop  (next) none. Push 2.  
4. Continue to fill `res` array.  
5. Unresolved days remain 0.

### Detailed Code Breakdown  
```cpp
vector<int> dailyTemperatures(vector<int>& T) {
    int n = T.size();
    vector<int> res(n, 0);              // 1) Result array, default 0.
    stack<int> st;                      // 2) Stack of indices, T[st.top()] decreasing.

    for (int i = 0; i < n; ++i) {       // 3) Iterate days.
        // 4) While current temp exceeds stack-top day temp:
        while (!st.empty() && T[i] > T[st.top()]) {
            int prev = st.top();        // 4a) Index of colder day.
            st.pop();                   // 4b) Remove it.
            res[prev] = i - prev;       // 4c) Compute wait days.
        }
        st.push(i);                     // 5) Push current day index.
    }
    return res;                         // 6) Return array of waits.
}
```
- Line 1: Initialize `res` to all zeros.  
- Line 2: Stack holds indices whose next warmer day is not yet found.  
- Lines 4a–4c: Resolve all previous days colder than today.  
- Line 5: Push current index for future resolution.

#### Pitfalls & Tips  
- Always compare `T[i] > T[st.top()]` (strictly “next greater”).  
- Do not forget to push the current index after resolution.  
- Unresolved indices at end remain 0 by default.

---

## 6. Car Fleet  
Link: https://leetcode.com/problems/car-fleet/

### Theory: Sorting + Stack‑Like Fleet Time Comparison  
Each car’s **arrival time** at the target = `(target - position) / speed`. Cars sorted by starting position from closest to farthest. As you traverse that sorted list, keep track of the **slowest arrival time so far** (the stack’s top). If a car’s time ≤ current slowest, it catches up (joins) and does not form a new fleet; otherwise it starts a new fleet.

### Approach Walkthrough  
Positions `[10, 8, 0, 5, 3]`, speeds `[2,4,1,1,3]`, target=12:

1. Compute times:  
   - Car at 10 → (12−10)/2 =1  
   - 8 → 1  
   - 5 → 7  
   - 3 → 3  
   - 0 →12  
2. Pair & sort by position descending:  
   - 10→1, 8→1, 5→7, 3→3, 0→12  
3. Traverse:  
   - 10→1 → new fleet (fleets=1, slowest=1)  
   - 8→1 → time=1 ≤ slowest=1 → joins  
   - 5→7 → 7>1 → new fleet (fleets=2, slowest=7)  
   - 3→3 → 3≤7 → joins  
   - 0→12 →12>7 → new fleet (fleets=3)

### Detailed Code Breakdown  
```cpp
int carFleet(int target, vector<int>& position, vector<int>& speed) {
    int n = position.size();
    // 1) Build vector of {pos, timeToTarget}
    vector<pair<int,double>> cars(n);
    for (int i = 0; i < n; ++i) {
        double t = (double)(target - position[i]) / speed[i];
        cars[i] = { position[i], t };
    }
    // 2) Sort by starting position descending.
    sort(cars.begin(), cars.end(),
         [](auto &a, auto &b){ return a.first > b.first; });

    int fleets = 0;
    double slowest = 0.0;              // 3) Slowest arrival time of formed fleets.

    // 4) Traverse sorted cars.
    for (auto& car : cars) {
        double t = car.second;
        if (t > slowest) {             // 5) If this car arrives later than all ahead,
            fleets++;                  //    it forms a new fleet.
            slowest = t;               //    Update the slowest time.
        }
        // Else: t ≤ slowest → joins existing fleet.
    }
    return fleets;                     // 6) Number of fleets.
}
```
- Line 1: Compute each car’s time to reach target.  
- Line 2: Sorting ensures we process closest cars first.  
- Lines 5–6: New fleet if time exceeds the current slowest; else joins.

#### Pitfalls & Tips  
- Beware floating‑point precision—use `double`.  
- Sorting must be descending by position.  
- The “stack” of fleet times is implicit in the `slowest` variable.

---

## 7. Largest Rectangle in Histogram  
Link: https://leetcode.com/problems/largest-rectangle-in-histogram/

### Theory: Monotonic Increasing Stack for Areas  
We want the largest rectangle under bars. We push indices onto a stack that maintains **increasing heights**. When a new bar is shorter, we pop taller bars, and compute area with that height spanning from the bar after the new top to the current index minus one.

### Approach Walkthrough  
Heights `[2,1,5,6,2,3]`:

1. Append 0 at end → `[2,1,5,6,2,3,0]`.  
2. i=0 (2): stack empty → push 0.  
3. i=1 (1): 1<2 → pop 0 → width = 1 (i - prevIndexAfterPop = 1−(-1)−1 =1) → area=2×1=2. Push 1.  
4. i=2 (5): 5>1 → push 2.  
5. i=3 (6): 6>5 → push 3.  
6. i=4 (2): 2<6 pop3→area=6×1=6; 2<5 pop2→area=5×2=10; push4.  
7. Continue until end, tracking maxArea=10.

### Detailed Code Breakdown  
```cpp
int largestRectangleArea(vector<int>& heights) {
    heights.push_back(0);              // 1) Append sentinel bar of height 0.
    int n = heights.size();
    stack<int> st;                     // 2) Stack of indices (increasing heights).
    int maxArea = 0;                   // 3) Track maximum area.

    for (int i = 0; i < n; ++i) {
        // 4) While current bar is shorter than top of stack
        while (!st.empty() && heights[i] < heights[st.top()]) {
            int h = heights[st.top()]; // 4a) Height of the bar to compute.
            st.pop();                  // 4b) Pop it.
            int left = st.empty()      // 4c) Find left boundary index.
                       ? -1
                       : st.top();
            int width = i - left - 1;  // 4d) Width between boundaries.
            maxArea = max(maxArea, h * width); // 4e) Update max area.
        }
        st.push(i);                    // 5) Push current bar index.
    }
    return maxArea;                   // 6) Return the largest found.
}
```
- Line 1: Sentinel triggers final area computations.  
- Lines 4a–4e: Pop taller bars and compute area using their height.  
- Line 4c: If stack is empty after pop, left boundary is before index 0 (use −1).  

#### Pitfalls & Tips  
- Forgetting the sentinel causes leftover bars not to be processed.  
- Off‑by‑one in width formula: width = `i - left - 1`.  
- Stack must store indices, not heights.

---

## 🔧 Expanded Helper Functions  

```cpp
// Print a 1D vector of ints
void printVec(const vector<int>& v) {
    for (int x : v) 
        cout << x << ' ';
    cout << "\n";
}

// Print a vector of strings
void printStrVec(const vector<string>& v) {
    for (auto& s : v) 
        cout << '"' << s << "\" ";
    cout << "\n";
}

// Print a vector of doubles (e.g., arrival times)
void printDoubleVec(const vector<double>& v) {
    for (double d : v) 
        cout << fixed << setprecision(2) << d << ' ';
    cout << "\n";
}
```
- **Why it works**: Simple iteration with appropriate formatting.  
- **When to use**: Debug intermediate results (stack contents, window states).  
- **Adaptations**:  
  - Change element type or precision.  
  - Wrap in `#ifdef DEBUG` to disable in production.

---

## 🔍 Deep Dive into Paradigms

### 1. Basic Stack Operations  
- Use when you need **Last‑In‑First‑Out** behavior (matching parentheses, undo/redo).  
- Key invariant: the top of the stack is always the most recent unprocessed element.

### 2. Monotonic Stack  
- Maintain a stack sorted by values (increasing or decreasing).  
- Use to solve “next greater/smaller element” and histogram area problems in O(n).  
- Invariant: the stack’s contents always satisfy the monotonic property.

### 3. Backtracking with Stack‑Like Recursion  
- DFS recursion implicitly uses a call stack.  
- Push choice, recurse, then pop to backtrack.  
- Use when exploring combinations or permutations under constraints.

### 4. Sorting + Stack Logic  
- Combine sorting with a simple pass using a stack or a single variable to form fleets or groupings.  
- Sorting provides a natural order; stack logic aggregates along that order.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Basic Stack**: Implement Undo/Redo, Min Queue, Max Stack  
- **Monotonic Stack**: Next Greater Element II, Trapping Rain Water II, Sliding Window Maximum  
- **Backtracking**: Parentheses II (with different types), Combination Sum, Palindrome Partitioning  
- **Sorting + Stacks**: Queue Reconstruction by Height, Car Pooling

### Advanced Variants & Real‑World Applications  
- **Expression Evaluators**: Build a full infix→RPN→evaluation engine.  
- **Concurrency**: Use stacks for thread contexts or lock tracking.  
- **Graphics**: Scene graph traversal (DFS) and backtracking.  
- **Financial Modeling**: Fleet logic applied to market grouping or order matching.

### Debugging Tips & Mental Checklists  
1. **Stack Safety**: Always check empty before pop/top.  
2. **Invariant Validation**: After each push/pop, confirm the stack still respects its property (e.g., monotonicity).  
3. **Boundary Conditions**: Sentinels, off‑by‑one widths, balanced counts in backtracking.  
4. **Complexity Mindset**: Ensure inner loops (e.g., while‑pop) still lead to overall O(n).  
5. **Small Examples**: Trace by hand on minimal inputs (e.g., height=[2,1]) before coding.

With these patterns and practices, you’re ready to tackle any stack problem confidently. Happy coding!  