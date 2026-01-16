## Count Subarrays With Median K
**Language:** python
**Tags:** python,oop,prefix sums,hashmap

### Description:
This code solves the problem of counting subarrays where a given integer `k` is the median. The problem defines the median of a sorted array as the middle element if the array has an odd length, or the left of the two middle elements if the array has an even length.

---

### 1. Overview & Intent

The core idea is to transform the original array `nums` into a new array `arr` where elements are represented by `1`, `-1`, or `0` based on their comparison with `k`. Specifically:
*   `1` if `num > k`
*   `-1` if `num < k`
*   `0` if `num == k` (though `k` itself is handled implicitly by its position).

With this transformation, the sum of elements in any subarray of `arr` (excluding the `0` at `idx_k`) represents `(count_greater_than_k - count_less_than_k)`. For `k` to be the median of a subarray, this difference must be either `0` or `1`:
*   **0**: If the subarray has an odd length, `k` is the exact middle. This implies an equal number of elements greater than `k` and less than `k`.
*   **1**: If the subarray has an even length, `k` is the left of the two middle elements. This implies one more element greater than `k` than elements less than `k`.

The algorithm efficiently counts these subarrays by finding the index of `k` (`idx_k`), then calculating prefix sums for the parts of subarrays to its left and right, using a hash map to store frequencies of left-part sums.

---

### 2. How It Works

1.  **Locate `k`**: The code first iterates through `nums` to find the index `idx_k` where `nums[idx_k] == k`. This is crucial because any subarray for which `k` is the median *must* contain `k`.

2.  **Transform `nums` to `arr`**: A new array `arr` of the same size as `nums` is created. Each element `nums[i]` is mapped to `arr[i]` as follows:
    *   If `nums[i] > k`, `arr[i] = 1`.
    *   If `nums[i] < k`, `arr[i] = -1`.
    *   If `nums[i] == k`, `arr[i] = 0`.
    This transformation allows us to convert the median condition into a sum condition.

3.  **Calculate Left Prefix Sums**:
    *   A `collections.defaultdict(int)` named `freq` is initialized. This map will store the counts of different prefix sums encountered when traversing *leftwards* from `idx_k - 1`.
    *   `freq[0] = 1` is set initially. This accounts for the "empty prefix" to the left of `idx_k`, which is necessary for subarrays that start at `idx_k` itself (i.e., `nums[idx_k...j]`).
    *   The code then iterates from `i = idx_k - 1` down to `0`. In each step, `current_sum_left` is updated by adding `arr[i]`, and its frequency is incremented in `freq`. So, `freq[S]` stores how many times the sum `S` was encountered for `sum(arr[i...idx_k-1])`.

4.  **Calculate Right Prefix Sums and Count Subarrays**:
    *   A `count` variable is initialized to `0`.
    *   `current_sum_right` is initialized to `0`. This will accumulate sums for elements *to the right* of `k`.
    *   The code iterates from `j = idx_k` up to `n - 1`.
    *   For each `j`:
        *   If `j > idx_k`, `arr[j]` is added to `current_sum_right`. This ensures `current_sum_right` represents `sum(arr[idx_k+1...j])`. When `j == idx_k`, `current_sum_right` remains `0`, corresponding to subarrays ending at `idx_k`.
        *   Now, for a subarray `nums[i...j]` (where `i <= idx_k <= j`), the sum of its transformed elements (excluding `arr[idx_k]`) is `S_left + current_sum_right`.
        *   We need this total sum to be `0` or `1`.
        *   Therefore, we need `S_left = -current_sum_right` or `S_left = 1 - current_sum_right`.
        *   The code looks up these required `S_left` values in the `freq` map and adds their counts to the total `count`.
    *   Finally, `count` holds the total number of subarrays where `k` is the median.

---

### 3. Key Design Decisions

*   **Transformation to `1`, `-1`, `0`**: This is the most critical design decision. It cleverly converts a "median" problem into a "sum" problem. The relative order of elements with respect to `k` is all that matters, not their absolute values.
*   **Splitting at `idx_k`**: Since `k` must be part of the median subarray, fixing its position (`idx_k`) allows the problem to be broken down into finding suitable "left parts" (`nums[i...idx_k-1]`) and "right parts" (`nums[idx_k+1...j]`).
*   **Prefix Sums with Hash Map (`freq`)**: This is a standard technique for efficiently counting subarrays with a specific sum. By pre-calculating and storing frequencies of sums for the left part, we can quickly find matching left parts for any given right part in O(1) average time.
*   **`freq[0] = 1` Initialization**: This handles the base case where the left part of the subarray is empty (i.e., the subarray starts at `idx_k`). An empty left part has a sum of `0`, and there's one such way to achieve it.
*   **Iterating Left and Right**: The two separate loops (one for left sums, one for right sums) are designed to efficiently combine the sums. The left loop populates the `freq` map, and the right loop uses this map to find matches.

---

### 4. Complexity Analysis

*   **Time Complexity**:
    *   Finding `idx_k`: O(N) in the worst case (if `k` is at the end or not present).
    *   Transforming `nums` to `arr`: O(N).
    *   Calculating left sums and populating `freq`: The loop runs `idx_k` times, and hash map operations (insertion, lookup) are O(1) on average. So, O(N).
    *   Calculating right sums and counting: The loop runs `n - idx_k` times, and hash map lookups are O(1) on average. So, O(N).
    *   **Total Time Complexity: O(N)**.

