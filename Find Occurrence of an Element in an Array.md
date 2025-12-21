## Find Occurrence of an Element in an Array
**Language:** python
**Tags:** python,oop,list,linear search

### Description:
This code snippet provides a solution to find the indices of specific occurrences of an element `x` within a list `nums`, based on a list of 1-indexed queries. It employs a common pre-computation strategy to optimize query processing.

---

### 1. Overview & Intent

The primary goal of this code is to efficiently answer multiple queries about the positions of a specific element `x` in a given list `nums`.
Specifically, for each query `q` (which is 1-indexed), the function should return the 0-indexed position of the `q`-th occurrence of `x` in `nums`. If the `q`-th occurrence does not exist, it should return `-1`.

The approach taken is to first pre-process the `nums` list to find all indices where `x` appears, store them in an ordered list, and then answer each query by a direct lookup into this pre-computed list. This is a classic time-space trade-off where extra space is used to reduce the time complexity of individual queries.

### 2. How It Works

The function operates in two main phases:

1.  **Pre-computation of `x_indices`**:
    *   An empty list `x_indices` is initialized.
    *   The code iterates through the input list `nums` from the first element to the last, keeping track of the current index `i`.
    *   For each element `nums[i]`, it checks if `nums[i]` is equal to the target value `x`.
    *   If they are equal, the current index `i` is appended to the `x_indices` list.
    *   After this loop, `x_indices` will contain all 0-indexed positions where `x` appears in `nums`, in ascending order.

2.  **Answering Queries**:
    *   An empty list `answer` is initialized to store the results for each query.
    *   The code then iterates through each `q` in the `queries` list.
    *   Since `q` represents a 1-indexed occurrence (e.g., `q=1` means the first occurrence), it's converted to a 0-indexed list index by subtracting 1: `target_idx = q - 1`.
    *   It then checks if `target_idx` is a valid index within the `x_indices` list. This check ensures two things:
        *   `target_idx` is not negative (i.e., `q` is not 0 or negative).
        *   `target_idx` is less than the total number of occurrences of `x` (i.e., `q` is not greater than `len(x_indices)`).
    *   If `target_idx` is valid, the value `x_indices[target_idx]` (which is the actual 0-indexed position of the `q`-th `x`) is appended to the `answer` list.
    *   If `target_idx` is not valid (meaning the `q`-th occurrence of `x` does not exist), `-1` is appended to the `answer` list.
    *   Finally, the `answer` list containing all results is returned.

### 3. Key Design Decisions

*   **Pre-computation Strategy**: The most significant design decision is to pre-compute all occurrences of `x` into a separate list (`x_indices`). This allows for O(1) lookup for each query after the initial O(N) scan, which is highly efficient when there are many queries (`M`).
*   **Data Structure for `x_indices`**: A Python `list` (which is implemented as a dynamic array) is chosen for `x_indices`. This is an excellent choice because:
    *   It allows efficient `append` operations (amortized O(1)) during the initial scan.
    *   It provides O(1) random access by index, which is crucial for answering queries quickly.
    *   It naturally maintains the order of indices, which is necessary for `q`-th occurrence logic.
*   **Handling 1-indexed Queries**: The code explicitly converts the 1-indexed query `q` to a 0-indexed list index `q - 1`. This is a common pattern when interfacing with 1-indexed requirements using 0-indexed data structures.
*   **Handling Non-existent Occurrences**: The problem specifies returning `-1` for non-existent occurrences. The code correctly implements this by checking the bounds of `x_indices` before attempting an access.

### 4. Complexity Analysis

Let `N` be the length of `nums` and `M` be the length of `queries`.

*   **Time Complexity**:
    *   **Building `x_indices`**: The first loop iterates through `nums` once. Appending to a Python list is amortized O(1). Therefore, this phase takes O(N) time.
    *   **Answering `queries`**: The second loop iterates through `queries` once. Each query involves an O(1) index calculation, an O(1) bounds check, and an O(1) list lookup (or appending -1). Appending to the `answer` list is also amortized O(1). Therefore, this phase takes O(M) time.
    *   **Total Time Complexity**: O(N + M). This is optimal as we must at least read all of `nums` once and process all `M` queries.

