<!--markdownlint-disable-->
# 📚 Linked List (Advanced Beginner‑Friendly Master Guide)

Welcome to your step‑by‑step master guide on **Linked List** problems. We preserve the original structure—problem titles, links, and code blocks—while teaching each concept from first principles. Every problem includes:

1. **Theory**: Why this approach works and what the pointers are doing.  
2. **Approach Walkthrough**: A small example you can trace on paper.  
3. **Detailed Code Breakdown**: Line‑by‑line commentary in plain English.  
4. **Pitfalls & Tips**: Common errors and how to avoid them.  

After all eleven problems, you’ll find:

- **Expanded Helper Functions** with usage notes.  
- **Deep Dive** into common linked‑list paradigms.  
- **Next Steps to Mastery**: follow‑up challenges, advanced variants, and debugging checklists.

Let’s get started!

---

## 1. Reverse Linked List  
Link: https://leetcode.com/problems/reverse-linked-list/

### Theory: Iterative Pointer Reversal  
We walk the list, one node at a time, and flip its `next` pointer to point backward. By keeping a `prev` pointer (initially null) and a `curr` pointer (initially head), we:

- Save `curr->next` in `nextNode`.  
- Redirect `curr->next` to `prev`.  
- Advance `prev` to `curr` and `curr` to `nextNode`.  
- When `curr` is null, `prev` is the new head.

This runs in O(n) time and uses O(1) extra space.

### Approach Walkthrough  
Given `1 → 2 → 3 → null`:

1. `prev = null`, `curr = 1`.  
2. Save `nextNode = 2`. Set `1->next = null`. Advance `prev = 1`, `curr = 2`.  
3. Save `nextNode = 3`. Set `2->next = 1`. Advance `prev = 2`, `curr = 3`.  
4. Save `nextNode = null`. Set `3->next = 2`. Advance `prev = 3`, `curr = null`.  
5. Return `prev` → list is `3 → 2 → 1 → null`.

### Detailed Code Breakdown  
```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;            // 1) 'prev' will trail behind and become new head.
    ListNode* curr = head;               // 2) 'curr' walks forward through the list.
    while (curr) {                       // 3) Process until curr reaches end (null).
        ListNode* nextNode = curr->next; // 4) Temporarily save the next node.
        curr->next = prev;               // 5) Reverse: point current node back to prev.
        prev = curr;                     // 6) Step prev forward to current.
        curr = nextNode;                 // 7) Step curr forward to saved next.
    }
    return prev;                         // 8) 'prev' is the new head after full reversal.
}
```

#### Pitfalls & Tips  
- Always save `nextNode` before changing `curr->next`.  
- If you swap steps, you’ll lose access to the remainder of the list.  
- Verify on a two‑node list to ensure pointers are correct.

---

## 2. Merge Two Sorted Lists  
Link: https://leetcode.com/problems/merge-two-sorted-lists/

### Theory: Dummy‑Node Merge  
Using a **dummy head** simplifies building the result list. We keep a `tail` pointer at the end of the merged list and pick the smaller node from `l1` or `l2` each time, appending it to `tail->next`.

### Approach Walkthrough  
`l1: 1→3→5`, `l2: 2→4→6`:

1. `dummy→null`, `tail = dummy`.  
2. Compare 1 vs. 2 → append 1 → `tail→1`. Advance `l1`.  
3. Compare 3 vs. 2 → append 2 → `tail→2`. Advance `l2`.  
4. Continue until one list empties, then append the rest.

### Detailed Code Breakdown  
```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);                   // 1) Create a dummy node as the list head.
    ListNode* tail = &dummy;             // 2) 'tail' always points to last node in result.
    while (l1 && l2) {                   // 3) While both lists have nodes:
        if (l1->val < l2->val) {         // 4) Choose the smaller node.
            tail->next = l1;             // 5) Attach l1 node to result.
            l1 = l1->next;               // 6) Advance l1.
        } else {
            tail->next = l2;             // 7) Attach l2 node to result.
            l2 = l2->next;               // 8) Advance l2.
        }
        tail = tail->next;               // 9) Move tail to the newly added node.
    }
    tail->next = l1 ? l1 : l2;           // 10) Append remaining nodes.
    return dummy.next;                   // 11) Return merged list, skipping dummy.
}
```

