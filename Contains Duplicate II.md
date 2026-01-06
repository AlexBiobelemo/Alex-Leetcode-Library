## Contains Duplicate II
**Language:** python

### Description:
<p>This code addresses the "Contains Duplicate II" problem, which asks to determine if there are two distinct indices <code>i</code> and <code>j</code> in an array of integers <code>nums</code> such that <code>nums[i] == nums[j]</code> and <code>abs(i - j) &lt;= k</code>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem:</strong> Check for duplicate numbers within a specified distance <code>k</code> in an array.</li>
<li><strong>Goal:</strong> Return <code>True</code> if any two identical numbers are found at indices <code>i</code> and <code>j</code> such that the absolute difference <code>|i - j|</code> is less than or equal to <code>k</code>. Otherwise, return <code>False</code>.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The code iterates through the array <code>nums</code> once, keeping track of the most recent index seen for each number using a hash map (Python dictionary).</p>
<ol>
<li><strong>Initialization:</strong> An empty dictionary <code>num_indices</code> is created. This dictionary will store <code>number: last_seen_index</code> pairs.</li>
<li><strong>Iteration:</strong> The code iterates through <code>nums</code> using <code>enumerate</code> to get both the index <code>i</code> and the value <code>num</code> at each step.</li>
<li><strong>Duplicate Check:</strong> For each <code>num</code>:<ul>
<li>It checks if <code>num</code> is already a key in <code>num_indices</code>.<ul>
<li>If <code>num</code> is found, it means this number has appeared before.</li>
<li>The distance between the current index <code>i</code> and its previous index (<code>num_indices[num]</code>) is calculated: <code>i - num_indices[num]</code>.</li>
<li>If this distance is less than or equal to <code>k</code>, a nearby duplicate is found, and the function immediately returns <code>True</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong>Index Update:</strong> Regardless of whether a duplicate was found or not, the <code>num_indices</code> dictionary is updated (or a new entry is created) for the current <code>num</code> with its current index <code>i</code>. This ensures that <code>num_indices[num]</code> always stores the <em>most recent</em> index encountered for <code>num</code>.</li>
<li><strong>No Duplicates:</strong> If the loop completes without finding any nearby duplicates, the function returns <code>False</code>.</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Data Structure: Hash Map (Python <code>dict</code>)</strong><ul>
<li><strong>Choice:</strong> <code>num_indices</code> is a hash map, mapping numbers to their latest encountered index.</li>
<li><strong>Rationale:</strong> Hash maps provide average O(1) time complexity for insertions, lookups, and updates. This is crucial for efficient checking of previously seen numbers and their positions.</li>
</ul>
</li>
<li><strong>Algorithm: Single Pass Iteration</strong><ul>
<li><strong>Choice:</strong> The array is traversed exactly once.</li>
<li><strong>Rationale:</strong> By processing each element once and using the hash map to store relevant information, the algorithm avoids nested loops (which would lead to O(N^2) time complexity).</li>
</ul>
</li>
<li><strong>Trade-offs:</strong><ul>
<li><strong>Space vs. Time:</strong> The solution uses O(N) additional space in the worst case (if all numbers are unique) to achieve O(N) time complexity. A brute-force O(N^2) time solution would use O(1) space. The hash map approach is generally preferred for performance.</li>
</ul>
</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>The code iterates through the <code>nums</code> array once, which has <code>N</code> elements.</li>
<li>Inside the loop, dictionary operations (lookup, insertion, and update) take O(1) time on average.</li>
<li>Therefore, the total time complexity is dominated by the single pass through the array.</li>
</ul>
</li>
<li><strong>Space Complexity: O(U) or O(N) in worst case</strong><ul>
<li>The <code>num_indices</code> dictionary stores at most <code>U</code> unique numbers, where <code>U &lt;= N</code>.</li>
<li>In the worst case (all numbers in <code>nums</code> are unique), the dictionary will store <code>N</code> entries.</li>
<li>Therefore, the space complexity is O(N) in the worst case.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>nums</code> array:</strong> The loop will not execute, and the function will correctly return <code>False</code>.</li>
<li><strong><code>k = 0</code>:</strong> The condition <code>i - num_indices[num] &lt;= 0</code> implies <code>i == num_indices[num]</code>, which is impossible for distinct indices (as <code>i</code> is strictly increasing). No duplicate will be found, correctly returning <code>False</code>.</li>
<li><strong><code>k</code> is large (e.g., <code>k &gt;= N - 1</code>):</strong> In this case, any duplicate number found will satisfy the <code>abs(i - j) &lt;= k</code> condition, as the maximum possible distance is <code>N-1</code>. The code correctly identifies the first such duplicate.</li>
<li><strong>All unique numbers:</strong> The <code>if num in num_indices:</code> condition will never be true, and the loop will complete, returning <code>False</code>. Correct.</li>
<li><strong>All identical numbers:</strong> e.g., <code>nums = [1, 1, 1], k = 1</code>.<ul>
<li><code>i=0, num=1</code>: <code>num_indices[1] = 0</code>.</li>
<li><code>i=1, num=1</code>: <code>1</code> is in <code>num_indices</code>. <code>1 - num_indices[1]</code> (<code>1 - 0</code>) is <code>1</code>. <code>1 &lt;= k</code> (true). Returns <code>True</code>. Correct.</li>
</ul>
</li>
<li><strong>Duplicates outside <code>k</code> range:</strong> e.g., <code>nums = [1, 2, 3, 1], k = 2</code>.<ul>
<li><code>i=0, num=1</code>: <code>num_indices[1] = 0</code>.</li>
<li><code>i=1, num=2</code>: <code>num_indices[2] = 1</code>.</li>
<li><code>i=2, num=3</code>: <code>num_indices[3] = 2</code>.</li>
<li><code>i=3, num=1</code>: <code>1</code> is in <code>num_indices</code>. <code>3 - num_indices[1]</code> (<code>3 - 0</code>) is <code>3</code>. <code>3 &lt;= k</code> (<code>3 &lt;= 2</code>) is false. <code>num_indices[1]</code> is updated to <code>3</code>.</li>
<li>Loop ends, returns <code>False</code>. Correct.</li>
</ul>
</li>
</ul>
<p>The logic correctly tracks the <em>last seen</em> index for each number. When a number is encountered again, checking the distance from its <em>last seen</em> index ensures that the <code>abs(i - j) &lt;= k</code> condition is evaluated against the most recent possible previous occurrence, which is what the problem asks for.</p>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is already very concise, clear, and uses idiomatic Python, making it highly readable. No significant readability improvements are needed.</li>
<li><strong>Alternative Approach: Sliding Window with a Set</strong><ul>
<li>Instead of a dictionary storing indices, one could use a <code>set</code> to store elements within a "sliding window" of size <code>k+1</code>.</li>
<li>For each element <code>nums[i]</code>:<ol>
<li>If <code>i &gt; k</code>, remove <code>nums[i - k - 1]</code> from the set (to maintain the window of size <code>k+1</code>).</li>
<li>Check if <code>nums[i]</code> is already in the set. If yes, return <code>True</code>.</li>
<li>Add <code>nums[i]</code> to the set.</li>
</ol>
</li>
<li>This approach also has O(N) time complexity and O(k) space complexity (as the set holds at most <code>k+1</code> elements). It's conceptually similar to the hash map solution, but focuses on the window constraint more explicitly. The provided dictionary solution essentially achieves the same by always updating the last seen index, effectively disregarding numbers outside the <code>k</code> distance automatically (because we only care about the <em>last</em> occurrence for distance calculation).</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance:</strong> For very large input arrays (<code>N</code>), the O(N) space complexity could be a concern if memory is extremely constrained, but for typical competitive programming limits, it's generally acceptable. The average O(1) hash map operations ensure it's very efficient time-wise.</li>
<li><strong>Security:</strong> This algorithm has no inherent security vulnerabilities. It processes an array of integers and does not interact with external systems or sensitive data.</li>
</ul>


### Code:
```python
class Solution:
    def containsNearbyDuplicate(self, nums: List[int], k: int) -> bool:
        num_indices = {}
        for i, num in enumerate(nums):
            if num in num_indices:
                if i - num_indices[num] <= k:
                    return True
            num_indices[num] = i
        return False
```
