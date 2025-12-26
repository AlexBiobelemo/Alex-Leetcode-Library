## Subarrays with K Different Integers
**Language:** python
**Tags:** python,oop,sliding window,hashmap,two-pointers

### Description:
This code addresses a classic sliding window problem, efficiently counting subarrays with a specific property. Let's break it down.

---

### 1. Overview & Intent

The primary goal of the `subarraysWithKDistinct` function is to count the number of subarrays within a given integer array `nums` that contain **exactly `k` distinct integers**.

The core strategy employed is a common and powerful technique for "exactly K" problems:
`count(exactly K) = count(at most K) - count(at most K-1)`

This transforms the problem into two simpler sub-problems, each solvable using a standard sliding window approach to count subarrays with "at most `k_val`" distinct integers.

---

### 2. How It Works

The solution is structured with a main function and a helper function:

**A. `subarraysWithKDistinct(nums, k)` (Main Function)**
1.  It calls the helper function `count_at_most_k(nums, k)` to determine the total number of subarrays that have `k` or fewer distinct integers.
2.  It then calls `count_at_most_k(nums, k - 1)` to determine the total number of subarrays that have `k - 1` or fewer distinct integers.
3.  Finally, it subtracts the second result from the first. The difference precisely yields the count of subarrays that have *exactly* `k` distinct integers.

**B. `count_at_most_k(arr, k_val)` (Helper Function)**
This function counts all subarrays in `arr` that contain *at most* `k_val` distinct integers.

1.  **Base Case**: If `k_val` is less than or equal to 0, it immediately returns 0. This handles cases where `k` is non-positive, as no meaningful subarray can have 0 or fewer distinct elements.
2.  **Initialization**:
    *   `count = 0`: This variable will accumulate the total number of valid subarrays.
    *   `left = 0`: This is the left pointer of our sliding window.
    *   `freq_map = collections.defaultdict(int)`: A hash map (dictionary) to store the frequency of each number within the current sliding window `[left...right]`. `defaultdict(int)` conveniently initializes new keys to 0.
    *   `distinct_count = 0`: Tracks the number of unique integers currently present in the sliding window.
3.  **Sliding Window Iteration**:
    *   The `right` pointer iterates from `0` to `len(arr) - 1`, expanding the window to the right.
    *   **Expand Window**:
        *   `if freq_map[arr[right]] == 0:`: If the element `arr[right]` is encountered for the first time in the current window, increment `distinct_count`.
        *   `freq_map[arr[right]] += 1`: Increment the frequency of the element `arr[right]` in the map.
    *   **Shrink Window**:
        *   `while distinct_count > k_val:`: If the number of distinct elements in the window exceeds `k_val`, the window is invalid. We need to shrink it from the left until it becomes valid again.
        *   `freq_map[arr[left]] -= 1`: Decrement the frequency of the element at the `left` pointer.
        *   `if freq_map[arr[left]] == 0:`: If its frequency drops to 0, it means this element is no longer present in the window, so decrement `distinct_count`.
        *   `left += 1`: Move the `left` pointer one step to the right.
    *   **Count Valid Subarrays**:
        *   `count += (right - left + 1)`: After the `while` loop, the window `[left...right]` is guaranteed to have `distinct_count <= k_val`. At this point, *every* subarray that ends at `right` and starts at or after `left` (i.e., `[left...right]`, `[left+1...right]`, ..., `[right...right]`) is a valid subarray. The number of such subarrays is `(right - left + 1)`. This is the key insight for "at most K" problems: if a window `[left...right]` is valid, all its sub-windows ending at `right` are also valid because they cannot have *more* distinct elements.

---

### 3. Key Design Decisions

*   **Sliding Window Pattern**: This is the fundamental algorithm choice. It allows for efficient processing of all possible subarrays in a single pass without redundant computations.
*   **"Exactly K" via "At Most K" Subtraction**: This is the most crucial design decision. It simplifies a potentially complex "exactly K" problem into two instances of a more straightforward "at most K" problem, which has a well-known sliding window solution.
*   **`collections.defaultdict(int)`**: This data structure simplifies frequency counting within the window. It automatically initializes the count of a new element to 0, avoiding explicit `if key in map` checks.
*   **Two Pointers (`left`, `right`)**: These pointers define the current window and enable its dynamic expansion and contraction.
*   **Monotonicity**: The `distinct_count` property exhibits monotonicity. As the window expands, `distinct_count` either stays the same or increases. As it shrinks, `distinct_count` either stays the same or decreases. This property ensures the `while` loop correctly maintains the "at most `k_val`" invariant.

---

### 4. Complexity Analysis

*   **Time Complexity: O(N)**
    *   The `count_at_most_k` function involves a single pass of the `right` pointer through the `arr` (N iterations). The `left` pointer also traverses the array at most once. Each element is added to and removed from `freq_map` at most once. Dictionary operations (insertion, deletion, lookup) take O(1) time on average.
    *   Since the main function calls `count_at_most_k` twice, the total time complexity is O(N) + O(N) = O(N), where N is the length of `nums`.

*   **Space Complexity: O(N)**
    *   The `freq_map` stores the frequencies of elements within the current window. In the worst case (e.g., all elements in `nums` are distinct), it might store up to N unique elements.
    *   Therefore, the space complexity is O(N). If the range of possible values in `nums` is small and bounded (e.g., 0-100), the space complexity would be O(range_size).

---

### 5. Edge Cases & Correctness

