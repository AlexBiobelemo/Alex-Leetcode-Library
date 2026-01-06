## Linked List Cycle
**Language:** python
**Tags:** python,oop,linked list,two-pointers

### Description:
This implementation of Floyd's Cycle-Finding Algorithm (also known as the "Tortoise and Hare" algorithm) to be a classic and highly efficient solution for detecting cycles in a linked list. Let's break it down comprehensively.

---

### 1. Overview & Intent

This code aims to solve the problem of determining whether a given singly linked list contains a cycle. A cycle exists in a linked list if some node in the list can be reached again by continuously following the `next` pointers.

The approach used is Floyd's Cycle-Finding Algorithm, which is an elegant and highly efficient method. The core idea is to use two pointers, one moving faster than the other. If a cycle exists, the faster pointer will eventually "lap" the slower pointer, causing them to meet within the cycle. If no cycle exists, the faster pointer will reach the end of the list (a `None` node).

### 2. How It Works

The algorithm proceeds in the following steps:

1.  **Initial Checks**: It first handles edge cases where the list is empty (`head is None`) or has only one node (`head.next is None`). In both these scenarios, a cycle is impossible, so it immediately returns `False`.
2.  **Pointer Initialization**: Two pointers, `slow` and `fast`, are initialized to point to the `head` of the linked list.
3.  **Iteration**: The code enters a `while` loop that continues as long as `fast` and `fast.next` are not `None`. This condition is crucial because `fast` moves two steps at a time, requiring `fast.next.next` to be a valid access.
4.  **Pointer Movement**:
    *   In each iteration, the `slow` pointer moves one step forward: `slow = slow.next`.
    *   The `fast` pointer moves two steps forward: `fast = fast.next.next`.
5.  **Cycle Detection**: After moving the pointers, the algorithm checks if `slow` and `fast` are pointing to the same node (`if slow == fast:`). If they are, it means the faster pointer has caught up to the slower pointer within a cycle, and thus a cycle exists. The function immediately returns `True`.
6.  **No Cycle**: If the loop completes without `slow` and `fast` ever meeting (i.e., `fast` or `fast.next` becomes `None`), it means the `fast` pointer reached the end of the list, indicating no cycle. The function then returns `False`.

### 3. Key Design Decisions

*   **Floyd's Cycle-Finding Algorithm (Tortoise and Hare)**: This is the fundamental algorithm chosen. Its brilliance lies in its ability to detect cycles using only a constant amount of extra space.
*   **Two Pointers with Different Speeds**: The use of `slow` (1 step/iteration) and `fast` (2 steps/iteration) pointers is central. If there's a cycle, the relative speed difference ensures that `fast` will eventually "catch up" to `slow` if they are both in the cycle. If there's no cycle, `fast` will always reach the end of the list first.
*   **Initialization of Pointers**: Both `slow` and `fast` start at `head`. This is a common and correct starting point for this algorithm. Some variations might start `fast` at `head.next`, but starting both at `head` is perfectly valid and simplifies the initial conditions.
*   **Loop Termination Condition (`while fast and fast.next`)**: This condition is carefully crafted to prevent `AttributeError` when accessing `fast.next.next`. It ensures that `fast` has at least two more nodes ahead of it to make the double jump.
*   **Early Return**: As soon as `slow == fast`, the function returns `True`. This is an optimization to exit immediately upon detection.

### 4. Complexity Analysis

*   **Time Complexity: O(N)**
    *   **No Cycle**: The `fast` pointer traverses the entire list once, and the `slow` pointer traverses it approximately half-way. The number of steps is proportional to the length of the list, N.
    *   **With Cycle**:
        1.  The `fast` pointer travels `k` steps to reach the start of the cycle (where `k` is the number of nodes before the cycle). `slow` also travels `k` steps.
        2.  Once both pointers are in the cycle (of length `C`), `fast` gains one step on `slow` in each iteration. They will meet within `C` steps.
        3.  The total number of steps is roughly `k + C`, which is proportional to the total number of nodes in the list, N.
    *   Therefore, in both cases, the time complexity is linear, O(N), where N is the number of nodes in the linked list.

