<!--markdownlint-disable-->
# 📚 Linked List

This guide covers eleven classic LeetCode problems under the **Linked List** topic. For each problem, you’ll find:

1. A plain‑English description  
2. Step‑by‑step core ideas  
3. A concise C++ snippet with inline comments  
4. Common helper functions  
5. A breakdown of algorithmic paradigms  
6. Advanced tips & practice  

---

## 1. Reverse Linked List  
Link: https://leetcode.com/problems/reverse-linked-list/

### Description  
Given the head of a singly linked list, reverse the list and return the new head.

### Core Idea: Iterative Pointer Reversal  
1. Maintain two pointers: `prev = nullptr`, `curr = head`.  
2. At each node, save `next = curr->next`.  
3. Redirect `curr->next = prev`.  
4. Move `prev = curr`, `curr = next`.  
5. When `curr` is null, `prev` is the new head.

### C++ Code Snippet: Iterative Reversal  
```cpp
// Reverses a singly linked list iteratively.
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;            // will become new head
    ListNode* curr = head;               // current node to process
    while (curr) {
        ListNode* nextNode = curr->next; // save next node
        curr->next = prev;               // reverse pointer
        prev = curr;                     // step prev forward
        curr = nextNode;                 // step curr forward
    }
    return prev;                         // prev is new head
}
```

---

## 2. Merge Two Sorted Lists  
Link: https://leetcode.com/problems/merge-two-sorted-lists/

### Description  
Merge two sorted singly linked lists and return it as a new sorted list.

### Core Idea: Dummy Node Merge  
1. Create a `dummy` node to anchor the result.  
2. Maintain `tail` pointing to the last node of merged list.  
3. While both lists non‑empty, attach the smaller node to `tail->next`.  
4. Advance `tail` and the list from which you took a node.  
5. Attach any remaining nodes after one list is exhausted.

### C++ Code Snippet: Dummy‑Node Merge  
```cpp
// Merges two sorted lists into one sorted list.
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);                   // sentinel head
    ListNode* tail = &dummy;             // tail of merged list
    while (l1 && l2) {
        if (l1->val < l2->val) {
            tail->next = l1;             // attach l1
            l1 = l1->next;               // advance l1
        } else {
            tail->next = l2;             // attach l2
            l2 = l2->next;               // advance l2
        }
        tail = tail->next;               // advance tail
    }
    // attach remaining list
    tail->next = l1 ? l1 : l2;
    return dummy.next;                   // head of merged list
}
```

---

## 3. Linked List Cycle  
Link: https://leetcode.com/problems/linked-list-cycle/

### Description  
Determine if a linked list has a cycle (back‑pointer).

### Core Idea: Floyd’s Cycle Detection  
1. Use two pointers: `slow` moves one step, `fast` moves two steps.  
2. If `fast` or `fast->next` becomes null → no cycle.  
3. If `slow == fast` at any point → cycle detected.

### C++ Code Snippet: Fast & Slow Pointers  
```cpp
// Detects if a cycle exists in the list.
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
    while (fast && fast->next) {
        slow = slow->next;               // move by 1
        fast = fast->next->next;         // move by 2
        if (slow == fast) return true;   // pointers met => cycle
    }
    return false;                        // reached end => no cycle
}
```

---

## 4. Reorder List  
Link: https://leetcode.com/problems/reorder-list/

### Description  
Reorder list as L0→Ln→L1→Ln‑1→L2→… in‑place without altering node values.

### Core Idea: Split, Reverse, Merge  
1. Find middle using fast/slow pointers.  
2. Split into two lists: front and back.  
3. Reverse the back list.  
4. Merge front and reversed back alternately.

