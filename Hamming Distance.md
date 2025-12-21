## Hamming Distance
**Language:** python
**Tags:** python,oop,bit manipulation,array

### Description:
### 1. Overview & Intent

The `totalHammingDistance` function aims to calculate the sum of Hamming distances between all possible pairs of numbers in a given list `nums`. The Hamming distance between two integers is the number of positions at which their corresponding bits are different.

The naive approach would be to iterate through all unique pairs of numbers, calculate the Hamming distance for each pair, and sum them up. This would involve `O(N^2)` pair comparisons, with each comparison taking `O(logM)` time (where `M` is the maximum value in `nums`). The provided code implements a much more efficient bit-wise approach to solve this problem.

The core intent is to optimize the calculation by leveraging the property that the total Hamming distance can be computed by summing the differences at each bit position independently.

### 2. How It Works

The algorithm works by iterating through each bit position (from 0 to 31, assuming 32-bit integers) and, for each position, counting how many numbers have a '0' at that bit and how many have a '1'.

Here's a step-by-step breakdown:

1.  **Initialization**: `total_distance` is initialized to 0. This variable will accumulate the Hamming distances across all bit positions and all pairs.

2.  **Iterate through Bit Positions**: The outer loop `for i in range(32)` iterates 32 times, representing bit positions from 0 (least significant bit) to 31 (most significant bit for a 32-bit integer).

3.  **Count 0s and 1s for Current Bit**:
    *   Inside the outer loop, `count0` and `count1` are initialized to 0 for each bit position `i`.
    *   The inner loop `for num in nums` iterates through every number in the input list.
    *   For each `num`, it checks the `i`-th bit:
        *   `(num >> i)`: Right-shifts `num` by `i` positions. This moves the `i`-th bit to the 0-th (least significant) position.
        *   `& 1`: Performs a bitwise AND with 1. This isolates the 0-th bit. If the original `i`-th bit was 1, the result is 1; otherwise, it's 0.
    *   Based on this check, either `count1` (if the bit is 1) or `count0` (if the bit is 0) is incremented.

4.  **Calculate Contribution to Total Distance**:
    *   After iterating through all numbers for a specific bit position `i`, `count0` holds the number of elements with a '0' at bit `i`, and `count1` holds the number of elements with a '1' at bit `i`.
    *   The crucial insight is that for this specific bit position `i`, any pair formed by one number with a '0' at bit `i` and another number with a '1' at bit `i` will contribute 1 to the total Hamming distance.
    *   The number of such pairs is simply `count0 * count1`.
    *   This product is added to `total_distance`.

5.  **Return Result**: After processing all 32 bit positions, `total_distance` will hold the sum of Hamming distances for all pairs, and this value is returned.

### 3. Key Design Decisions

*   **Bit-wise Processing**: The most significant design decision is to process the numbers bit by bit rather than comparing pairs directly. This transforms an `O(N^2)` problem into an `O(N * B)` problem, where `B` is the number of bits.
*   **Counting 0s and 1s**: The algorithm cleverly avoids explicit pair comparisons by counting the occurrences of 0s and 1s at each bit position. This allows for a direct calculation of the contribution of each bit position to the total Hamming distance.
*   **Linearity of Hamming Distance**: The algorithm implicitly relies on the linearity of the Hamming distance. The total Hamming distance is the sum of Hamming distances for each bit position. For a given bit position `i`, the sum of `(x_i != y_i)` over all pairs `(x, y)` is exactly `count0 * count1`.
*   **Fixed Bit Range (32)**: The choice of `range(32)` assumes that the input integers are 32-bit. This is a common assumption in competitive programming contexts for standard integer types.

### 4. Complexity Analysis

*   **Time Complexity**:
    *   The outer loop runs `B` times (where `B` is 32 in this case, a constant).
    *   The inner loop runs `N` times (where `N` is the length of the `nums` list).
    *   Inside the inner loop, bitwise operations (`>>`, `&`) and arithmetic operations (`+`) are constant time operations.
    *   Therefore, the total time complexity is **O(B * N)**, which simplifies to **O(N)** since `B` is a constant.

*   **Space Complexity**:
    *   The variables `total_distance`, `i`, `count0`, `count1`, and `num` each use a constant amount of memory.
    *   The input list `nums` is not modified or copied.
    *   Therefore, the auxiliary space complexity is **O(1)**.

### 5. Edge Cases & Correctness

*   **Empty list (`nums = []`)**:
    *   The outer loop will run 32 times.
    *   The inner loop `for num in nums` will not execute.
    *   `count0` and `count1` will remain 0.
    *   `total_distance += 0 * 0` will add 0.
    *   The function will return 0, which is correct as there are no pairs to compare.