*   **Space Complexity**:
    *   `arr`: O(N) to store the transformed array.
    *   `freq`: In the worst case, all prefix sums could be distinct, leading to `N` entries in the hash map. So, O(N).
    *   **Total Space Complexity: O(N)**.

---

### 5. Edge Cases & Correctness

*   **`k` not in `nums`**: The problem statement for LeetCode 2488 (which this code solves) guarantees that `k` is present in `nums`. If `k` were not guaranteed, `idx_k` would remain `-1`, causing `range(idx_k - 1, -1, -1)` to be `range(-2, -1, -1)` (an empty range), and `range(idx_k, n)` to be `range(-1, n)` (which would iterate from `-1` to `n-1`, leading to `IndexError` for `arr[j]` if `j` is negative). A robust solution would check `if idx_k == -1: return 0`.
*   **`nums` has one element (e.g., `[5]`, `k=5`)**:
    *   `idx_k = 0`.
    *   `arr = [0]`.
    *   `freq = {0: 1}` (left loop is empty).
    *   Right loop `j=0`: `current_sum_right = 0`. `count += freq[0] + freq[1] = 1 + 0 = 1`. Correct, `[5]` is a subarray where `5` is the median.
*   **All elements are `k` (e.g., `[3,3,3]`, `k=3`)**:
    *   `idx_k = 0`.
    *   `arr = [0,0,0]`.
    *   `freq = {0: 1}`.
    *   Right loop:
        *   `j=0`: `current_sum_right = 0`. `count += freq[0] + freq[1] = 1`. (Subarray `[3]` at index 0).
        *   `j=1`: `current_sum_right = 0` (since `arr[1]` is `0`). `count += freq[0] + freq[1] = 1`. (Subarray `[3,3]` at index 0).
        *   `j=2`: `current_sum_right = 0` (since `arr[2]` is `0`). `count += freq[0] + freq[1] = 1`. (Subarray `[3,3,3]` at index 0).
    *   Total `count = 3`. This is correct, as `[3]`, `[3,3]`, `[3,3,3]` are all valid subarrays where `3` is the median (using the "left of two middle" definition for even length).
*   **Correctness of `0` and `1` targets**: The logic correctly implements the median definition. If `k` is the median of a subarray (excluding `k` itself):
    *   If `count(>k) == count(<k)`, then `count(>k) - count(<k) = 0`. This applies to odd-length subarrays.
    *   If `count(>k) == count(<k) + 1`, then `count(>k) - count(<k) = 1`. This applies to even-length subarrays where `k` is the left median.
    The transformation and sum logic perfectly capture these conditions.

---

### 6. Improvements & Alternatives

*   **Readability**:
    *   Rename `arr` to something more descriptive like `relative_values` or `comparison_array`.
    *   Rename `freq` to `left_sum_counts` or `prefix_sum_frequencies`.
    *   Add more explicit comments explaining the `0` and `1` target sums in relation to odd/even subarray lengths and the median definition.
*   **Robustness**: While problem constraints usually guarantee `k` is present, adding an explicit check for `idx_k == -1` at the beginning and raising an error or returning `0` would make the code more robust for general use cases.
*   **Alternative Approaches**:
    *   **Brute Force**: Iterate over all `O(N^2)` subarrays, and for each, sort it or count elements relative to `k` to check the median condition. This would be `O(N^3 log N)` or `O(N^3)` respectively, far too slow.
    *   **Two Pointers**: Not directly applicable here because the condition depends on the counts of elements relative to `k` across a potentially non-contiguous range from the perspective of two pointers. The fixed `idx_k` is key.
    *   The current approach is optimal in terms of time complexity (O(N)) and is a standard technique for this class of problems.

---

### 7. Security/Performance Notes

*   **Security**: There are no direct security concerns with this code. It processes numerical input without external dependencies or sensitive operations.
*   **Performance**: The code is highly optimized with O(N) time and space complexity, which is the best possible for problems requiring a full scan of the input array. There are no obvious performance bottlenecks or areas for micro-optimization that would yield significant gains without changing the fundamental algorithm. The use of `collections.defaultdict` is efficient for hash map operations.

### Code:
```python
import collections
from typing import List

class Solution:
    def countSubarrays(self, nums: List[int], k: int) -> int:
        n = len(nums)
        idx_k = -1
        for i in range(n):
            if nums[i] == k:
                idx_k = i
                break

        arr = [0] * n
        for i in range(n):
            if nums[i] > k:
                arr[i] = 1
            elif nums[i] < k:
                arr[i] = -1
           
        freq = collections.defaultdict(int)
        current_sum_left = 0
        
        freq[0] = 1 
        
        # Iterate from idx_k-1 down to 0 to calculate sums of left parts
        for i in range(idx_k - 1, -1, -1):
            current_sum_left += arr[i]
            freq[current_sum_left] += 1

        count = 0
        current_sum_right = 0
        
        # Iterate from idx_k up to n-1 to calculate sums of right parts
        # current_sum_right represents sum(arr[idx_k+1...j])
        for j in range(idx_k, n):
            if j > idx_k: # Only add elements to the right of k
                current_sum_right += arr[j]

            
            count += freq[-current_sum_right]
            count += freq[1 - current_sum_right]
            
        return count
```
