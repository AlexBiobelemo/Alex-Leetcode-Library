## Minimum Adjacent Swaps for K Consecutive Ones
**Language:** python
**Tags:** python,oop,prefix sum,median,sliding window

### Description:
The provided Python code implements an efficient solution to find the minimum number of moves required to make `k` ones consecutive in a binary array `nums`.

---

### 1. Overview & Intent

The problem asks to find the minimum total distance to move `k` existing `1`s in a binary array `nums` so that they form a contiguous block. Each move costs 1 unit of distance. For example, moving a `1` from index 5 to index 2 costs `abs(5-2) = 3` moves.

The core idea behind this solution is a clever transformation that simplifies the problem. Instead of directly minimizing `sum(abs(original_index - target_index))` for `k` ones, it transforms the indices such that the problem becomes minimizing `sum(abs(transformed_value - median_of_transformed_values))`. This is a classic problem where the sum of absolute differences is minimized when the target value is the median of the set.

### 2. How It Works

The algorithm proceeds in the following steps:

1.  **Extract Indices of Ones**:
    *   It first iterates through the input `nums` array to find all indices where `nums[i] == 1`. These indices are stored in `ones_indices`. This effectively filters out the `0`s, as only the `1`s are relevant for movement.
    *   Example: `nums = [0,1,0,1,1,0,1]`, `ones_indices = [1, 3, 4, 6]`

2.  **Handle Base Case**:
    *   If `k` is 1, it means we only need one `1` to be "consecutive". Since a single `1` is already consecutive, no moves are needed. The function returns `0`.

3.  **Transform Indices (`Q_arr`)**:
    *   A new list `Q_arr` is created. For each `j`-th `1` in `ones_indices` (at `ones_indices[j]`), its transformed value `Q_arr[j]` is calculated as `ones_indices[j] - j`.
    *   **Mathematical Rationale**: Suppose we select `k` ones from `ones_indices` starting at `ones_indices[i]` and ending at `ones_indices[i+k-1]`. We want to move them to form a consecutive block, say at `x, x+1, ..., x+k-1`.
    *   The `j`-th `1` in this chosen window (which is `ones_indices[i + (j-i)]`) would ideally move to `x + (j-i)`.
    *   The number of moves for this specific `1` would be `abs(ones_indices[i+(j-i)] - (x+(j-i)))`.
    *   This expression can be rewritten as `abs((ones_indices[i+(j-i)] - (i+(j-i))) - (x-i))`.
    *   Let `q_idx = i+(j-i)`. Then `Q_arr[q_idx] = ones_indices[q_idx] - q_idx`.
    *   The problem then becomes minimizing `sum(abs(Q_arr[q_idx] - C))` for a constant `C = x-i` over the chosen window of `k` elements. This sum is minimized when `C` is the median of the `Q_arr` values within that window.

4.  **Compute Prefix Sums (`P_Q_padded`)**:
    *   To efficiently calculate the sum of elements within any sub-array of `Q_arr`, a prefix sum array `P_Q_padded` is computed. `P_Q_padded[x]` stores the sum of `Q_arr[0]` through `Q_arr[x-1]`. This allows for O(1) sum calculations for any range.

5.  **Iterate Through Sliding Windows**:
    *   The code then uses a sliding window approach. It iterates through all possible contiguous windows of `k` elements within `Q_arr`. Each window `Q_arr[i...i+k-1]` represents a potential group of `k` ones to be made consecutive.
    *   For each window:
        *   **Find Median**: It determines the index of the median element `mid_idx = i + k // 2` within the current window. The value `median_val = Q_arr[mid_idx]` is the target `C` that minimizes the sum of absolute differences for this window.
        *   **Calculate Moves for Current Window**: The total moves for this window are calculated using the property that `sum(abs(x_j - M))` is minimized when `M` is the median. The formula is derived from `sum(M - x_j)` for `x_j < M` and `sum(x_j - M)` for `x_j > M`.
            *   `sum_left_of_median = P_Q_padded[mid_idx] - P_Q_padded[i]` (sum of elements `Q_arr[i]` to `Q_arr[mid_idx-1]`)
            *   `count_left_of_median = mid_idx - i`
            *   `sum_right_of_median = P_Q_padded[i+k] - P_Q_padded[mid_idx+1]` (sum of elements `Q_arr[mid_idx+1]` to `Q_arr[i+k-1]`)
            *   `count_right_of_median = (i + k - 1) - mid_idx`
            *   `current_total_moves = (count_left_of_median * median_val - sum_left_of_median) + (sum_right_of_median - count_right_of_median * median_val)`
        *   **Update Minimum**: `min_total_moves` is updated if `current_total_moves` is smaller.

6.  **Return Result**:
    *   After checking all possible windows, the `min_total_moves` found is returned.

### 3. Key Design Decisions

*   **Index Transformation (`Q_arr`)**: This is the most crucial design choice. It converts a complex 2D optimization problem (choosing `k` ones and their `k` target positions) into a simpler 1D problem (choosing `k` transformed values and a single target median value). The `p_j - j` transformation effectively "normalizes" the indices, making the relative positions the key factor.
*   **Prefix Sums**: The use of prefix sums (`P_Q_padded`) is essential for achieving optimal time complexity. It allows the sum of any sub-array of `Q_arr` to be computed in `O(1)` time, which is critical for the sliding window approach.
*   **Median Property**: The fundamental mathematical property that `sum(abs(x_i - M))` is minimized when `M` is the median of `x_i` values is the cornerstone of the solution. This allows for a direct calculation of moves once the median is identified for a given window.
*   **Sliding Window**: Iterating through all `k`-sized windows in `Q_arr` ensures that every possible combination of `k` ones is considered for forming a consecutive block.

