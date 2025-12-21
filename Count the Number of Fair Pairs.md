## Count the Number of Fair Pairs
**Language:** python
**Tags:** python,sorting,two-pointers,list,oop

### Description:
<p>This code efficiently counts "fair pairs" in an array. A pair <code>(nums[i], nums[j])</code> where <code>i &lt; j</code> is considered "fair" if their sum <code>nums[i] + nums[j]</code> falls within a specified range <code>[lower, upper]</code>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The primary goal of this code is to find the number of unique pairs <code>(i, j)</code> with <code>i &lt; j</code> such that <code>lower &lt;= nums[i] + nums[j] &lt;= upper</code>. It achieves this by leveraging a sorted array and a two-pointer technique.</p>
<hr>
<h3>2. How It Works</h3>
<ol>
<li><strong>Sort the Array</strong>: The <code>nums</code> array is first sorted in non-decreasing order. This is a crucial preprocessing step that enables the efficient two-pointer approach.</li>
<li><strong>Helper Function <code>count_pairs_less_than_or_equal(target_sum)</code></strong>:<ul>
<li>This inner function takes a <code>target_sum</code> as input and counts all pairs <code>(i, j)</code> with <code>i &lt; j</code> such that <code>nums[i] + nums[j] &lt;= target_sum</code>.</li>
<li>It uses a two-pointer approach:<ul>
<li><code>left</code> pointer starts at the beginning of the sorted array (index 0).</li>
<li><code>right</code> pointer starts at the end of the sorted array (index <code>n-1</code>).</li>
</ul>
</li>
<li>While <code>left &lt; right</code>:<ul>
<li>Calculate <code>current_sum = nums[left] + nums[right]</code>.</li>
<li><strong>If <code>current_sum &lt;= target_sum</code></strong>: This means <code>nums[left]</code> can form a valid pair with <code>nums[right]</code> <em>and all elements between <code>nums[left]</code> and <code>nums[right]</code></em>. Specifically, <code>nums[left]</code> will form a sum less than or equal to <code>target_sum</code> with <code>nums[left+1]</code>, <code>nums[left+2]</code>, ..., up to <code>nums[right]</code>. The count is incremented by <code>(right - left)</code>. The <code>left</code> pointer is then moved one step to the right to consider the next smallest element.</li>
<li><strong>If <code>current_sum &gt; target_sum</code></strong>: This means <code>nums[left]</code> is too large when paired with <code>nums[right]</code>. To reduce the sum, the <code>right</code> pointer is moved one step to the left, considering a smaller element.</li>
</ul>
</li>
<li>The function returns the total count of such pairs.</li>
</ul>
</li>
<li><strong>Final Calculation</strong>: The <code>countFairPairs</code> function calls <code>count_pairs_less_than_or_equal</code> twice:<ul>
<li>Once with <code>upper</code> to get all pairs whose sum is <code>STATE &lt;= upper</code>.</li>
<li>Once with <code>lower - 1</code> to get all pairs whose sum is <code>STATE &lt;= lower - 1</code>.</li>
<li>The difference between these two counts <code>(count(sum &lt;= upper) - count(sum &lt;= lower - 1))</code> yields exactly the number of pairs whose sum is <code>lower &lt;= sum &lt;= upper</code>.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Sorting the Array</strong>: This is the fundamental decision. Sorting allows for the efficient two-pointer technique to find pairs with specific sum properties. Without sorting, a brute-force <code>O(N^2)</code> approach would be required.</li>
<li><strong>Two-Pointer Technique</strong>: For a sorted array, the two-pointer approach (one from start, one from end) is ideal for finding pairs that satisfy a sum condition (e.g., <code>sum &lt; X</code>, <code>sum &gt; X</code>, <code>sum == X</code>). It efficiently explores potential sums by converging the pointers.</li>
<li><strong>Decomposition into Helper Function</strong>: Encapsulating the "count pairs less than or equal to a target" logic into <code>count_pairs_less_than_or_equal</code> improves modularity and readability. It also clearly isolates the core counting mechanism.</li>
<li><strong>Inclusive-Exclusive Principle for Range Counting</strong>: The strategy <code>count(X &lt;= upper) - count(X &lt;= lower - 1)</code> is a standard and elegant way to count elements within an inclusive range <code>[lower, upper]</code> when you have a function that counts elements up to an inclusive limit.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity</strong>:<ul>
<li><code>nums.sort()</code>: <code>O(N log N)</code> due to Python's Timsort algorithm.</li>
<li><code>count_pairs_less_than_or_equal()</code>: Each call takes <code>O(N)</code> time. The <code>left</code> pointer only moves forward, <code>right</code> pointer only moves backward, and they cross at most once.</li>
<li>Total Time Complexity: <code>O(N log N)</code> (dominated by the sorting step).</li>
</ul>
</li>
<li><strong>Space Complexity</strong>:<ul>
<li><code>nums.sort()</code>: <code>O(N)</code> in the worst case for Timsort (e.g., if a merge sort pass needs auxiliary space). In some cases, it can be <code>O(log N)</code> for recursion stack space for nearly sorted arrays.</li>
<li><code>count_pairs_less_than_or_equal()</code>: <code>O(1)</code> additional space (a few variables for pointers and count).</li>
<li>Total Space Complexity: <code>O(N)</code> (dominated by the space required for sorting).</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty <code>nums</code> array</strong>:<ul>
<li><code>n</code> will be 0. <code>nums.sort()</code> does nothing.</li>
<li><code>left &lt; right</code> condition in <code>count_pairs_less_than_or_equal</code> will never be met.</li>
<li>Both calls to the helper will return 0. The final result will be <code>0 - 0 = 0</code>, which is correct as no pairs exist.</li>
</ul>
</li>
<li><strong><code>nums</code> with a single element</strong>:<ul>
<li>Similar to the empty array case, <code>n</code> will be 1. <code>left &lt; right</code> will never be met.</li>
<li>Returns 0, which is correct.</li>
</ul>
</li>
<li><strong>All elements are the same</strong>:<ul>
<li>The sorting has no effect. The two-pointer logic correctly counts pairs. For example, if <code>nums = [1, 1, 1]</code> and <code>target_sum = 2</code>:<ul>
<li><code>left=0, right=2</code>, <code>sum=2</code>. <code>count += (2-0) = 2</code>. <code>left=1</code>.</li>
<li><code>left=1, right=2</code>, <code>sum=2</code>. <code>count += (2-1) = 1</code>. <code>left=2</code>.</li>
<li><code>left=2, right=2</code>, loop terminates. Total <code>count = 3</code>. This is correct: <code>(0,1), (0,2), (1,2)</code>.</li>
</ul>
</li>
</ul>
</li>
<li><strong><code>lower &gt; upper</code></strong>:<ul>
<li>While problem constraints usually imply <code>lower &lt;= upper</code>, if <code>lower</code> were greater, the range <code>[lower, upper]</code> would be empty. The code would correctly return 0 because <code>count_pairs_less_than_or_equal(upper)</code> would be less than or equal to <code>count_pairs_less_than_or_equal(lower - 1)</code>. For example, <code>count(sum &lt;= 5) - count(sum &lt;= 6)</code> would yield 0 or a negative result, but problem constraints on <code>lower</code> and <code>upper</code> for array sums typically ensure <code>count(sum &lt;= upper) &gt;= count(sum &lt;= lower - 1)</code>.</li>
</ul>
</li>
<li><strong>Pairs with identical indices <code>i == j</code></strong>: The two-pointer <code>left &lt; right</code> condition inherently ensures that <code>i</code> and <code>j</code> are distinct and <code>i &lt; j</code>, adhering to the standard definition of a pair.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>: The code is already quite readable. Adding docstrings to the class and methods would further enhance it, especially for a public API.</li>
<li><strong>No Asymptotic Performance Improvements</strong>: For arbitrary integer arrays, <code>O(N log N)</code> is generally the best-achievable time complexity using comparison-based sorting. Alternatives like hash maps or frequency arrays would typically only offer improvements if the range of values in <code>nums</code> was very small (making counting sort feasible) or if the elements themselves were small integers enabling specific data structures (like Fenwick trees), which would introduce more complexity.</li>
<li><strong>Alternative <code>count_pairs_less_than_or_equal</code></strong>: Instead of the two-pointer approach, one could iterate with <code>left</code> from <code>0</code> to <code>n-2</code>. For each <code>nums[left]</code>, a binary search (<code>bisect_right</code>) could be used on the subarray <code>nums[left+1:]</code> to find the largest index <code>k</code> such that <code>nums[left] + nums[k] &lt;= target_sum</code>. This would result in <code>O(N log N)</code> for the helper function, making the total <code>O(N log N)</code> but likely with a higher constant factor than the two-pointer approach for the helper. The current two-pointer for the helper is <code>O(N)</code>, which is more efficient.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this algorithm. It's a purely computational problem that doesn't interact with external systems, user input, or sensitive data in a way that would introduce common security risks.</li>
<li><strong>Performance</strong>: The <code>O(N log N)</code> performance is robust for typical input sizes. Python's arbitrary-precision integers mean there's no risk of integer overflow during sum calculations, which simplifies correctness but could theoretically impact performance for extremely large numbers (though this is rarely a concern for typical competitive programming/interview problem constraints). The constant factor for the two calls to <code>count_pairs_less_than_or_equal</code> is negligible.</li>
</ul>


### Code:
```python
class Solution:
    def countFairPairs(self, nums: List[int], lower: int, upper: int) -> int:
        nums.sort()
        n = len(nums)

        def count_pairs_less_than_or_equal(target_sum: int) -> int:
            count = 0
            left = 0
            right = n - 1
            while left < right:
                current_sum = nums[left] + nums[right]
                if current_sum <= target_sum:
                    count += (right - left)
                    left += 1
                else:
                    right -= 1
            return count
        
        return count_pairs_less_than_or_equal(upper) - count_pairs_less_than_or_equal(lower - 1)
```
