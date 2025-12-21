## Sum of Mutated Array Closest to Target
**Language:** python
**Tags:** python,object-oriented,binary search,prefix sum,sorting

### Description:
This code implements an efficient solution to find an integer `value` that minimizes the absolute difference between a modified array's sum and a given `target`. The modification rule is: any element in the array `arr` that is greater than `value` is replaced by `value`. If multiple `value`s yield the same minimum difference, the smallest such `value` should be chosen.

---

### 1. Overview & Intent

The problem asks us to find an optimal integer `value` that, when used to cap elements in an input array `arr`, makes the sum of the modified array as close as possible to a given `target`. The "capping" means any element `x` in `arr` such that `x > value` is replaced by `value`; elements `x <= value` remain unchanged. In case of a tie (multiple `value`s result in the same minimum absolute difference from `target`), the smallest `value` is preferred.

The core idea behind this solution is that the sum of the modified array is a monotonically increasing function of `value`. This monotonic property allows us to use binary search to efficiently find the optimal `value`.

---

### 2. How It Works

The solution proceeds in three main steps:

1.  **Preprocessing (Sorting and Prefix Sums):**
    *   `arr` is first sorted in non-decreasing order. This is crucial for two reasons:
        *   It enables the use of `bisect_left` to quickly find the split point for `value`.
        *   It allows for efficient calculation of sums of initial segments using prefix sums.
    *   `prefix_sums` is created. `prefix_sums[i]` stores the sum of the first `i` elements of the *sorted* `arr`. This allows calculating the sum of `arr[0...k-1]` in O(1) time as `prefix_sums[k]`.

2.  **`calculate_current_sum(val)` Helper Function:**
    *   This function takes a candidate `val` and computes the sum of the array if all elements greater than `val` were replaced by `val`.
    *   It uses `bisect.bisect_left(arr, val)` to find `idx`, the index of the first element in `arr` that is greater than or equal to `val`.
    *   Elements `arr[0]` through `arr[idx-1]` are all less than `val` (or equal to `val` if `val` is present at `idx`). These elements remain unchanged. Their sum is efficiently retrieved as `prefix_sums[idx]`.
    *   Elements `arr[idx]` through `arr[n-1]` are all greater than or equal to `val`. According to the rule, if `arr[i] > val`, it becomes `val`. If `arr[i] == val`, it remains `val`. So, all `n - idx` elements from `arr[idx]` onwards effectively become `val`. Their sum is `val * (n - idx)`.
    *   The total sum is `sum_unchanged + sum_changed`.

3.  **Binary Search for Optimal `value`:**
    *   The search space for `value` is defined by `low = 0` and `high = arr[-1]`.
        *   `0` is the minimum possible `value`.
        *   `arr[-1]` (the maximum element in the original array) is a suitable upper bound. Any `value` greater than `arr[-1]` would result in the same modified array sum as `value = arr[-1]` (because all elements are already less than or equal to `arr[-1]`, so no elements would be capped by a `value` greater than `arr[-1]`).
    *   The binary search iteratively calculates `mid = low + (high - low) // 2`.
    *   It calls `calculate_current_sum(mid)` to get the sum for the current `mid` value.
    *   It calculates `current_diff = abs(current_sum - target)`.
    *   **Update Logic:**
        *   If `current_diff < min_diff`, it means `mid` is a better candidate, so `min_diff` and `best_val` are updated.
        *   If `current_diff == min_diff`, it means `mid` is equally good. The problem requires choosing the *smallest* `value` in case of a tie, so `best_val` is updated to `min(best_val, mid)`.
    *   **Search Range Adjustment:**
        *   If `current_sum < target`, we need a larger sum. Since `calculate_current_sum` is monotonic, increasing `value` will increase the sum. So, `low` is moved to `mid + 1`.
        *   If `current_sum >= target`, we have met or exceeded the target. We might be able to get closer by trying a smaller `value`. So, `high` is moved to `mid - 1`.
    *   The loop continues until `low > high`, at which point `best_val` holds the optimal `value`.

---

### 3. Key Design Decisions