*   **Space Complexity: O(1)**
    *   The algorithm only uses a few constant-space variables (`head`, `slow`, `fast`). It does not allocate any data structures whose size depends on the input list's size. Hence, the space complexity is constant.

### 5. Edge Cases & Correctness

The code correctly handles various edge cases:

*   **Empty List (`head = None`)**: The `if not head:` check handles this, returning `False`. Correct.
*   **Single Node List (`head -> None`)**: The `if not head.next:` check handles this, returning `False`. Correct.
*   **Two Node List, No Cycle (`head -> node2 -> None`)**:
    *   `slow = head`, `fast = head`
    *   Loop 1: `slow = node2`, `fast = None` (since `head.next.next` is `None`)
    *   Loop terminates because `fast` is `None`. Returns `False`. Correct.
*   **Two Node List, Cycle (`head -> node2 -> head`)**:
    *   `slow = head`, `fast = head`
    *   Loop 1: `slow = node2`, `fast = head` (since `head.next.next` is `head`)
    *   `slow != fast`
    *   Loop 2: `slow = head`, `fast = node2` (since `head.next.next` is `node2`)
    *   `slow != fast`
    *   Loop 3: `slow = node2`, `fast = node2` (since `node2.next.next` is `node2`)
    *   `slow == fast`. Returns `True`. Correct.
*   **Long List, No Cycle**: The `fast` pointer will eventually reach `None` or `fast.next` will be `None`, causing the loop to terminate. Returns `False`. Correct.
*   **Long List, Cycle**: The `slow` and `fast` pointers will eventually meet within the cycle, as demonstrated by the mathematical proof of Floyd's algorithm. Returns `True`. Correct.

### 6. Improvements & Alternatives

*   **Readability**: The code is already very readable and follows standard Python conventions. The variable names `slow` and `fast` are universally understood in the context of this algorithm. Type hints (`Optional[ListNode]`) further enhance clarity.
*   **Robustness**: The initial checks for `head` and `head.next` make the code robust against null or single-node inputs.
*   **Alternatives**:
    1.  **Using a Hash Set (Visited Nodes)**:
        *   **Approach**: Iterate through the linked list. For each node, add it to a hash set (or dictionary). If you encounter a node that is already in the hash set, a cycle exists.
        *   **Pros**: Conceptually simpler to understand for beginners.
        *   **Cons**:
            *   **Space Complexity**: O(N) in the worst case, as it might store all N nodes if there's no cycle or a very long cycle. This is less efficient than Floyd's O(1) space.
            *   **Time Complexity**: O(N) on average (due to hash set operations), similar to Floyd's.
    2.  **Modifying Node Values/Pointers**:
        *   **Approach**: If allowed to modify the list, you could change the `next` pointer of each visited node to point to a sentinel node, or even to itself. Or, change the node's value to a special marker. If you encounter a node with the marker, a cycle exists.
        *   **Pros**: O(1) space complexity.
        *   **Cons**: Modifies the original data structure, which is often undesirable and can have side effects if other parts of the program rely on the original list structure or values. Not suitable for immutable lists or concurrent access.

### 7. Security/Performance Notes

*   **Performance**: This implementation is highly performant. Its O(N) time complexity is optimal because, in the worst case, you must visit every node at least once to confirm the absence of a cycle. Its O(1) space complexity is also optimal, as it uses no auxiliary data structures.
*   **Security**: For this specific algorithm, there are no direct security implications. It operates on the structure of a linked list within a single process and doesn't involve external inputs or sensitive data handling that would introduce typical security vulnerabilities. It's a purely algorithmic solution.
*   **Resource Usage**: The algorithm is very light on resource usage, making it suitable for environments with strict memory constraints or for processing very large linked lists where O(N) space solutions would be prohibitive.

### Code:
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not head or not head.next:
            return False

        slow = head
        fast = head

        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                return True
        
        return False
```