### C++ Code Snippet: Reorder by Halves  
```cpp
// Reorders list to L0→Ln→L1→Ln‑1→…
void reorderList(ListNode* head) {
    if (!head || !head->next) return;
    // 1. find middle
    ListNode *slow = head, *fast = head;
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    // 2. split & reverse second half
    ListNode* second = slow->next;
    slow->next = nullptr;                // cut
    // reverse second half
    ListNode* prev = nullptr;
    while (second) {
        ListNode* nxt = second->next;
        second->next = prev;
        prev = second;
        second = nxt;
    }
    // 3. merge first and reversed second
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

---

## 5. Remove Nth Node From End of List  
Link: https://leetcode.com/problems/remove-nth-node-from-end-of-list/

### Description  
Remove the N-th node from the end in one pass and return head.

### Core Idea: Two‑Pointer Gap  
1. Create `dummy` before head.  
2. Advance `first` pointer `n+1` steps from `dummy`.  
3. Advance `first` and `second` together until `first` hits null.  
4. `second->next` is the node to remove.  
5. Re-link and return `dummy.next`.

### C++ Code Snippet: One‑Pass Removal  
```cpp
// Removes the n-th node from end in one traversal.
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* first = &dummy;
    ListNode* second = &dummy;
    // advance first n+1 steps
    for (int i = 0; i <= n; ++i) {
        first = first->next;
    }
    // move both until first reaches end
    while (first) {
        first = first->next;
        second = second->next;
    }
    // skip the target node
    second->next = second->next->next;
    return dummy.next;
}
```

---

## 6. Copy List with Random Pointer  
Link: https://leetcode.com/problems/copy-list-with-random-pointer/

### Description  
Each node has `next` and `random` pointers. Create a deep copy of the list.

### Core Idea: Interweaving Nodes (O(1) Space)  
1. For each node, insert its clone between itself and `next`.  
2. Set `clone->random = original->random->next`.  
3. Detach clones to form the new list and restore original.

### C++ Code Snippet: Interleaving Clone Technique  
```cpp
// Copies a list with next and random pointers in O(n) time, O(1) extra space.
Node* copyRandomList(Node* head) {
    if (!head) return nullptr;
    // 1. interleave cloned nodes
    for (Node* cur = head; cur; cur = cur->next->next) {
        Node* clone = new Node(cur->val);
        clone->next = cur->next;
        cur->next = clone;
    }
    // 2. assign random pointers
    for (Node* cur = head; cur; cur = cur->next->next) {
        if (cur->random)
            cur->next->random = cur->random->next;
    }
    // 3. detach clones
    Node* dummy = new Node(0);
    Node* tail = dummy;
    for (Node* cur = head; cur; cur = cur->next) {
        tail->next = cur->next;
        tail = tail->next;
        cur->next = tail->next;        // restore original next
    }
    return dummy->next;
}
```

---

## 7. Add Two Numbers  
Link: https://leetcode.com/problems/add-two-numbers/

### Description  
Add two non‑empty linked lists representing reversed digits. Return the sum as a linked list.

### Core Idea: Digit‑wise Addition with Carry  
1. Traverse both lists node by node, summing `l1->val + l2->val + carry`.  
2. Create new node with `sum % 10`, update `carry = sum / 10`.  
3. Advance lists; when one is null treat its value as 0.  
4. After traversal, if `carry` remains, append a new node.

### C++ Code Snippet: Per‑Digit Summation  
```cpp
// Adds two numbers represented by reversed linked lists.
ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);
    ListNode* tail = &dummy;
    int carry = 0;
    while (l1 || l2 || carry) {
        int sum = carry;
        if (l1) { sum += l1->val; l1 = l1->next; }
        if (l2) { sum += l2->val; l2 = l2->next; }
        carry = sum / 10;
        tail->next = new ListNode(sum % 10);
        tail = tail->next;
    }
    return dummy.next;
}
```

---

## 8. Find the Duplicate Number  
Link: https://leetcode.com/problems/find-the-duplicate-number/

### Description  
Given an array of `n+1` integers in range [1,n], find the one duplicate without modifying the array or using extra space.

### Core Idea: Cycle Detection on Array  
1. Treat array as a linked list: index → next index = `nums[index]`.  
2. Use Floyd’s algorithm to find intersection.  
3. To find entry point (duplicate), start one pointer at `begin`, one at `intersection`, advance both by one.

### C++ Code Snippet: Floyd on Array  
```cpp
// Finds duplicate in array using cycle detection.
int findDuplicate(vector<int>& nums) {
    // 1. find intersection
    int slow = nums[0], fast = nums[0];
    do {
        slow = nums[slow];
        fast = nums[nums[fast]];
    } while (slow != fast);
    // 2. find cycle entry
    slow = nums[0];
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[fast];
    }
    return slow;  // duplicate number
}
```

---

## 9. LRU Cache  
Link: https://leetcode.com/problems/lru-cache/

### Description  
Design a cache with capacity `k` that supports `get` and `put` in O(1) time, evicting least‑recently‑used items.

### Core Idea: Doubly‑Linked List + Hash Map  
1. Maintain a doubly‑linked list of keys in usage order; head = most recent, tail = least.  
2. Hash map maps keys → list nodes.  
3. `get`: if key exists, move node to head, return value; else return -1.  
4. `put`: if key exists, update value and move to head; else create node, insert at head; if capacity exceeded, remove tail node.

### C++ Code Snippet: LRU with List & Unordered Map  
```cpp
// Implements an LRU cache with O(1) operations.
class LRUCache {
    int cap;
    list<pair<int,int>> lst;              // {key, value}, front = MRU
    unordered_map<int, list<pair<int,int>>::iterator> mp;
public:
    LRUCache(int capacity) : cap(capacity) {}
    int get(int key) {
        if (!mp.count(key)) return -1;
        // move accessed node to front
        auto it = mp[key];
        lst.splice(lst.begin(), lst, it);
        return it->second;
    }
    void put(int key, int value) {
        if (mp.count(key)) {
            // update existing and move to front
            auto it = mp[key];
            it->second = value;
            lst.splice(lst.begin(), lst, it);
        } else {
            if (lst.size() == cap) {
                // evict LRU from tail
                auto kv = lst.back();
                mp.erase(kv.first);
                lst.pop_back();
            }
            // insert new node at front
            lst.emplace_front(key, value);
            mp[key] = lst.begin();
        }
    }
};
```

---

## 10. Merge k Sorted Lists  
Link: https://leetcode.com/problems/merge-k-sorted-lists/

### Description  
Merge `k` sorted linked lists into one sorted list and return it.

### Core Idea: Min‑Heap of Heads  
1. Use a min‑heap (priority queue) ordering nodes by value.  
2. Push the head of each non‑empty list into the heap.  
3. Pop the smallest node, attach to result, and if it has `next`, push that too.  
4. Continue until heap is empty.

### C++ Code Snippet: Heap‑Based k‑Way Merge  
```cpp
// Merges k sorted lists using a min-heap.
ListNode* mergeKLists(vector<ListNode*>& lists) {
    auto cmp = [](ListNode* a, ListNode* b){
        return a->val > b->val;
    };
    priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> pq(cmp);
    // initialize heap
    for (auto l : lists) if (l) pq.push(l);
    ListNode dummy(0), *tail = &dummy;
    while (!pq.empty()) {
        auto node = pq.top(); pq.pop();
        tail->next = node;               // attach smallest
        tail = tail->next;
        if (node->next) pq.push(node->next);
    }
    return dummy.next;
}
```

---

## 11. Reverse Nodes in k‑Group  
Link: https://leetcode.com/problems/reverse-nodes-in-k-group/

### Description  
Given a linked list, reverse the nodes of the list `k` at a time and return its modified list. Nodes not a multiple of `k` remain as is.

### Core Idea: Group‑wise Reversal with Dummy  
1. Use a `dummy` before head, and two pointers: `prevGroupEnd`, `curr`.  
2. Count `k` nodes ahead to ensure full group; if less, break.  
3. Reverse the group of `k` nodes via iterative pointer reversal.  
4. Link `prevGroupEnd->next` to new group head, update `prevGroupEnd` and `curr`.

### C++ Code Snippet: Iterative k‑Group Reversal  
```cpp
// Reverses nodes in k-size groups.
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* prevGroup = &dummy;
    while (true) {
        // check if k nodes remain
        ListNode* node = prevGroup;
        for (int i = 0; i < k && node; ++i) node = node->next;
        if (!node) break;
        // reverse k nodes
        ListNode* groupStart = prevGroup->next;
        ListNode* curr = groupStart->next;
        for (int i = 1; i < k; ++i) {
            groupStart->next = curr->next;  // detach curr
            curr->next = prevGroup->next;   // move curr to front of group
            prevGroup->next = curr;         // link curr
            curr = groupStart->next;        // advance curr
        }
        prevGroup = groupStart;            // new tail of reversed group
    }
    return dummy.next;
}
```

---

## 🔧 Common Helper Functions  
```cpp
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