*   **Sorting `arr`**: Essential for both `bisect_left` (which requires a sorted array) and for the efficient calculation of prefix sums. Without sorting, `bisect_left` would be incorrect, and calculating sums of elements less than `val` would require iterating through the array, making `calculate_current_sum` O(N) instead of O(log N).
*   **Prefix Sums**: This data structure allows for O(1) retrieval of the sum of any initial segment of the sorted array. This is critical for `calculate_current_sum` to be efficient.
*   **Binary Search on `value`**: The core algorithmic choice. The function `f(value) = calculate_current_sum(value)` is monotonically non-decreasing. This property allows us to use binary search to find the `value` that makes `f(value)` closest to `target`. Iterating through all possible `value`s (from 0 to `max(arr)`) would be too slow if `max(arr)` is large.
*   **`bisect.bisect_left`**: This standard library function efficiently finds the insertion point for `val` in `arr` while maintaining sorted order. It's an O(log N) operation, crucial for the overall efficiency.
*   **Tie-breaking Rule**: The problem explicitly states to choose the smallest `value` if multiple `value`s result in the same minimum difference. The code `best_val = min(best_val, mid)` correctly implements this. It's important to initialize `best_val` to 0 (or a sufficiently small value) and `min_diff` to `float('inf')` to ensure correct updates.
*   **Binary Search Range `[0, arr[-1]]`**: This range covers all relevant `value`s. `0` is the absolute minimum. `arr[-1]` (the largest element in the original array) is the maximum relevant `value` because any `value` greater than `arr[-1]` would result in the same modified sum as `arr[-1]` (no elements would be capped).

---

### 4. Complexity Analysis

Let `N` be the length of `arr` and `MaxVal` be the maximum value in `arr`.

*   **Time Complexity**:
    *   `arr.sort()`: O(N log N).
    *   `prefix_sums` calculation: O(N).
    *   Binary search loop: The search range is `[0, MaxVal]`. So, the loop runs `O(log MaxVal)` times.
        *   Inside the loop, `calculate_current_sum(mid)` is called.
        *   `bisect.bisect_left(arr, val)` takes O(log N) time because `arr` is sorted.
        *   Prefix sum lookup is O(1).
        *   Therefore, each iteration of the binary search loop takes O(log N) time.
    *   Total Time Complexity: O(N log N + log MaxVal * log N).
        *   Since `N` can be up to 10^4 and `MaxVal` up to 10^5, `N log N` (e.g., 10^4 * 14 = 1.4 * 10^5) typically dominates `log MaxVal * log N` (e.g., 17 * 14 = 238).
        *   Thus, the overall time complexity is **O(N log N)**.

*   **Space Complexity**:
    *   `arr.sort()`: Python's Timsort uses O(N) space in the worst case (or O(log N) for in-place merge sort variants).
    *   `prefix_sums`: O(N) space to store `N+1` integers.
    *   Other variables: O(1).
    *   Total Space Complexity: **O(N)**.

---

### 5. Edge Cases & Correctness

*   **Empty `arr`**: The code would raise an `IndexError` at `arr[-1]`. Problem constraints usually guarantee `arr` is non-empty. If not, an explicit check for `n == 0` should be added.
*   **`arr` with one element**: Works correctly. `prefix_sums` will have two elements. `bisect_left` and the binary search will function as expected.
*   **All elements are smaller than `target`**: The `value` will likely be `arr[-1]`, resulting in the sum of the original array.
*   **All elements are larger than `target`**: The `value` will likely be small (possibly 0), capping all elements to a small value to reduce the sum.
*   **`target` is 0**: `value` will likely be 0, making the sum 0.
*   **`target` is very large**: `value` will likely be `arr[-1]`, making the sum `sum(arr)`.
*   **`target` exactly matches a possible sum**: The binary search will converge to a `value` that produces this sum, and the `min_diff` will be 0.
*   **Tie-breaking**: The `elif current_diff == min_diff: best_val = min(best_val, mid)` correctly handles the requirement to choose the smallest `value` in case of a tie. This is crucial for correctness.
*   **Binary Search Range**: `low = 0` and `high = arr[-1]` correctly define the search space for `value`. `0` is the smallest possible `value`. `arr[-1]` is the largest relevant `value` because any `value` greater than `arr[-1]` would result in the same sum as `arr[-1]`.
*   **Integer Division**: `low + (high - low) // 2` correctly calculates the midpoint, preventing potential integer overflow if `low + high` were to exceed the maximum integer value (though less of a concern in Python) and ensuring correct integer division.