#### Pitfalls & Tips  
- Never forget to return `dummy.next`, not `&dummy`.  
- Using a dummy avoids handling the first node as a special case.  
- If one list is longer, the remaining tail attaches in one step.

---

## 3. Linked List Cycle  
Link: https://leetcode.com/problems/linked-list-cycle/

### Theory: Floyd’s Fast & Slow Pointers  
A **cycle** implies that a fast pointer (2 steps per move) will eventually lap a slow pointer (1 step per move). If they ever meet, there’s a cycle. If the fast pointer reaches null, there’s no cycle.

### Approach Walkthrough  
For `1→2→3→4→2 (cycle)`:

1. `slow=1`, `fast=1`.  
2. Move `slow=2`, `fast=3`.  
3. Move `slow=3`, `fast=2` (wrapped).  
4. Move `slow=4`, `fast=4` → they meet → cycle detected.

### Detailed Code Breakdown  
```cpp
bool hasCycle(ListNode* head) {
    ListNode* slow = head;               // 1) Moves by 1.
    ListNode* fast = head;               // 2) Moves by 2.
    while (fast && fast->next) {         // 3) Continue while fast can advance.
        slow = slow->next;               // 4) Advance slow by one.
        fast = fast->next->next;         // 5) Advance fast by two.
        if (slow == fast)                // 6) Pointers met?
            return true;                 //    → cycle exists.
    }
    return false;                        // 7) Fast reached end → no cycle.
}
```

#### Pitfalls & Tips  
- Always check `fast` and `fast->next` for null before moving two steps.  
- A single‑node list without a self‑loop returns false.  
- For Cycle II (finding start), reset one pointer to head and move both by one.

---

## 4. Reorder List  
Link: https://leetcode.com/problems/reorder-list/

### Theory: Split, Reverse, Merge  
We split the list in half, reverse the second half, then merge nodes alternately:

1. **Find middle** with fast/slow.  
2. **Reverse** second half.  
3. **Merge** two halves by alternating nodes.

### Approach Walkthrough  
`1→2→3→4→5`:

1. Middle at `3`: split into `1→2→3` and `4→5`.  
2. Reverse `4→5` → `5→4`.  
3. Merge: `1→5→2→4→3`.

### Detailed Code Breakdown  
```cpp
void reorderList(ListNode* head) {
    if (!head || !head->next) return;           // 1) Edge‑case: 0 or 1 node.

    // 2. Find middle
    ListNode *slow = head, *fast = head;
    while (fast->next && fast->next->next) {
        slow = slow->next;                      // advance 1
        fast = fast->next->next;                // advance 2
    }

    // 3. Split & reverse second half
    ListNode* second = slow->next;             
    slow->next = nullptr;                       // cut first half
    ListNode* prev = nullptr;
    while (second) {                            
        ListNode* nxt = second->next;          
        second->next = prev;                   
        prev = second;                         
        second = nxt;                          
    }

    // 4. Merge halves
    ListNode* first = head;
    while (prev) {
        ListNode* tmp1 = first->next;          
        ListNode* tmp2 = prev->next;           
        first->next = prev;                    
        prev->next = tmp1;                     
        first = tmp1;                          
        prev = tmp2;                           
    }
}
```

#### Pitfalls & Tips  
- Remember to cut the list (`slow->next = nullptr`) before reversing.  
- Use three pointers during reversal to avoid losing the rest of the list.  
- During merge, always save both `first->next` and `prev->next` before re-linking.

---

## 5. Remove Nth Node From End of List  
Link: https://leetcode.com/problems/remove-nth-node-from-end-of-list/

### Theory: Two‑Pointer Gap  
We place `first` and `second` pointers at a fixed gap of `n+1` nodes apart. When `first` hits the end, `second` points just before the node to remove.

### Approach Walkthrough  
List: `1→2→3→4→5`, `n = 2`:

1. `dummy→1→2→3→4→5`, both pointers at `dummy`.  
2. Advance `first` 3 steps → at `3`.  
3. Move both until `first` reaches null: `first` runs to end, `second` lands on `3`.  
4. `second->next` is `4` (the 2nd from end) → skip it → result `1→2→3→5`.

