## Best Time to Buy and Sell Stock III
**Language:** python
**Tags:** python,dynamic programming,array,oop

### Description:
This problem asks to find the maximum profit achievable by completing at most two transactions.

---

### 1. Overview & Intent

This code implements a dynamic programming approach to solve the "Best Time to Buy and Sell Stock III" problem. The core idea is to track the maximum profit at different stages of up to two transactions as we iterate through the stock prices day by day. Instead of building a full 2D DP table, it uses a space-optimized approach with a constant number of variables.

The intent is to efficiently calculate the maximum profit by considering four key states for each day:
*   Having bought the first stock (`buy1`).
*   Having sold the first stock (`sell1`).
*   Having bought the second stock (`buy2`).
*   Having sold the second stock (`sell2`).

By iteratively updating these states based on the current day's price and the previous day's best profits, the algorithm determines the overall maximum profit.

### 2. How It Works

The algorithm processes the `prices` list sequentially, representing the stock prices on consecutive days. It maintains four variables, each representing the maximum profit achievable at a specific stage of transactions:

*   **`buy1`**: Represents the maximum profit (or minimum cost, hence negative profit) after *buying the first stock*. Initially `float('-inf')` because no stock has been bought yet.
*   **`sell1`**: Represents the maximum profit after *selling the first stock*. Initially `0` because no profit has been made yet.
*   **`buy2`**: Represents the maximum profit after *completing the first transaction (selling the first stock) and then buying the second stock*. Initially `float('-inf')`.
*   **`sell2`**: Represents the maximum profit after *completing the second transaction (selling the second stock)*. This is the ultimate goal. Initially `0`.