---

### 6. Improvements & Alternatives

*   **Readability**:
    *   Add a class docstring and method docstring explaining the purpose, parameters, and return value.
    *   Consider more descriptive variable names for `low` and `high` in the binary search, e.g., `min_possible_value` and `max_possible_value_to_check`.
*   **Robustness**:
    *   If `arr` could be empty, add a check at the beginning: `if not arr: return 0` (or raise an error, depending on problem spec).
*   **Minor Optimization for `high` bound**: The upper bound for `value` could potentially be `max(arr)` or even `target` (if `target` is smaller than `max(arr)`), as setting `value` higher than `target` would only increase the sum further from `target` if `current_sum` is already greater than `target`. However, `arr[-1]` is simple and correct, and the `log MaxVal` factor is small.
*   **Alternative Search Algorithm**:
    *   **Ternary Search**: The function `g(value) = abs(calculate_current_sum(value) - target)` is unimodal (first decreasing, then increasing). Ternary search could be applied here. However, binary search is generally preferred for its simpler implementation and similar asymptotic complexity (O(log N)). Binary search can be adapted to find the minimum of a unimodal function by checking `mid` and `mid+1` values. The current binary search implementation effectively finds the "pivot" point where the sum crosses the target, and then checks values around it, which is also a valid approach for unimodal functions.

---

### 7. Security/Performance Notes

*   **Security**: This code is purely algorithmic and does not interact with external systems, user input, or sensitive data, so there are no direct security implications.
*   **Performance**: The solution is highly optimized. The O(N log N) time complexity is generally the best one can achieve for problems involving sorted arrays and efficient lookups. The use of `bisect` and prefix sums demonstrates good algorithmic choices for performance. No obvious performance bottlenecks exist within the current implementation given the problem constraints.

### Code:
```python
import bisect
from typing import List

class Solution:
    def findBestValue(self, arr: List[int], target: int) -> int:
        arr.sort()
        n = len(arr)
        
        # Calculate prefix sums for efficient sum calculation
        prefix_sums = [0] * (n + 1)
        for i in range(n):
            prefix_sums[i+1] = prefix_sums[i] + arr[i]

        # Helper function to calculate the sum of the modified array for a given 'value'
        def calculate_current_sum(val):
            # Find the index 'idx' where elements start to be greater than 'val'
            # All elements arr[0]...arr[idx-1] are <= val and remain unchanged
            # All elements arr[idx]...arr[n-1] are > val and become 'val'
            idx = bisect.bisect_left(arr, val)
            
            sum_unchanged = prefix_sums[idx]
            sum_changed = val * (n - idx)
            return sum_unchanged + sum_changed

        # Binary search for the optimal 'value'
        # The search range for 'value' is from 0 to the maximum element in arr.
        # If 'value' is greater than max(arr), the sum will be sum(arr).
        # In case of a tie, we need the minimum 'value', so max(arr) is preferred over any 'value' > max(arr).
        low = 0
        high = arr[-1] 
        
        best_val = 0
        min_diff = float('inf')

        while low <= high:
            mid = low + (high - low) // 2
            current_sum = calculate_current_sum(mid)
            current_diff = abs(current_sum - target)

            # Update best_val if current_diff is smaller
            # Or if current_diff is equal but 'mid' is smaller (tie-breaking rule)
            if current_diff < min_diff:
                min_diff = current_diff
                best_val = mid
            elif current_diff == min_diff:
                best_val = min(best_val, mid)

            # Adjust search range based on current_sum compared to target
            if current_sum < target:
                # We need a larger sum, so increase 'value'
                low = mid + 1
            else: # current_sum >= target
                # We have met or exceeded the target sum, try a smaller 'value'
                high = mid - 1
        
        return best_val
```