### Detailed Code Breakdown  
```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0);                          // 1) Dummy before head.
    dummy.next = head;
    ListNode* first = &dummy;                  
    ListNode* second = &dummy;

    // 2) Move first ahead by n+1.
    for (int i = 0; i <= n; ++i) 
        first = first->next;

    // 3) Move both until first is past the end.
    while (first) {
        first = first->next;
        second = second->next;
    }

    // 4) second->next is node to remove.
    second->next = second->next->next;

    return dummy.next;                          // 5) Return new head.
}
```

#### Pitfalls & Tips  
- Advance `first` exactly `n+1` times so that `second` lands before the target.  
- Handle the case of removing the head (`n == length`) via the dummy.  
- Always return `dummy.next`.

---

## 6. Copy List with Random Pointer  
Link: https://leetcode.com/problems/copy-list-with-random-pointer/

### Theory: Interweaving Clones In‑Place  
We avoid extra space by inserting each clone right after its original, setting random pointers, then detaching clones:

1. **Interleave**: for each original `cur`, create `clone` and insert it `cur->next = clone`.  
2. **Random**: set `clone->random = cur->random->next` if `cur->random` exists.  
3. **Detach**: restore original `next` pointers and extract cloned list.

### Approach Walkthrough  
Original: `A→B→C`, with random edges. After interleaving: `A→A'→B→B'→C→C'`. Random of `A'` is `A.random->next`. Then detach.

### Detailed Code Breakdown  
```cpp
Node* copyRandomList(Node* head) {
    if (!head) return nullptr;                   // 1) Empty list.

    // 2) Interleave clones.
    for (Node* cur = head; cur; cur = cur->next->next) {
        Node* clone = new Node(cur->val);        
        clone->next = cur->next;                 
        cur->next = clone;                       
    }

    // 3) Assign random pointers.
    for (Node* cur = head; cur; cur = cur->next->next) {
        if (cur->random)
            cur->next->random = cur->random->next;
    }

    // 4) Detach clones into new list.
    Node* dummy = new Node(0);
    Node* tail = dummy;
    for (Node* cur = head; cur; cur = cur->next) {
        tail->next = cur->next;                  
        tail = tail->next;                       
        cur->next = tail->next;                  // restore original
    }
    return dummy->next;                          // 5) Return clone head.
}
```

#### Pitfalls & Tips  
- Always use `cur->next->next` in loops after interleaving.  
- Detach carefully: restore original `next` before moving `cur`.  
- Clean up the dummy node to avoid memory leaks.

---

## 7. Add Two Numbers  
Link: https://leetcode.com/problems/add-two-numbers/

### Theory: Digit‑Wise Addition with Carry  
Treat each list node as a digit (least significant first). Sum corresponding digits plus a carry, create new nodes for each sum % 10, propagate carry = sum / 10.

### Approach Walkthrough  
`(2→4→3)` + `(5→6→4)`:

- 2+5=7, carry=0 → node 7.  
- 4+6+0=10, carry=1 → node 0.  
- 3+4+1=8, carry=0 → node 8.  
Result: `7→0→8`.

### Detailed Code Breakdown  
```cpp
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);                           // 1) Dummy for result.
    ListNode* tail = &dummy;
    int carry = 0;                               // 2) Running carry.

    // 3) Process until both lists and carry are exhausted.
    while (l1 || l2 || carry) {
        int sum = carry;                         
        if (l1) { sum += l1->val; l1 = l1->next; }
        if (l2) { sum += l2->val; l2 = l2->next; }
        carry = sum / 10;                        
        tail->next = new ListNode(sum % 10);     // 4) Create node for digit.
        tail = tail->next;                       
    }

    return dummy.next;                           // 5) Return sum list.
}
```

#### Pitfalls & Tips  
- Include `carry` in loop condition to handle final carry node.  
- Treat missing nodes as zero when one list is shorter.  
- Always update `carry` before creating the new node.

---

## 8. Find the Duplicate Number  
Link: https://leetcode.com/problems/find-the-duplicate-number/

### Theory: Array as Linked List + Cycle Detection  
We interpret `nums` as a linked structure: index i points to `nums[i]`. The duplicate creates a cycle. Floyd’s algorithm finds the intersection, then locating the cycle entry gives the duplicate.

