## Maximum Profit from Valid Topological Order in DAG
**Language:** python
**Tags:** python,dynamic programming,bitmask dp,graph,oop

### Description:
<p>This Python code solves a dynamic programming problem to find the maximum profit by selecting a sequence of nodes from a graph, respecting prerequisite constraints. Each node <code>i</code> selected at position <code>p</code> contributes <code>score[i] * p</code> to the total profit.</p>
<h2>1. Overview &amp; Intent</h2>
<ul>
<li><strong>Goal:</strong> Calculate the maximum possible profit achievable by selecting <code>n</code> nodes in a specific order.</li>
<li><strong>Profit Rule:</strong> If node <code>i</code> is the <code>p</code>-th node selected in the sequence, it adds <code>score[i] * p</code> to the total profit.</li>
<li><strong>Constraints:</strong> Node <code>u</code> must be selected before node <code>v</code> if there's an edge <code>(u, v)</code>. This defines a prerequisite relationship where <code>u</code> is a prerequisite for <code>v</code>.</li>
<li><strong>Approach:</strong> The problem is tackled using dynamic programming with bitmasking to represent subsets of chosen nodes and memoization to optimize recursive calls.</li>
</ul>
<h2>2. How It Works</h2>
<ol>
<li><p><strong>Prerequisite Precomputation:</strong></p>
<ul>
<li>An array <code>prereq</code> of size <code>n</code> is initialized.</li>
<li>For each edge <code>(u, v)</code> in <code>edges</code>, it sets the <code>u</code>-th bit in <code>prereq[v]</code>. This means <code>prereq[v]</code> becomes a bitmask where a bit <code>(1 &lt;&lt; u)</code> being set signifies that node <code>u</code> is a prerequisite for node <code>v</code>.</li>
</ul>
</li>
<li><p><strong><code>solve(mask)</code> Recursive Function (DP with Memoization):</strong></p>
<ul>
<li>This function uses <code>@functools.lru_cache(None)</code> for memoization, storing results for each unique <code>mask</code>.</li>
<li><strong><code>mask</code> Parameter:</strong> A bitmask representing the set of nodes that have <em>already</em> been selected in the sequence. If the <code>i</code>-th bit is set, node <code>i</code> has been chosen.</li>
<li><strong><code>current_pos</code> Calculation:</strong> Determines the position (1-indexed) for the <em>next</em> node to be chosen. It's calculated as <code>mask.bit_count() + 1</code>. If no nodes are in <code>mask</code> (0 bits), the next node will be at position 1.</li>
<li><strong>Base Case:</strong> If <code>current_pos</code> exceeds <code>n</code> (meaning all <code>n</code> nodes have been selected), the function returns 0, as no more profit can be gained.</li>
<li><strong>Iterating Candidates:</strong> The function iterates through all <code>n</code> nodes (from <code>0</code> to <code>n-1</code>).</li>
<li><strong>Validity Checks for Node <code>i</code>:</strong><ul>
<li><code>if not (mask &amp; (1 &lt;&lt; i))</code>: Checks if node <code>i</code> has <em>not</em> already been selected.</li>
<li><code>if (prereq[i] &amp; mask) == prereq[i]</code>: This is the crucial prerequisite check. It verifies if <em>all</em> prerequisites for node <code>i</code> (bits set in <code>prereq[i]</code>) are also present in the <code>mask</code> (i.e., have already been selected).</li>
</ul>
</li>
<li><strong>Calculate Profit &amp; Recurse:</strong> If node <code>i</code> is a valid candidate:<ul>
<li><code>current_profit</code> for picking <code>i</code> is <code>(score[i] * current_pos)</code>.</li>
<li>Recursively call <code>solve()</code> with an updated mask (<code>mask | (1 &lt;&lt; i)</code>) to find the maximum profit from the remaining sequence.</li>
<li>The total profit for this path is <code>current_profit + solve(mask | (1 &lt;&lt; i))</code>.</li>
<li><code>max_profit</code> is updated to store the highest profit found among all valid choices for the current <code>mask</code>.</li>
</ul>
</li>
<li>Returns <code>max_profit</code> for the given <code>mask</code>.</li>
</ul>
</li>
<li><p><strong>Initial Call:</strong> The process starts by calling <code>solve(0)</code>, representing an empty set of selected nodes.</p>
</li>
</ol>
<h2>3. Key Design Decisions</h2>
<ul>
<li><strong>Dynamic Programming:</strong> The problem exhibits optimal substructure (optimal solution for <code>n</code> nodes depends on optimal solutions for <code>n-1</code> nodes) and overlapping subproblems (the same subproblems, represented by a <code>mask</code>, are encountered multiple times).</li>
<li><strong>Bitmasking:</strong><ul>
<li>Node subsets are efficiently represented as bitmasks, allowing <code>O(1)</code> operations for checking node membership, adding a node, and checking prerequisite satisfaction.</li>
<li>The bitmask serves as the state for the DP, making memoization straightforward.</li>
</ul>
</li>
<li><strong><code>functools.lru_cache</code>:</strong> Simplifies the implementation of memoization for the recursive DP, making the code cleaner and more concise than manual dictionary lookups.</li>
<li><strong>Precomputation of <code>prereq</code>:</strong> Computing all prerequisites upfront avoids redundant calculations within the recursive function, improving efficiency.</li>
</ul>
<h2>4. Complexity</h2>
<ul>
<li><strong>Time Complexity:</strong> <code>O(2^n * n)</code><ul>
<li>There are <code>2^n</code> possible states (masks) for the <code>solve</code> function.</li>
<li>For each state, the function iterates through <code>n</code> possible nodes to pick.</li>
<li>Inside the loop, bitwise operations and <code>mask.bit_count()</code> (which is O(1) in Python 3.10+ due to native <code>popcount</code>) are efficient.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong> <code>O(2^n)</code><ul>
<li>The <code>lru_cache</code> stores results for <code>2^n</code> states.</li>
<li>The <code>prereq</code> array takes <code>O(n)</code> space.</li>
<li>The recursion depth can go up to <code>O(n)</code>.</li>
<li>Dominated by the <code>lru_cache</code> storage.</li>
</ul>
</li>
</ul>
<h2>5. Edge Cases &amp; Correctness</h2>
<ul>
<li><strong><code>n = 1</code>:</strong> The algorithm correctly handles a single node. It picks it at <code>current_pos = 1</code>, calculates <code>score[0] * 1</code>, and then returns 0.</li>
<li><strong>No Edges (<code>edges</code> is empty):</strong> <code>prereq</code> will contain all zeros. Any node can be picked at any time. The algorithm will correctly find the optimal permutation based on scores.</li>
<li><strong>Graphs with Cycles:</strong> The code assumes a Directed Acyclic Graph (DAG) for a valid topological sort order. If cycles exist, nodes involved in a cycle whose prerequisites cannot be resolved outside the cycle will simply never satisfy the <code>(prereq[i] &amp; mask) == prereq[i]</code> condition and thus will not be picked. The algorithm will still find the maximum profit from valid sequences of non-cyclic nodes. If the problem <em>requires</em> all <code>n</code> nodes to be picked, cycles would make it impossible.</li>
<li><strong>All Scores Zero/Negative:</strong> <code>max_profit</code> is initialized to 0. If all possible node selections lead to a non-positive profit, the function will correctly return 0, effectively choosing to pick no nodes, which is a valid strategy when maximizing profit.</li>
</ul>
<h2>6. Improvements &amp; Alternatives</h2>
<ul>
<li><strong>Iterative DP:</strong> While <code>lru_cache</code> provides a clean recursive solution, an iterative DP approach could be written to avoid recursion overhead. This would involve iterating <code>mask</code> from <code>0</code> to <code>2^n - 1</code> and calculating <code>dp[mask]</code> based on <code>dp[smaller_mask]</code>.</li>
<li><strong>Clarity:</strong> The internal comments explaining <code>current_pos</code> are good. For very complex bitmask logic, more descriptive variable names or inline comments could sometimes help, though here it's quite standard.</li>
<li><strong>Problem Constraints:</strong> This <code>O(2^n * n)</code> approach is highly effective for <code>n</code> up to about 20-25. For larger <code>n</code>, alternative algorithms (e.g., approximation algorithms or different problem formulations) would be necessary if the problem constraints allowed.</li>
</ul>
<h2>7. Security/Performance Notes</h2>
<ul>
<li><strong>Performance Bottleneck:</strong> The exponential time complexity <code>O(2^n * n)</code> makes this solution unsuitable for large values of <code>n</code>. For <code>n=20</code>, <code>2^20 * 20</code> is approximately 20 million operations, which is feasible. For <code>n=30</code>, it becomes prohibitive.</li>
<li><strong>No Security Concerns:</strong> The code primarily performs numerical and bitwise operations on internal data structures. There are no external inputs/outputs or sensitive operations that introduce security vulnerabilities.</li>
</ul>


### Code:
```python
import functools

class Solution:
    def maxProfit(self, n: int, edges: List[List[int]], score: List[int]) -> int:
        # Precompute prerequisites for each node as a bitmask
        # prereq[i] contains a bit '1' at position j if j -> i
        prereq = [0] * n
        for u, v in edges:
            prereq[v] |= (1 << u)

        # Memoization cache
        @functools.lru_cache(None)
        def solve(mask):
            # count = current position we are filling (1-based)
            # If mask has 0 bits, we are filling position 1.
            # If mask has 1 bit, we have filled pos 1, now filling pos 2.
            # So, current_pos = count_set_bits(mask) + 1
            current_pos = mask.bit_count() + 1
            
            if current_pos > n:
                return 0
            
            max_profit = 0
            
            # Try to pick every node 'i' that hasn't been used yet
            for i in range(n):
                # Check if node i is NOT in mask
                if not (mask & (1 << i)):
                    # Check if all prerequisites of i are already in mask
                    if (prereq[i] & mask) == prereq[i]:
                        # Pick node i for the current_pos
                        current_profit = (score[i] * current_pos) + solve(mask | (1 << i))
                        if current_profit > max_profit:
                            max_profit = current_profit
            
            return max_profit

        return solve(0)
```
