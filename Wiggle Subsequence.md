---

## Wiggle Subsequence
**Language:** python


### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This code aims to find the maximum length of a "wiggle subsequence" in a given list of integers (<code>nums</code>).</p>
<ul>
<li><strong>Wiggle Subsequence</strong>: A sequence where the differences between consecutive numbers strictly alternate between positive and negative. For example, <code>[1, 7, 4, 9, 2, 5]</code> is a wiggle subsequence because <code>6 &gt; 0</code>, <code>-3 &lt; 0</code>, <code>5 &gt; 0</code>, <code>-7 &lt; 0</code>, <code>3 &gt; 0</code>.</li>
<li><strong>Goal</strong>: Determine the longest possible wiggle subsequence that can be formed from the input array <code>nums</code> by deleting zero or more elements.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The solution uses a dynamic programming approach with optimized space complexity to track the longest wiggle subsequence.</p>
<ul>
<li><strong>Base Case</strong>: If the input list has fewer than 2 elements, it's already a wiggle subsequence of its own length (0 for empty, 1 for single element).</li>
<li><strong>Initialization</strong>:<ul>
<li><code>up = 1</code>: Represents the maximum length of a wiggle subsequence ending with an <em>increasing</em> difference (e.g., <code>...a, b</code> where <code>b &gt; a</code>). It's initialized to <code>1</code> because any single number forms a wiggle subsequence of length 1.</li>
<li><code>down = 1</code>: Represents the maximum length of a wiggle subsequence ending with a <em>decreasing</em> difference (e.g., <code>...a, b</code> where <code>b &lt; a</code>). Also initialized to <code>1</code>.</li>
</ul>
</li>
<li><strong>Iteration</strong>: The code iterates through the <code>nums</code> array starting from the second element (<code>i=1</code>).<ul>
<li><strong>Calculate Difference</strong>: For each element <code>nums[i]</code>, it calculates the difference <code>diff = nums[i] - nums[i-1]</code>.</li>
<li><strong>Update <code>up</code></strong>: If <code>diff &gt; 0</code> (current segment is increasing):<ul>
<li>This means <code>nums[i]</code> could extend a wiggle subsequence that <em>previously ended with a decreasing difference</em>.</li>
<li>So, <code>up</code> is updated to <code>down + 1</code>. This effectively "switches" the direction from a down-wiggle to an up-wiggle, extending the length.</li>
</ul>
</li>
<li><strong>Update <code>down</code></strong>: If <code>diff &lt; 0</code> (current segment is decreasing):<ul>
<li>Similarly, <code>nums[i]</code> could extend a wiggle subsequence that <em>previously ended with an increasing difference</em>.</li>
<li>So, <code>down</code> is updated to <code>up + 1</code>. This "switches" the direction from an up-wiggle to a down-wiggle.</li>
</ul>
</li>
<li><strong>Ignore Zero Difference</strong>: If <code>diff == 0</code>, no update occurs. A zero difference does not contribute to a "wiggle" (which requires strict increase or decrease), so we effectively skip <code>nums[i]</code> and maintain the previous <code>up</code> and <code>down</code> lengths.</li>
</ul>
</li>
<li><strong>Result</strong>: After iterating through all elements, the maximum of <code>up</code> and <code>down</code> is returned. This accounts for the possibility that the longest wiggle subsequence might end with either an increasing or decreasing pair.</li>
</ul>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Greedy Dynamic Programming</strong>: The solution uses a greedy approach where at each step, it makes the locally optimal choice based on the <code>up</code> and <code>down</code> lengths accumulated so far. This works because the decision to extend an 'up' or 'down' subsequence only depends on the length of the <em>opposite</em> subsequence and doesn't need to look back further.</li>
<li><strong>Space Optimization</strong>: Instead of a full DP table (e.g., <code>dp[i][0]</code> for up, <code>dp[i][1]</code> for down), which would require O(N) space, the solution cleverly uses just two variables (<code>up</code>, <code>down</code>) to store the necessary information from the previous step. This reduces space complexity to O(1).</li>
<li><strong>Handling Zero Differences</strong>: The decision to not update <code>up</code> or <code>down</code> when <code>diff == 0</code> is crucial. It correctly ensures that only strictly alternating differences count towards the wiggle length. If <code>nums[i] == nums[i-1]</code>, that <code>nums[i]</code> cannot extend a wiggle and can be effectively "skipped" to find the longest subsequence.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The algorithm iterates through the <code>nums</code> list exactly once, from the second element to the end.</li>
<li>Each step involves a constant number of operations (subtraction, comparison, assignment).</li>
<li>Therefore, the time taken scales linearly with the number of elements <code>N</code> in <code>nums</code>.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>The algorithm uses a fixed number of variables (<code>up</code>, <code>down</code>, <code>i</code>, <code>diff</code>) regardless of the input size.</li>
<li>No auxiliary data structures that grow with <code>N</code> are used.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<p>The solution handles various edge cases correctly:</p>
<ul>
<li><strong>Empty list (<code>[]</code>)</strong>: <code>len(nums) &lt; 2</code> returns <code>0</code>. Correct.</li>
<li><strong>Single element list (<code>[5]</code>)</strong>: <code>len(nums) &lt; 2</code> returns <code>1</code>. Correct.</li>
<li><strong>Two elements (<code>[1, 2]</code>, <code>[2, 1]</code>, <code>[1, 1]</code>)</strong>:<ul>
<li><code>[1, 2]</code>: <code>diff = 1</code>. <code>up</code> becomes <code>2</code>. <code>max(2, 1) = 2</code>. Correct.</li>
<li><code>[2, 1]</code>: <code>diff = -1</code>. <code>down</code> becomes <code>2</code>. <code>max(1, 2) = 2</code>. Correct.</li>
<li><code>[1, 1]</code>: <code>diff = 0</code>. <code>up</code> and <code>down</code> remain <code>1</code>. <code>max(1, 1) = 1</code>. Correct (the single element <code>1</code> is a wiggle subsequence).</li>
</ul>
</li>
<li><strong>All same elements (<code>[7, 7, 7, 7]</code>)</strong>: <code>diff</code> is always <code>0</code>. <code>up</code> and <code>down</code> remain <code>1</code>. Returns <code>1</code>. Correct.</li>
<li><strong>Monotonically increasing (<code>[1, 2, 3, 4]</code>)</strong>: <code>diff</code> is always <code>&gt;0</code>. <code>up</code> becomes <code>2</code> repeatedly, <code>down</code> stays <code>1</code>. Returns <code>2</code>. Correct (e.g., <code>[1, 2]</code> is the longest wiggle subsequence).</li>
<li><strong>Monotonically decreasing (<code>[4, 3, 2, 1]</code>)</strong>: <code>diff</code> is always <code>&lt;0</code>. <code>down</code> becomes <code>2</code> repeatedly, <code>up</code> stays <code>1</code>. Returns <code>2</code>. Correct (e.g., <code>[4, 3]</code> is the longest wiggle subsequence).</li>
<li><strong>General Wiggle (<code>[1, 7, 4, 9, 2, 5]</code>)</strong>:<ul>
<li><code>up=1, down=1</code></li>
<li><code>[1, 7]</code> (diff=6): <code>up=2, down=1</code></li>
<li><code>[7, 4]</code> (diff=-3): <code>down=3, up=2</code></li>
<li><code>[4, 9]</code> (diff=5): <code>up=4, down=3</code></li>
<li><code>[9, 2]</code> (diff=-7): <code>down=5, up=4</code></li>
<li><code>[2, 5]</code> (diff=3): <code>up=6, down=5</code></li>
<li>Returns <code>max(6, 5) = 6</code>. Correct.</li>
</ul>
</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability (Minor)</strong>: The code is already very concise and readable for those familiar with the problem. For someone new, a short comment explaining what <code>up</code> and <code>down</code> represent might be helpful:<pre><code class="language-python"># up: length of the longest wiggle subsequence ending with an increasing difference
# down: length of the longest wiggle subsequence ending with a decreasing difference
</code></pre>
</li>
<li><strong>Alternative (Peak-Valley Counting)</strong>: Another common approach is to count peaks and valleys.<ol>
<li>First, filter out consecutive duplicate numbers (e.g., <code>[1, 1, 7, 4, 4, 9]</code> becomes <code>[1, 7, 4, 9]</code>).</li>
<li>If the filtered array has fewer than 2 elements, return its length.</li>
<li>Otherwise, the length of the longest wiggle subsequence is <code>number_of_peaks + number_of_valleys + 1</code> (for the first element). This approach is slightly more involved to implement correctly due to edge cases at the start/end and handling plateaus, but conceptually it achieves the same result. The current DP solution is often considered cleaner and more robust.</li>
</ol>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this code. It performs basic arithmetic and array access on numerical input, without external dependencies or sensitive operations.</li>
<li><strong>Performance</strong>: The performance is optimal. O(N) time and O(1) space are the best possible complexities for this problem, as every element must be at least considered once. No further performance optimizations are practically achievable.</li>
</ul>


### Code:
```python
class Solution:
    def wiggleMaxLength(self, nums: List[int]) -> int:
        if len(nums) < 2:
            return len(nums)
        up = 1
        down = 1

        for i in range(1, len(nums)):
            diff = nums[i] - nums[i-1]
            if diff > 0:
                up = down + 1
            elif diff < 0:
                down = up + 1

        return max(up, down)
```

---