### Approach Walkthrough  
`nums = [1,3,4,2,2]`:

1. Start `slow=1`, `fast=1`.  
2. Advance until they meet inside the cycle.  
3. Reset `slow` to start; move both by one until they meet at the cycle’s entry (`2`).

### Detailed Code Breakdown  
```cpp
int findDuplicate(vector<int>& nums) {
    int slow = nums[0], fast = nums[0];
    // 1) Find intersection inside cycle.
    do {
        slow = nums[slow];
        fast = nums[nums[fast]];
    } while (slow != fast);

    // 2) Find entry point of cycle.
    slow = nums[0];
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[fast];
    }
    return slow;                              // 3) Duplicate number.
}
```

#### Pitfalls & Tips  
- Use `do…while` to ensure at least one advance.  
- Reset only one pointer to the start before finding entry.  
- This uses O(1) space and no modification of the array.

---

## 9. LRU Cache  
Link: https://leetcode.com/problems/lru-cache/

### Theory: Doubly‑Linked List + Hash Map  
Keep a **doubly‑linked list** of key‑value pairs in recency order (most recent at front). A hash map points keys to their nodes. `get` and `put` operations move or remove nodes in O(1) and update the map.

### Approach Walkthrough  
Capacity=2:

1. `put(1,1)` → list: (1)  
2. `put(2,2)` → list: (2→1)  
3. `get(1)` → move 1 to front → (1→2)  
4. `put(3,3)` → capacity exceeded → remove tail (2) → insert 3 → (3→1)

### Detailed Code Breakdown  
```cpp
class LRUCache {
    int cap;
    list<pair<int,int>> lst;                  // 1) Front=MRU, back=LRU.
    unordered_map<int, list<pair<int,int>>::iterator> mp;
public:
    LRUCache(int capacity) : cap(capacity) {}

    int get(int key) {
        if (!mp.count(key)) return -1;        // 2) Not found.
        auto it = mp[key];
        lst.splice(lst.begin(), lst, it);     // 3) Move node to front.
        return it->second;                    // 4) Return value.
    }

    void put(int key, int value) {
        if (mp.count(key)) {                  // 5) Update existing.
            auto it = mp[key];
            it->second = value;
            lst.splice(lst.begin(), lst, it);
        } else {
            if (lst.size() == cap) {          // 6) Evict LRU.
                auto kv = lst.back();
                mp.erase(kv.first);
                lst.pop_back();
            }
            lst.emplace_front(key, value);    // 7) Insert new MRU.
            mp[key] = lst.begin();
        }
    }
};
```

#### Pitfalls & Tips  
- Always update the map’s iterator when you splice/move a node.  
- Evict **after** checking capacity, not before.  
- Use `splice` instead of remove+insert to maintain O(1).

---

## 10. Merge k Sorted Lists  
Link: https://leetcode.com/problems/merge-k-sorted-lists/

### Theory: Min‑Heap of Heads  
We keep the current head of each list in a **min‑heap** (priority queue). Each pop gives the smallest node; we attach it to our result and, if that node has a `next`, push the `next` into the heap.

### Approach Walkthrough  
Lists: `[1→4]`, `[2→3]`, `[5]`:

1. Heap contains [1,2,5]. Pop 1 → result 1. Push 4.  
2. Heap [2,4,5]. Pop 2 → result 1→2. Push 3.  
3. Continue until heap is empty.

### Detailed Code Breakdown  
```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    auto cmp = [](ListNode* a, ListNode* b){
        return a->val > b->val;            // 1) Min‑heap comparator.
    };
    priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> pq(cmp);
    for (auto l : lists)                   // 2) Push initial heads.
        if (l) pq.push(l);

    ListNode dummy(0), *tail = &dummy;     // 3) Dummy head for result.
    while (!pq.empty()) {
        auto node = pq.top(); pq.pop();   // 4) Smallest node.
        tail->next = node;                
        tail = tail->next;
        if (node->next)                   // 5) Push next from that list.
            pq.push(node->next);
    }
    return dummy.next;                     // 6) Return merged list.
}
```

#### Pitfalls & Tips  
- Guard against empty input (no lists).  
- The comparator must be `a->val > b->val` for a min‑heap.  
- Each node is pushed and popped once → O(N log k).