*   **Space Complexity**:
    *   **`x_indices`**: In the worst case, `x` could be every element in `nums` (e.g., `nums = [1, 1, 1]`, `x = 1`). In this scenario, `x_indices` would store `N` integers. So, O(N) space.
    *   **`answer`**: This list stores `M` integers, one for each query. So, O(M) space.
    *   **Total Space Complexity**: O(N + M).

### 5. Edge Cases & Correctness

The code handles various edge cases correctly:

*   **`nums` is empty**: `len(nums)` is 0. The first loop won't run, `x_indices` will remain empty. All queries will correctly fall into the `else` block and append `-1`.
*   **`queries` is empty**: The second loop won't run. `answer` will remain empty, which is the correct result.
*   **`x` is not present in `nums`**: `x_indices` will be empty. All queries will correctly result in `-1` because `len(x_indices)` will be 0, failing the `0 <= q - 1 < len(x_indices)` condition.
*   **`x` appears only once**: `x_indices` will contain a single index. Queries for `q=1` will return this index; all other queries will return `-1`.
*   **`q=1` (first occurrence)**: `q - 1` becomes `0`, correctly accessing `x_indices[0]`.
*   **`q` is very large (out of bounds)**: If `q` is greater than the total number of occurrences of `x`, `q - 1` will be `>= len(x_indices)`. The condition `0 <= q - 1 < len(x_indices)` will correctly evaluate to `False`, leading to `-1` being appended.
*   **`q` is negative or zero**: If `q` is `0` or negative, `q - 1` will be negative. The condition `0 <= q - 1` will correctly evaluate to `False`, leading to `-1` being appended. This demonstrates robustness beyond typical problem constraints where `q` is usually positive.

### 6. Improvements & Alternatives

*   **Readability/Conciseness (Pythonic Style)**:
    The code is already quite readable. For more conciseness, especially in Python, list comprehensions can be used:

    ```python
    class Solution:
        def occurrencesOfElement(self, nums: List[int], queries: List[int], x: int) -> List[int]:
            # Pre-computation using list comprehension
            x_indices = [i for i, num in enumerate(nums) if num == x]

            # Answering queries using list comprehension
            answer = [x_indices[q - 1] if 0 <= q - 1 < len(x_indices) else -1 for q in queries]

            return answer
    ```
    This version achieves the same functionality with fewer lines of code and is often considered more "Pythonic." However, the original explicit loops are perfectly clear and sometimes preferred for beginners or for very complex logic.

*   **Performance (Minor/Specific Scenarios)**:
    The current `O(N+M)` solution is generally optimal. There are no significant performance improvements possible for the general case without changing the problem constraints (e.g., if `nums` was sorted, or if `x` was guaranteed to be unique).

    *   If `N` is extremely large, `M` is very small, and `x` is extremely rare, one *could* argue for processing each query by iterating `nums` until the `q`-th occurrence is found. However, this would lead to a worst-case `O(N*M)` time complexity, which is generally much worse than `O(N+M)`. The current pre-computation approach is superior for most practical scenarios where `M` is not trivial.

### 7. Security/Performance Notes

*   **Security**: There are no direct security implications. The code operates on primitive data types (integers, lists) and does not interact with external systems, files, or user input in a way that could introduce vulnerabilities like injection attacks or information leakage.
*   **Performance**: The chosen algorithm is efficient. The time complexity of O(N+M) and space complexity of O(N+M) are generally considered optimal for this problem. There are no obvious performance anti-patterns or bottlenecks. The use of Python lists for `x_indices` and `answer` is appropriate and efficient for the operations performed.

### Code:
```python
class Solution:
    def occurrencesOfElement(self, nums: List[int], queries: List[int], x: int) -> List[int]:
        x_indices = []
        for i in range(len(nums)):
            if nums[i] == x:
                x_indices.append(i)

        answer = []
        for q in queries:
            # queries[i] is 1-indexed, so we need q-1 for 0-indexed list
            if 0 <= q - 1 < len(x_indices):
                answer.append(x_indices[q - 1])
            else:
                answer.append(-1)
        
        return answer
```