### 4. Complexity Analysis

Let `N` be the length of the input `nums` array and `m` be the number of `1`s in `nums`.

*   **Time Complexity**:
    *   **Extract One-Indices**: `O(N)` to iterate through `nums`.
    *   **Transform Indices (`Q_arr`)**: `O(m)` to iterate through `ones_indices`.
    *   **Prefix Sums (`P_Q_padded`)**: `O(m)` to compute prefix sums.
    *   **Iterate Through Windows**: The loop runs `m - k + 1` times (approximately `O(m)` iterations). Inside the loop, all calculations (median index, value, sums, counts) are `O(1)` due to prefix sums.
    *   **Overall Time Complexity**: `O(N) + O(m) + O(m) + O(m) = O(N + m)`. Since `m <= N`, this simplifies to **`O(N)`**.

*   **Space Complexity**:
    *   `ones_indices`: Stores up to `m` indices. `O(m)` space.
    *   `Q_arr`: Stores `m` transformed values. `O(m)` space.
    *   `P_Q_padded`: Stores `m+1` prefix sums. `O(m)` space.
    *   **Overall Space Complexity**: **`O(m)`**. In the worst case (all `1`s), this is `O(N)`.

### 5. Edge Cases & Correctness

*   **`k = 1`**: Correctly handled by returning `0`. A single `1` is always "consecutive".
*   **`k = m`**: If `k` equals the total number of `1`s, the loop for windows runs once (`i=0`), considering all `m` ones. This is correct.
*   **`k > m`**: The problem constraints typically guarantee `k <= m`. If `k > m` were possible, the `range(m - k + 1)` would be empty or negative, causing the main loop to not run. `min_total_moves` would remain `float('inf')`, which correctly indicates impossibility.
*   **`nums` contains no `1`s (`m = 0`)**: If `k=1`, it returns 0. If `k > 1`, `m` is 0, the loop `range(m - k + 1)` is empty, and `min_total_moves` remains `float('inf')`, correctly indicating impossibility.
*   **All `1`s are already consecutive**: If `ones_indices` is `[x, x+1, ..., x+m-1]`, then `Q_arr` will contain all identical values (e.g., `[x, x, ..., x]`). The `median_val` will be `x`, and the `current_total_moves` calculation will correctly yield `0`.
*   **Parity of `k` (even/odd)**: The `k // 2` calculation for `mid_idx` correctly identifies the median. For odd `k`, it's the unique middle element. For even `k`, it's the left of the two middle elements. Both choices are valid for minimizing the sum of absolute differences.

The mathematical derivation for `current_total_moves` using prefix sums is robust and correctly calculates the sum of absolute differences from the median.

### 6. Improvements & Alternatives

*   **Readability**:
    *   The `current_total_moves` calculation is mathematically concise but can be hard to parse. Breaking it down into two separate terms (for elements left and right of the median) with comments would enhance readability.
    *   Adding comments to explain the `Q_arr` transformation and the median property would greatly help understanding.
    *   More descriptive variable names, e.g., `prefix_sums_of_Q_arr` instead of `P_Q_padded`.

*   **Robustness**:
    *   If `k > m` is a possible input (and not guaranteed by problem constraints), an explicit check and an appropriate error (e.g., `ValueError`) or a specific return value (e.g., `-1`) might be more informative than `float('inf')`.

*   **Alternative Approaches (Less Optimal)**:
    *   **Brute Force with Sorting**: For each `k`-sized window of `1`s, extract their `Q_arr` values, sort them to find the median, and then sum absolute differences. This would be `O(m * k log k)` or `O(m * k)` if using a linear-time median selection algorithm, which is slower than the current `O(m)` approach.
    *   **Sliding Window with Heaps**: Maintain two heaps (min-heap and max-heap) to find the median in `O(log k)` time per window shift. This would result in `O(m log k)` time complexity. The current prefix sum approach is superior because it directly calculates the sum of differences from the median *value* without needing to re-sort or re-balance heaps for each window, leveraging the fact that `Q_arr` values are derived from sorted indices.

### 7. Security/Performance Notes

*   **Security**: This code is purely algorithmic and operates on numerical inputs. It does not interact with external systems, files, or user inputs in a way that would introduce security vulnerabilities. Thus, security notes are not applicable.
*   **Performance**: The solution is highly optimized, achieving linear time complexity `O(N)` and linear space complexity `O(m)`. This is generally considered optimal for this type of problem, as it requires at least `O(N)` to read the input and `O(m)` to store the relevant `1` indices. The use of prefix sums is key to this efficiency.

### Code:
```python
class Solution:
    def minMoves(self, nums: List[int], k: int) -> int:
        ones_indices = []
        for i, num in enumerate(nums):
            if num == 1:
                ones_indices.append(i)

        m = len(ones_indices)

        if k == 1:
            return 0

        Q_arr = [ones_indices[j] - j for j in range(m)]

        P_Q_padded = [0] * (m + 1)
        for j in range(m):
            P_Q_padded[j+1] = P_Q_padded[j] + Q_arr[j]

        min_total_moves = float('inf')

        for i in range(m - k + 1):
            mid_idx = i + k // 2
            median_val = Q_arr[mid_idx]

            sum_left_of_median = P_Q_padded[mid_idx] - P_Q_padded[i]
            count_left_of_median = mid_idx - i

            sum_right_of_median = P_Q_padded[i+k] - P_Q_padded[mid_idx+1]
            count_right_of_median = (i + k - 1) - mid_idx
            
            current_total_moves = (count_left_of_median * median_val - sum_left_of_median) + \
                                  (sum_right_of_median - count_right_of_median * median_val)
            
            min_total_moves = min(min_total_moves, current_total_moves)

        return int(min_total_moves)
```
