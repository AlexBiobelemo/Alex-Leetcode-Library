## Find Maximum in Rotated Sorted Array
**Language:** python
**Tags:** python,binary search,oop,array

### Description:
This code implements a classic binary search algorithm to find the minimum element in a rotated sorted array. It's a well-known problem often encountered in technical interviews.

---

### 1. Overview & Intent

The primary goal of this code is to efficiently locate the minimum element within an array that was originally sorted in ascending order but has been rotated at some unknown pivot point. For example, `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`. The key constraint for this specific implementation (and the typical problem statement for LeetCode 153) is that **all elements in the array are unique**.

The chosen approach leverages the partially sorted nature of the rotated array to employ a modified binary search, achieving a significantly faster solution than a linear scan.

### 2. How It Works

The algorithm uses a binary search strategy to narrow down the search space until the minimum element is found.

1.  **Initialization**: Two pointers, `left` and `right`, are initialized to the beginning (index 0) and end (last index) of the array, respectively.
2.  **Search Loop**: A `while` loop continues as long as `left < right`. This condition ensures that there are at least two elements in the current search space, allowing for meaningful comparisons and reduction.
3.  **Midpoint Calculation**: Inside the loop, `mid` is calculated as `left + (right - left) // 2`. This method of calculating the midpoint is preferred over `(left + right) // 2` to prevent potential integer overflow in languages with fixed-size integers (though less critical in Python).
4.  **Comparison and Space Reduction**: The core logic lies in comparing `nums[mid]` with `nums[right]`:
    *   **`if nums[mid] > nums[right]`**: This condition indicates that the element at `mid` is greater than the element at the `right` end of the current search space. This can only happen if the rotation point (and thus the minimum element) lies somewhere *after* `mid`. For example, in `[4,5,6,7,0,1,2]`, if `mid` points to `7` and `right` points to `2`, then `7 > 2`. This means the minimum must be in the range `mid + 1` to `right`. Therefore, we discard `mid` and everything to its left by setting `left = mid + 1`.
    *   **`else (nums[mid] < nums[right])`**: This condition implies that the segment of the array from `mid` to `right` is sorted in ascending order. Since `nums[mid]` is less than `nums[right]`, `nums[mid]` is either the minimum element itself or the minimum element lies to its left (in the range `left` to `mid-1`). The minimum cannot be in `mid+1` to `right` because `nums[mid]` is already smaller than or equal to all elements in that range. Thus, we narrow the search space by setting `right = mid`, keeping `nums[mid]` as a potential candidate.
5.  **Termination**: The loop terminates when `left` becomes equal to `right`. At this point, both pointers converge on a single index, which is the index of the minimum element in the array.
6.  **Return Value**: The function returns `nums[left]` (or `nums[right]`, as they are the same).

### 3. Key Design Decisions

*   **Binary Search**: The most critical design decision is the use of binary search. This is appropriate because even though the array is rotated, it retains a "partially sorted" property that allows for efficient halving of the search space.
*   **Comparison `nums[mid]` vs `nums[right]`**: This specific comparison is crucial.
    *   It helps determine which side of `mid` the "disruption" (the rotation point where the minimum lies) occurs.
    *   By comparing with `nums[right]`, we can reliably tell if the segment `mid...right` is sorted or if the minimum must be within it.
*   **`right = mid` vs. `right = mid - 1`**: When `nums[mid] < nums[right]`, we set `right = mid`. This is because `nums[mid]` itself could be the minimum element (e.g., if `mid` points to the actual minimum). We cannot discard `mid` by setting `right = mid - 1`.
*   **`left = mid + 1` vs. `left = mid`**: When `nums[mid] > nums[right]`, we set `left = mid + 1`. In this case, `nums[mid]` cannot be the minimum because we know `nums[mid]` is greater than `nums[right]`, and `nums[right]` is still part of the search space. Therefore, `mid` can be safely excluded.
*   **Loop Condition `left < right`**: This condition ensures that the loop continues as long as there's a range to search. When `left == right`, the search space has converged to a single element, which must be the minimum.

### 4. Complexity Analysis