*   **List with one number (`nums = [x]`)**:
    *   The outer loop runs 32 times.
    *   For each bit position, the inner loop will run once. Either `count0` will be 1 and `count1` will be 0, or vice-versa.
    *   `count0 * count1` will always be `1 * 0 = 0`.
    *   `total_distance` will remain 0, which is correct as there are no pairs.

*   **All numbers are identical (`nums = [5, 5, 5]`)**:
    *   For any bit position, all numbers will have the same bit (either all 0s or all 1s).
    *   So, either `count0 = N` and `count1 = 0`, or `count0 = 0` and `count1 = N`.
    *   In both cases, `count0 * count1` will be 0.
    *   `total_distance` will remain 0, which is correct as the Hamming distance between identical numbers is 0.

*   **Numbers exceeding 32-bit range**:
    *   Python integers have arbitrary precision. If `nums` contains numbers larger than `2^32 - 1`, the current `range(32)` will *incorrectly ignore* the higher-order bits. For example, if `nums = [2**32, 0]`, the code would return 0, but the actual Hamming distance is 1 (at bit 32).
    *   **Correctness Proof Sketch**: The algorithm's correctness stems from the fact that the total Hamming distance can be decomposed. For any two numbers `x` and `y`, their Hamming distance `H(x, y)` is the sum of `1` for each bit position `i` where `x_i != y_i`. The total Hamming distance `sum(H(x, y) for all pairs (x, y))` can be rewritten as `sum(sum(1 for pairs (x, y) where x_i != y_i) for all bit positions i)`. For a fixed bit position `i`, the number of pairs `(x, y)` where `x_i != y_i` is exactly `count0 * count1`. Summing `count0 * count1` over all bit positions correctly yields the total Hamming distance.

### 6. Improvements & Alternatives

*   **Dynamic Bit Length**:
    *   The most significant improvement would be to make the bit range dynamic. Instead of `range(32)`, it should be `range(max(num).bit_length() if nums else 0)`. This handles numbers of arbitrary size correctly and can be more efficient if all numbers are small (e.g., single-digit numbers would only iterate a few times).
    *   Example: `max_bits = 0`
        `if nums: max_bits = max(num).bit_length()`
        `for i in range(max_bits): ...`
*   **Readability**: The code is already quite readable and follows standard Python conventions. No major readability improvements are needed.
*   **Minor Optimization (Python specific)**:
    *   Since `count0 + count1 = len(nums)`, `count0` can be expressed as `len(nums) - count1`.
    *   The line `total_distance += count0 * count1` could be `total_distance += count1 * (len(nums) - count1)`. This saves one counter variable but doesn't change complexity.
*   **Alternative (Naive `O(N^2 * logM)`)**:
    ```python
    def totalHammingDistance_naive(self, nums: List[int]) -> int:
        total_distance = 0
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n): # Iterate through unique pairs
                diff = nums[i] ^ nums[j] # XOR gives bits that are different
                distance = 0
                while diff > 0:
                    distance += diff & 1 # Check LSB
                    diff >>= 1 # Shift right
                total_distance += distance
        return total_distance
    ```
    This alternative is much less efficient than the provided solution, demonstrating the power of the bit-wise approach.

### 7. Security/Performance Notes

*   **Performance**: The provided solution is highly optimized for the problem. Its `O(N)` time complexity (considering `B` as a constant) and `O(1)` space complexity are generally the best achievable for this problem, as every number must be examined at least once.
*   **Integer Overflow**: In languages with fixed-size integers (like C++ or Java), `total_distance` could potentially overflow if `N` is very large. For example, if `N = 10^5`, `count0 * count1` could be up to `(N/2)*(N/2) = (5*10^4)^2 = 2.5 * 10^9`. Summing this over 32 bits could exceed the capacity of a 32-bit integer. However, Python integers handle arbitrary precision, so overflow is not a concern for `total_distance` in this specific Python implementation.
*   **Negative Numbers**: The problem statement typically implies non-negative integers for Hamming distance. If negative numbers were allowed, their two's complement representation would need to be considered, and the definition of Hamming distance for them might need clarification. The current bitwise operations would work on the two's complement representation.

### Code:
```python
class Solution:
    def totalHammingDistance(self, nums: List[int]) -> int:
        total_distance = 0
        for i in range(32):
            count0 = 0
            count1 = 0
            for num in nums:
                if (num >> i) & 1:
                    count1 += 1
                else:
                    count0 += 1
            total_distance += count0 * count1
        return total_distance
```
