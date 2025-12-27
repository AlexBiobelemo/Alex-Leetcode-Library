## Best Time to Buy and Sell Stock
**Language:** python
**Tags:** python,oop,greedy,array

### Description:
---

### 1. Overview & Intent

This code implements a solution to the "Best Time to Buy and Sell Stock" problem (often seen as LeetCode problem 121). The goal is to find the maximum profit that can be achieved by completing at most one transaction – meaning, you can buy a stock on one day and sell it on a later day. If no profit can be made, the result should be 0.

The approach taken is a single-pass, greedy algorithm. It iterates through the stock prices once, keeping track of the lowest price encountered so far and the maximum profit that could have been made up to the current point.

### 2. How It Works

The algorithm proceeds as follows:

1.  **Initialization:**
    *   `min_price` is initialized to `float('inf')`. This ensures that the very first stock price encountered will always be lower than `min_price`, effectively setting the initial potential buying point.
    *   `max_profit` is initialized to `0`. This serves as the baseline; if no profitable transaction is possible, the function will correctly return 0.

2.  **Iteration:**
    *   The code iterates through each `price` in the input `prices` list.

3.  **Logic within the loop:**
    *   **Finding a new minimum:** `if price < min_price:`
        *   If the current `price` is lower than the `min_price` recorded so far, it means we've found a new potential "best day to buy." We update `min_price` to this new lower value. This is a greedy choice: always aim to buy at the lowest possible price seen up to the current day.
    *   **Calculating potential profit:** `elif price - min_price > max_profit:`
        *   If the current `price` is *not* lower than `min_price` (meaning `price >= min_price`), it means we are considering selling the stock on the current day. The profit from selling today would be `price - min_price` (assuming we bought at the lowest price seen *before or on* the current day).
        *   If this calculated profit is greater than the `max_profit` found so far, we update `max_profit` to this new higher value. This is another greedy choice: always keep track of the highest profit achievable.

4.  **Return Value:**
    *   After iterating through all prices, `max_profit` will hold the maximum profit that could have been achieved from a single transaction. This value is then returned.

### 3. Key Design Decisions

*   **Greedy Approach:** The core design decision is the greedy strategy. At each step, the algorithm makes the locally optimal choice: either update the `min_price` if a lower buying point is found, or update `max_profit` if a better selling opportunity presents itself relative to the current `min_price`. This works because a selling decision on day `X` can only be based on a buying decision made on day `Y` where `Y <= X`. By always tracking the global minimum buying price up to the current day, we ensure we're always calculating the maximum possible profit for any given selling day.
*   **Single Pass:** The algorithm processes the input array only once. This is crucial for achieving optimal time complexity.
*   **Minimal State:** Only two variables (`min_price` and `max_profit`) are needed to maintain the necessary state throughout the iteration. This contributes to optimal space complexity.

### 4. Complexity Analysis

*   **Time Complexity: O(N)**
    *   The code iterates through the `prices` list exactly once, where `N` is the number of prices.
    *   Inside the loop, all operations (comparisons, assignments, subtractions) are constant time, O(1).
    *   Therefore, the total time complexity is directly proportional to the number of prices, making it O(N). This is the most efficient possible time complexity, as every price must be examined at least once.

*   **Space Complexity: O(1)**
    *   The algorithm uses a fixed number of variables (`min_price`, `max_profit`, and the loop variable `price`).
    *   The amount of memory used does not grow with the size of the input `prices` list.
    *   Thus, the space complexity is constant, O(1).

### 5. Edge Cases & Correctness

The algorithm handles various edge cases correctly:

*   **Empty `prices` list:** If `prices` is empty, the `for` loop will not execute. `max_profit` will remain `0`, which is correct as no transaction can occur. (Note: LeetCode problems often specify non-empty inputs, but it's good practice to consider).
*   **Single `price`:** The loop runs once. `min_price` becomes the single price. `max_profit` remains `0`. Correct, as you cannot buy and sell on the same day or without a future day.
*   **Prices in decreasing order (e.g., `[7, 6, 4, 3, 1]`):** `min_price` will continuously update to the current (lower) price. The condition `price - min_price > max_profit` will never be true for a positive profit (it will always be 0 or negative). `max_profit` will correctly remain `0`.
*   **Prices in increasing order (e.g., `[1, 2, 3, 4, 5]`):** `min_price` will be set to `1` on the first iteration. Subsequent iterations will calculate `price - 1`, and `max_profit` will be updated progressively (e.g., `2-1=1`, `3-1=2`, `4-1=3`, `5-1=4`). The final `max_profit` will be `4`, which is correct (`5-1`).
*   **Mixed prices (e.g., `[7, 1, 5, 3, 6, 4]`):**
    *   `min_price = inf`, `max_profit = 0`
    *   `price = 7`: `min_price = 7`
    *   `price = 1`: `min_price = 1`
    *   `price = 5`: `5 - 1 = 4`. `max_profit = 4`
    *   `price = 3`: `3 - 1 = 2`. `2 < 4`. `max_profit` remains `4`.
    *   `price = 6`: `6 - 1 = 5`. `5 > 4`. `max_profit = 5`
    *   `price = 4`: `4 - 1 = 3`. `3 < 5`. `max_profit` remains `5`.
    *   Returns `5`. This is correct, as buying at 1 and selling at 6 yields the maximum profit.

### 6. Improvements & Alternatives

*   **Readability:** The code is already very clear, concise, and follows Pythonic conventions. No significant readability improvements are needed.
*   **Alternative (Brute Force - Less Efficient):**
    *   A naive approach would involve nested loops: an outer loop for every possible buying day `i`, and an inner loop for every possible selling day `j` (where `j > i`).
    *   Calculate `prices[j] - prices[i]` and keep track of the maximum.
    *   Time Complexity: O(N^2). Space Complexity: O(1). This is significantly less efficient than the provided solution.
*   **Alternative (Kadane's Algorithm Variation - Similar Efficiency):**
    *   This problem can be rephrased as finding the maximum subarray sum of price *differences*.
    *   First, transform the `prices` array into an array of daily gains/losses: `diffs[i] = prices[i+1] - prices[i]`.
    *   Then, apply Kadane's algorithm to find the maximum sum of a contiguous subarray in `diffs`. This sum represents the maximum profit.
    *   Time Complexity: O(N) (O(N) to create `diffs` + O(N) for Kadane's).
    *   Space Complexity: O(N) if `diffs` array is explicitly created, or O(1) if `diffs` are calculated on the fly within a modified Kadane's.
    *   The provided solution is essentially a more direct and slightly more space-efficient (O(1) vs O(N) for the `diffs` array) way of achieving the same O(N) time complexity, without explicitly creating the `diffs` array. It's often considered the most intuitive and direct O(N) O(1) solution.

### 7. Security/Performance Notes

*   **Security:** There are no inherent security vulnerabilities in this code. It performs purely numerical computations on an input list and does not interact with external systems, files, or user inputs in a way that could be exploited.
*   **Performance:** The code is optimally performant for the given problem constraints. With O(N) time complexity and O(1) space complexity, it cannot be significantly improved upon in terms of asymptotic performance. Any further "performance" gains would be micro-optimizations (e.g., using `min()` and `max()` built-ins instead of `if` statements, which often have negligible or even negative impact on real-world speed due to function call overheads). The current implementation is already highly efficient.

### Code:
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        min_price = float('inf')
        max_profit = 0
        for price in prices:
            if price < min_price:
                min_price = price
            elif price - min_price > max_profit:
                max_profit = price - min_price
        return max_profit
```