For each `price` in the `prices` list (representing the current day's stock price), the variables are updated in a specific order:

1.  **`sell2 = max(sell2, buy2 + price)`**:
    *   `sell2` is the maximum profit after selling the second stock.
    *   We either keep the `sell2` value from the previous day (meaning we didn't sell the second stock today), OR
    *   We sell the second stock today: this means we must have bought the second stock previously (represented by `buy2`), and now we add the current `price` to that profit.
    *   Crucially, `buy2` here refers to the maximum profit *after buying the second stock up to the previous day*.

2.  **`buy2 = max(buy2, sell1 - price)`**:
    *   `buy2` is the maximum profit after buying the second stock.
    *   We either keep the `buy2` value from the previous day (meaning we didn't buy the second stock today), OR
    *   We buy the second stock today: this means we must have completed the first transaction (represented by `sell1`), and now we subtract the current `price` (cost of buying) from that profit.
    *   `sell1` here refers to the maximum profit *after selling the first stock up to the previous day*.

3.  **`sell1 = max(sell1, buy1 + price)`**:
    *   `sell1` is the maximum profit after selling the first stock.
    *   We either keep the `sell1` value from the previous day, OR
    *   We sell the first stock today: this means we must have bought the first stock previously (represented by `buy1`), and now we add the current `price`.
    *   `buy1` here refers to the maximum profit *after buying the first stock up to the previous day*.

4.  **`buy1 = max(buy1, -price)`**:
    *   `buy1` is the maximum profit after buying the first stock.
    *   We either keep the `buy1` value from the previous day, OR
    *   We buy the first stock today: this means we incur a cost, so we subtract the current `price`.

After iterating through all prices, `sell2` will hold the maximum profit achievable with at most two transactions. The final `return sell2` correctly provides this result.

### 3. Key Design Decisions

*   **Dynamic Programming (DP)**: The problem is broken down into overlapping subproblems, where the solution for the current day depends on the solutions from previous days.
*   **State Definition**: The choice of four states (`buy1`, `sell1`, `buy2`, `sell2`) is critical. These states effectively capture all necessary information to transition between transaction phases (buy first, sell first, buy second, sell second) and track maximum profit.
*   **Space Optimization**: Instead of using a 2D DP table (e.g., `dp[day][state]`), the solution uses only four variables. This reduces the space complexity from O(N) to O(1), as each day's calculation only requires the previous day's state values.
*   **Initialization**:
    *   `buy1` and `buy2` are initialized to `float('-inf')` because holding a stock initially means incurring a cost, and `float('-inf')` ensures that any actual purchase (even at a high price) will be considered a "better" state than `'-inf'`.
    *   `sell1` and `sell2` are initialized to `0` because initially, no profit has been made.
*   **Order of Updates**: The updates are performed in reverse order of transaction completion (`sell2`, then `buy2`, then `sell1`, then `buy1`). This specific order is crucial for space-optimized DP where `dp[i][state]` depends on `dp[i-1][state']`. It ensures that when calculating a state for the current day (`i`), it uses the values of dependent states from the *previous* day (`i-1`), preventing the use of "current day's" values for a state that logically should precede it. For example, `sell2` uses `buy2` from the previous iteration, `buy2` uses `sell1` from the previous iteration, and so on.

### 4. Complexity Analysis

*   **Time Complexity: O(N)**
    *   The code iterates through the `prices` list exactly once.
    *   Inside the loop, a constant number of operations (comparisons and arithmetic) are performed.
    *   Therefore, the time complexity is directly proportional to the number of prices, N.

*   **Space Complexity: O(1)**
    *   The algorithm uses a fixed number of variables (`buy1`, `sell1`, `buy2`, `sell2`) regardless of the input size.
    *   No auxiliary data structures that grow with N are used.

### 5. Edge Cases & Correctness

The solution handles various edge cases correctly:

*   **Empty `prices` list**: If `prices` is empty, the loop will not execute. `sell2` remains `0`, which is the correct maximum profit.
*   **Single price**: If `prices` contains one element, the loop runs once. `buy1` will become `-price`, while `sell1`, `buy2`, and `sell2` will remain `0`. The final `sell2` will be `0`, which is correct as no transaction can be completed.
*   **Prices in decreasing order (no profit possible)**: E.g., `[5, 4, 3, 2, 1]`. All `buyX` variables will track increasingly negative values, and `sellX` variables will remain `0`. The final `sell2` will be `0`, which is correct.
*   **Prices allowing only one transaction**: E.g., `[1, 5]`.
    *   `price = 1`: `buy1 = -1`, others `0`.
    *   `price = 5`:
        *   `sell2 = max(0, buy2_old + 5) = max(0, -1 + 5) = 4`
        *   `buy2 = max(-1, sell1_old - 5) = max(-1, 0 - 5) = -1`
        *   `sell1 = max(0, buy1_old + 5) = max(0, -1 + 5) = 4`
        *   `buy1 = max(-1, -5) = -1`
    *   The final `sell2` is `4`. This demonstrates how the `buy2` state, when `sell1` is `0`, effectively allows the second transaction to act as the first (and only) transaction, thus correctly capturing profits from one transaction as well. This is crucial for the "at most two transactions" requirement.
*   **Complex fluctuating prices**: The DP correctly identifies optimal buy/sell points across two transactions, even if they are non-contiguous or involve "holding" for some days. The `max` operations ensure that the best profit for each state is always retained.

### 6. Improvements & Alternatives

*   **Readability of Variable Names**: While `buy1`, `sell1`, `buy2`, `sell2` are standard in competitive programming for this problem, for a general codebase, more descriptive names could be used, e.g., `max_profit_after_first_buy`, `max_profit_after_first_sell`, etc. However, given the context of a common algorithmic problem, the current names are acceptable.
*   **Generalization to K Transactions**: This specific solution is optimized for K=2. A more general DP solution for "at most K transactions" would typically involve a 2D array, `dp[k][state]`, where `k` is the transaction count. The space-optimized approach for K=2 is derived from this general solution.
*   **Alternative DP Perspective (State Machine)**: The problem can also be modeled as a state machine where each state represents a combination of `(transaction_count, holding_stock)`. The transitions between states are driven by buying or selling actions. This code is essentially an optimized implementation of such a state machine.

### 7. Security/Performance Notes

*   **Security**: There are no inherent security vulnerabilities in this code. It deals with numerical calculations on input arrays and does not involve external resources, user input that could lead to injection, or sensitive data handling.
*   **Performance**: The performance is optimal for this problem. O(N) time complexity is the best possible since every price must be examined at least once. O(1) space complexity is also optimal as it uses a constant amount of memory. No further performance improvements are possible within the constraints of the problem.

### Code:
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        buy1 = float('-inf')
        sell1 = 0
        buy2 = float('-inf')
        sell2 = 0

        for price in prices:

            sell2 = max(sell2, buy2 + price)

            buy2 = max(buy2, sell1 - price)
            sell1 = max(sell1, buy1 + price)
            buy1 = max(buy1, -price)

        return sell2
```