*   **Empty `nums` array**:
    *   `len(arr)` will be 0. The `for` loop in `count_at_most_k` won't execute. It will return 0.
    *   `subarraysWithKDistinct` will return `0 - 0 = 0`. This is correct, as an empty array has no subarrays.
*   **`k = 0`**:
    *   `count_at_most_k(nums, 0)` returns 0 due to the initial check.
    *   `count_at_most_k(nums, -1)` also returns 0.
    *   Result: `0 - 0 = 0`. Correct, as there are no subarrays with exactly 0 distinct elements (assuming non-empty subarrays).
*   **`k = 1`**:
    *   `count_at_most_k(nums, 1)` counts subarrays with at most 1 distinct element.
    *   `count_at_most_k(nums, 0)` returns 0.
    *   The result correctly counts subarrays like `[1]`, `[1,1]`, `[2]`, `[2,2,2]`, etc.
*   **`k > len(nums)`**:
    *   If `k` is greater than the total number of distinct elements in `nums`, there can be no subarray with *exactly* `k` distinct elements.
    *   `count_at_most_k(nums, k)` will count all possible subarrays (as `distinct_count` will never exceed `k`).
    *   `count_at_most_k(nums, k-1)` will also count all possible subarrays (or fewer, but the difference will still be 0 if `k` is too large).
    *   The subtraction will correctly yield 0.
*   **All elements are the same (e.g., `nums = [1,1,1,1]`, `k=1`)**:
    *   `count_at_most_k(nums, 1)` will correctly count all subarrays (10 in this case: `[1]`, `[1,1]`, `[1,1,1]`, `[1,1,1,1]` and their shifted versions).
    *   `count_at_most_k(nums, 0)` returns 0.
    *   Result: `10 - 0 = 10`. Correct.
*   **Correctness of `count += (right - left + 1)`**: This is the most subtle part. When the window `[left...right]` satisfies `distinct_count <= k_val`, it means all subarrays `[i...right]` where `left <= i <= right` also satisfy this condition. This is because any such subarray `[i...right]` is a sub-window of `[left...right]` and thus cannot have more distinct elements than `[left...right]`. The number of such valid starting points `i` is `right - left + 1`. This logic is sound and forms the basis of the "at most K" sliding window.

---

### 6. Improvements & Alternatives

*   **Readability**: The code is already quite readable due to clear variable names and the well-defined helper function. A comment explaining the `count += (right - left + 1)` line could further enhance understanding for those unfamiliar with this specific sliding window trick.
*   **Alternative "Direct" Approach (More Complex)**:
    It's possible to solve the "exactly K" problem in a single pass by maintaining two left pointers: `left1` for the window with *at most K* distinct elements, and `left2` for the window with *at most K-1* distinct elements. The number of subarrays ending at `right` with exactly K distinct elements would then be `left2 - left1`.
    ```python
    import collections

    class Solution:
        def subarraysWithKDistinct(self, nums: List[int], k: int) -> int:
            if k <= 0:
                return 0

            ans = 0
            left1 = 0 # Window for at most k distinct elements
            left2 = 0 # Window for at most k-1 distinct elements
            freq1 = collections.defaultdict(int)
            freq2 = collections.defaultdict(int)
            distinct1 = 0
            distinct2 = 0

            for right in range(len(nums)):
                # Process for 'at most k' window
                if freq1[nums[right]] == 0:
                    distinct1 += 1
                freq1[nums[right]] += 1
                while distinct1 > k:
                    freq1[nums[left1]] -= 1
                    if freq1[nums[left1]] == 0:
                        distinct1 -= 1
                    left1 += 1

                # Process for 'at most k-1' window
                if freq2[nums[right]] == 0:
                    distinct2 += 1
                freq2[nums[right]] += 1
                while distinct2 > k - 1: # Key difference: k-1 here
                    freq2[nums[left2]] -= 1
                    if freq2[nums[left2]] == 0:
                        distinct2 -= 1
                    left2 += 1
                
                # The number of subarrays ending at 'right' with exactly k distinct elements
                # is the difference in starting points between the two windows.
                ans += (left2 - left1)
            
            return ans
    ```
    This alternative has the same O(N) time and O(N) space complexity but performs a single pass over the array, potentially offering a minor constant factor performance improvement. However, the original "subtraction" approach is often favored for its conceptual clarity and the reusability of the `count_at_most_k` helper.

---

### 7. Security/Performance Notes

*   **Security**: The code operates purely on in-memory integer lists and does not interact with external systems, user input in a vulnerable way, or sensitive data. There are no apparent security implications.
*   **Performance**: The solution is optimal in terms of time complexity (O(N)) as it requires at least one pass through the input array. The space complexity (O(N) in the worst case for `freq_map`) is also generally optimal for problems involving arbitrary integer values where frequency counting is necessary. For typical competitive programming constraints, this performance is excellent.

### Code:
```python
import collections

class Solution:
    def subarraysWithKDistinct(self, nums: List[int], k: int) -> int:
        
        def count_at_most_k(arr, k_val):
            if k_val <= 0:
                return 0

            count = 0
            left = 0
            freq_map = collections.defaultdict(int)
            distinct_count = 0 

            for right in range(len(arr)):
                if freq_map[arr[right]] == 0:
                    distinct_count += 1
                freq_map[arr[right]] += 1

                while distinct_count > k_val:
                    freq_map[arr[left]] -= 1
                    if freq_map[arr[left]] == 0:
                        distinct_count -= 1
                    left += 1
                
                count += (right - left + 1)
            return count

        return count_at_most_k(nums, k) - count_at_most_k(nums, k - 1)
```