*   **Time Complexity: O(log N)**
    *   In each iteration of the `while` loop, the search space (the range between `left` and `right`) is effectively halved.
    *   This logarithmic reduction in search space is the hallmark of binary search algorithms.
    *   `N` represents the number of elements in the input array `nums`.
*   **Space Complexity: O(1)**
    *   The algorithm uses a constant amount of extra space for variables like `left`, `right`, and `mid`, regardless of the input array's size. It does not allocate any data structures proportional to `N`.

### 5. Edge Cases & Correctness

The algorithm correctly handles several edge cases:

*   **Array with one element**:
    *   `nums = [1]`
    *   `left = 0`, `right = 0`.
    *   The `while left < right` condition (`0 < 0`) is immediately false.
    *   The loop doesn't execute, and `nums[left]` (which is `nums[0] = 1`) is returned. Correct.
*   **Array that is not rotated (fully sorted)**:
    *   `nums = [1,2,3,4,5]`
    *   `left = 0`, `right = 4`. `mid = 2` (`nums[mid] = 3`).
    *   `nums[mid] (3) < nums[right] (5)` is true. `right` becomes `mid` (2).
    *   `left = 0`, `right = 2`. `mid = 1` (`nums[mid] = 2`).
    *   `nums[mid] (2) < nums[right] (3)` is true. `right` becomes `mid` (1).
    *   `left = 0`, `right = 1`. `mid = 0` (`nums[mid] = 1`).
    *   `nums[mid] (1) < nums[right] (2)` is true. `right` becomes `mid` (0).
    *   `left = 0`, `right = 0`. Loop terminates.
    *   Returns `nums[0] = 1`. Correct.
*   **Array rotated such that the minimum is at the beginning or end**:
    *   `nums = [7,0,1,2,4,5,6]` (min at index 1)
    *   `nums = [1,2,4,5,6,7,0]` (min at index 6)
    *   The logic correctly guides `left` and `right` to converge on the minimum in these scenarios due to the `nums[mid] > nums[right]` and `nums[mid] < nums[right]` comparisons.
*   **Uniqueness Constraint**: This implementation relies on the problem's typical constraint that all elements are unique. If duplicates were allowed (e.g., `[3,1,3]`), the `nums[mid] == nums[right]` case would need special handling (often by simply decrementing `right` to shrink the search space, as `nums[right]` provides no new information). However, for unique elements, this code is robust.
*   **Empty Array**: The code assumes `nums` is non-empty due to `len(nums) - 1`. If an empty array were possible, an explicit check `if not nums: return None` (or raise an error) would be necessary.

### 6. Improvements & Alternatives

*   **Readability**: The comments within the code are exceptionally clear and helpful, explaining the rationale behind each decision to narrow the search space. No significant readability improvements are needed.
*   **Alternative Approaches**:
    *   **Linear Scan**: A simple loop iterating from the first element to the last, keeping track of the minimum value encountered. This is `O(N)` time complexity, which is less efficient than binary search for large arrays.
    *   **Built-in `min()` function**: `return min(nums)`. This is also `O(N)` time complexity as it performs a linear scan internally. While concise, it doesn't leverage the partially sorted nature of the array.
*   **Minor Optimization (Python specific)**: For very small arrays (e.g., N < 10-20), the overhead of binary search (pointer arithmetic, loop conditions) might slightly outweigh a simple linear scan. However, for any practical input size where efficiency matters, binary search is superior.

### 7. Security/Performance Notes

*   **Security**: This algorithm operates purely on an in-memory array of integers and does not involve any external input, network communication, file I/O, or sensitive data. Therefore, there are no direct security implications.
*   **Performance**: The `O(log N)` time complexity is optimal for this problem, as it's impossible to do better than logarithmic time without knowing the minimum's position beforehand. The constant factors involved are small, making it very fast in practice.

### Code:
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left = 0
        right = len(nums) - 1

        while left < right:
            mid = left + (right - left) // 2
            
            if nums[mid] > nums[right]:
                left = mid + 1
            else:
                right = mid
        
        return nums[left]
```