// Definition for random-pointer list.
struct Node {
    int val;
    Node* next;
    Node* random;
    Node(int _v) : val(_v), next(nullptr), random(nullptr) {}
};

// Print a singly-linked list
void printList(ListNode* head) {
    while (head) {
        cout << head->val << " -> ";
        head = head->next;
    }
    cout << "null\n";
}
```

---

## 🔍 Algorithmic Paradigms  
- Iterative reversal (Reverse List, Reverse k‑Group)  
- Dummy‑node merging (Merge Two Lists, Merge k Lists)  
- Two‑pointer gap (Remove Nth From End)  
- Fast/slow pointers (Cycle Detection, Find Duplicate)  
- Split & merge halves (Reorder List)  
- Interleaving clones (Copy Random List)  
- Digit‑wise addition (Add Two Numbers)  
- Min‑heap for k‑way merge (Merge k Lists)  
- Doubly‑linked + map for LRU Cache  

### Complexity Summary  

| Problem                                 | Time                         | Space            |
|-----------------------------------------|------------------------------|------------------|
| Reverse Linked List                     | O(n)                         | O(1)             |
| Merge Two Sorted Lists                  | O(n₁ + n₂)                   | O(1)             |
| Linked List Cycle                       | O(n)                         | O(1)             |
| Reorder List                            | O(n)                         | O(1)             |
| Remove Nth From End                     | O(n)                         | O(1)             |
| Copy List with Random Pointer           | O(n)                         | O(1) extra       |
| Add Two Numbers                         | O(max(n₁,n₂))                | O(max(n₁,n₂))    |
| Find the Duplicate Number               | O(n)                         | O(1)             |
| LRU Cache                               | O(1) per op                 | O(capacity)      |
| Merge k Sorted Lists                    | O(N log k)                   | O(k)             |
| Reverse Nodes in k‑Group                | O(n)                         | O(1)             |

---

## 🚀 Advanced Tips & Practice  

### Strategy Tweaks  
- **Easy**: Master pointer updates and dummy nodes to simplify edge‑cases.  
- **Medium**: Combine two‑pointer and list‑splitting for in‑place operations.  
- **Hard**: Use cycle detection in non‑obvious contexts (arrays, random pointers), and design custom data structures (LRU Cache).

### Follow‑Up Problems  
- Linked List Cycle II, Palindrome Linked List, Odd Even Linked List  
- Split Linked List in Parts, Rotate List  
- Flatten a Multilevel Doubly Linked List  
- Design Skiplist, Design Twitter  

### Common Pitfalls & Debugging  
- Losing `next` pointers during reversal—always store `next` before re-linking.  
- Off‑by‑one when checking group size in k‑group reversal.  
- Forgetting to restore original list when interleaving clones.  
- Handling carry after loop in Add Two Numbers.  
- Keeping map and list in sync for LRU Cache eviction.  

Happy coding and mastering Linked Lists!  