---

## 11. Reverse Nodes in k‑Group  
Link: https://leetcode.com/problems/reverse-nodes-in-k-group/

### Theory: Group‑Wise In‑Place Reversal  
We reverse each block of k nodes in place using pointer juggling. A `prevGroup` pointer marks the node before the current group. We check if k nodes remain, then repeatedly extract the next node and insert it immediately after `prevGroup`, effectively reversing the segment.

### Approach Walkthrough  
List `1→2→3→4→5`, k=3:

1. Check nodes 1,2,3 exist.  
2. Reverse to `3→2→1`. List is `dummy→3→2→1→4→5`.  
3. Move `prevGroup` to `1` and repeat for next group.

### Detailed Code Breakdown  
```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode dummy(0);
    dummy.next = head;                     
    ListNode* prevGroup = &dummy;          // 1) Node before current group.

    while (true) {
        // 2) Check if k nodes remain.
        ListNode* node = prevGroup;
        for (int i = 0; i < k && node; ++i) 
            node = node->next;
        if (!node) break;                  //    Fewer than k → done.

        // 3) Reverse k nodes.
        ListNode* groupStart = prevGroup->next;
        ListNode* curr = groupStart->next;
        for (int i = 1; i < k; ++i) {
            groupStart->next = curr->next;  
            curr->next = prevGroup->next;   
            prevGroup->next = curr;         
            curr = groupStart->next;        
        }
        prevGroup = groupStart;             // 4) Move prevGroup to end of reversed.
    }

    return dummy.next;                     // 5) Return new head.
}
```

#### Pitfalls & Tips  
- Always verify k nodes exist before reversing.  
- Use three pointers (`prevGroup`, `groupStart`, `curr`) carefully.  
- After reversing, `groupStart` becomes the tail of that segment.

---

## 🔧 Expanded Helper Functions  
```cpp
// Definition for singly‑linked list node.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

// Definition for node with random pointer.
struct Node {
    int val;
    Node* next;
    Node* random;
    Node(int v) : val(v), next(nullptr), random(nullptr) {}
};

// Print a singly‑linked list (values and arrows).
void printList(ListNode* head) {
    while (head) {
        cout << head->val << " -> ";
        head = head->next;
    }
    cout << "null\n";
}

// Utility to build a list from vector.
ListNode* buildList(const vector<int>& a) {
    ListNode dummy(0), *tail = &dummy;
    for (int x : a) {
        tail->next = new ListNode(x);
        tail = tail->next;
    }
    return dummy.next;
}
```
- **When to use**: debugging, verifying list structure, quick test setup.  
- **Adaptation**: extend `buildList` to include random pointers or cycles.

---

## 🔍 Deep Dive into Paradigms

1. **Pointer Reversal**: core to reversing lists in O(1) space.  
   - Invariant: `prev→…→head` reversed portion, `curr→…` unreversed.  
2. **Dummy‑Node Technique**: simplifies edge cases for merges and removals.  
3. **Fast & Slow Pointers**: detect cycles or find list midpoints in one pass.  
4. **Interleaving Clones**: clever in‑place deep copy without extra map.  
5. **Min‑Heap Merge**: generalizes merging k sorted lists in O(N log k).  
6. **Group‑Wise In‑Place Reversal**: repeated pointer insertion to reverse fixed blocks.

---

## 🚀 Next Steps to Mastery

### Follow‑Up Problems  
- **Easy → Medium → Hard**  
  - Reverse List II (reverse between positions)  
  - Palindrome Linked List, Split Linked List in Parts  
  - Flatten Multilevel Doubly Linked List, Design Skiplist  

### Advanced Variants & Applications  
- Persistent linked lists in functional programming.  
- Lock‑free linked lists in concurrent code.  
- Memory pools and custom allocators for nodes.  

### Debugging Tips & Mental Checklists  
1. Always save `next` pointers before reassigning.  
2. Use a dummy to avoid special‑case checks on head.  
3. After pointer operations, ensure no unintended cycles.  
4. Trace small examples by hand (2–3 nodes) to validate logic.  
5. Confirm your loop conditions guarantee termination.

Keep practicing these patterns until pointer logic feels natural. Happy coding!  