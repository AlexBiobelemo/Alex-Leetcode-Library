## Make K Subarray Sums Equal
**Language:** python
**Tags:** python,oop,sorting,median,number theory

### Description:
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The code aims to minimize the total number of operations required to make certain groups of elements in an array equal. An "operation" is defined as changing an element's value by 1. Specifically, it considers elements that are <code>k</code> positions apart in a circular manner (i.e., <code>arr[j]</code>, <code>arr[(j+k)%n]</code>, <code>arr[(j+2k)%n]</code>, etc.) to belong to the same interdependent group or "cycle". The goal is to find the minimum operations to make all elements <em>within each cycle</em> equal.</p>
<p>The problem implicitly leverages a key mathematical property: to minimize the sum of absolute differences <code>sum(abs(x_i - X))</code> for a set of numbers <code>x_i</code>, the target value <code>X</code> should be the median of that set.</p>
<h3>2. How It Works</h3>
<ol>
<li><p><strong>Identify Cycles</strong>:</p>
<ul>
<li>It first calculates <code>n = len(arr)</code> (the array length).</li>
<li>It then computes <code>g = math.gcd(n, k)</code>, the greatest common divisor of <code>n</code> and <code>k</code>. This <code>g</code> is crucial because it represents the number of independent, disjoint cycles or groups that are formed when traversing the array by steps of <code>k</code> modulo <code>n</code>.</li>
<li>There will be <code>g</code> such cycles, each of length <code>n // g</code>.</li>
</ul>
</li>
<li><p><strong>Process Each Cycle</strong>:</p>
<ul>
<li>The code iterates <code>g</code> times using <code>for i in range(g)</code>. Each <code>i</code> serves as the starting index for a distinct cycle.</li>
<li>For each cycle, it collects all its elements into <code>current_cycle_elements</code>. This is done by starting at <code>arr[i]</code> and repeatedly adding <code>k</code> (modulo <code>n</code>) to the index until <code>n // g</code> elements are gathered.</li>
<li>It then sorts <code>current_cycle_elements</code> to find its median.</li>
</ul>
</li>
<li><p><strong>Calculate Minimum Operations for a Cycle</strong>:</p>
<ul>
<li>The <code>median_val</code> is determined by taking the middle element of the sorted <code>current_cycle_elements</code>.</li>
<li>For each element <code>val</code> in the cycle, the absolute difference <code>abs(val - median_val)</code> is calculated and added to <code>total_operations</code>. This is the minimum cost to make all elements in that specific cycle equal to their median.</li>
</ul>
</li>
<li><p><strong>Aggregate Result</strong>:</p>
<ul>
<li><code>total_operations</code> accumulates the costs from all <code>g</code> cycles and is returned as the final result.</li>
</ul>
</li>
</ol>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong><code>math.gcd(n, k)</code> for Cycle Identification</strong>: This is the most critical design decision. When stepping <code>k</code> positions at a time in a circular array of size <code>n</code>, the sequence of visited indices <code>j, (j+k)%n, (j+2k)%n, ...</code> will form a cycle. The length of this cycle is <code>n / gcd(n, k)</code>, and there are <code>gcd(n, k)</code> such distinct, non-overlapping cycles. This ensures that elements that are truly interdependent are grouped together, and independent groups are processed separately.</li>
<li><strong>Median as Optimal Target</strong>: For a set of numbers, making all of them equal to their median minimizes the sum of absolute differences. This mathematical property is fundamental to the correctness of the operation calculation.</li>
<li><strong>Sorting to Find Median</strong>: Sorting each cycle's elements is a straightforward way to find the median. While other methods exist (like Quickselect), sorting is simple to implement and understand.</li>
</ul>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the length of the array <code>arr</code>.</p>
<ul>
<li><p><strong>Time Complexity</strong>:</p>
<ul>
<li><code>math.gcd(N, k)</code>: <code>O(log(min(N, k)))</code></li>
<li>The outer loop runs <code>g</code> times.</li>
<li>Inside the loop:<ul>
<li>Collecting <code>N // g</code> elements: <code>O(N // g)</code></li>
<li>Sorting <code>N // g</code> elements: <code>O((N // g) * log(N // g))</code></li>
<li>Summing <code>N // g</code> differences: <code>O(N // g)</code></li>
</ul>
</li>
<li>Total time complexity: <code>g * (O(N // g) + O((N // g) * log(N // g)) + O(N // g))</code>.</li>
<li>This simplifies to <code>O(N * log(N // g))</code>.</li>
<li>In the worst case (when <code>g = 1</code>, meaning one large cycle of <code>N</code> elements), this is <code>O(N log N)</code>.</li>
<li>In the best case (when <code>g = N</code>, meaning <code>N</code> cycles of 1 element each), this is <code>O(N)</code>.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>:</p>
<ul>
<li><code>current_cycle_elements</code> stores <code>N // g</code> integers.</li>
<li>Total auxiliary space complexity: <code>O(N // g)</code>.</li>
<li>In the worst case (when <code>g = 1</code>), this is <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>N = 1</code></strong>: <code>g = gcd(1, k) = 1</code>. One cycle of 1 element. <code>median_val</code> is the element itself. <code>total_operations</code> will be 0. Correct.</li>
<li><strong><code>k = N</code></strong>: <code>g = gcd(N, N) = N</code>. There will be <code>N</code> cycles, each containing a single element. <code>median_val</code> for each cycle is its sole element. <code>total_operations</code> will be 0. Correct, as each element is already "equal" within its own single-element cycle.</li>
<li><strong><code>k = 1</code></strong>: <code>g = gcd(N, 1) = 1</code>. All <code>N</code> elements form one single cycle. The code correctly gathers all elements, sorts them, finds the median, and sums differences. Correct.</li>
<li><strong><code>arr</code> with all identical elements</strong>: <code>total_operations</code> will be 0, as <code>abs(val - median_val)</code> will always be 0. Correct.</li>
<li><strong>Empty <code>arr</code></strong>: The code would raise an <code>IndexError</code> on <code>len(arr)</code> or <code>current_cycle_elements[len(current_cycle_elements) // 2]</code>. An initial check for <code>n == 0</code> would make it robust.</li>
</ul>
<p>The use of <code>math.gcd</code> and the median property makes the core logic robust for various <code>N</code> and <code>k</code> values, assuming <code>N &gt; 0</code>.</p>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Performance (Median Finding)</strong>: Instead of fully sorting <code>current_cycle_elements</code> (which is <code>O(M log M)</code> where <code>M = N // g</code>), we can use an algorithm like Quickselect (or <code>nth_element</code> in C++ STL) to find the median in average <code>O(M)</code> time.<ul>
<li>If implemented, the total time complexity would improve from <code>O(N * log(N // g))</code> to average <code>O(N)</code>. This is a significant optimization for very large <code>N</code> where <code>N // g</code> is also large. Python's standard library does not expose <code>nth_element</code> directly for lists, but it could be implemented or found in libraries like <code>numpy</code> if allowed.</li>
</ul>
</li>
<li><strong>Readability/Comments</strong>: While the variable names are good, adding a comment to explain <em>why</em> <code>g = math.gcd(n, k)</code> is used (i.e., "determines the number of disjoint cycles formed by steps of k modulo n") would enhance the educational aspect for someone new to this problem pattern.</li>
<li><strong>Input Validation</strong>: For production-grade code, adding checks for <code>arr</code> being non-empty (<code>n &gt; 0</code>) would prevent potential errors.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: As mentioned, the <code>O(N log(N/g))</code> complexity is generally efficient. For extremely large inputs (e.g., <code>N</code> in the tens of millions or more), the <code>O(N)</code> average-case median finding via Quickselect would become a critical optimization to consider. Python's default sort (Timsort) is highly optimized, but its worst-case <code>O(N log N)</code> behavior remains.</li>
<li><strong>Security</strong>: There are no apparent security vulnerabilities in this code. It performs numerical operations on an internal array and does not interact with external systems, user inputs (beyond the array itself), or sensitive data in a way that would introduce common security risks like injection, data leakage, or denial-of-service from malicious input values (assuming array elements are within standard integer limits). Python's arbitrary-precision integers mitigate overflow concerns for <code>total_operations</code>.</li>
</ul>


### Code:
```python
import math

class Solution:
    def makeSubKSumEqual(self, arr: List[int], k: int) -> int:
        n = len(arr)
        
        g = math.gcd(n, k)
        
        total_operations = 0
        
        for i in range(g):
            current_cycle_elements = []
            
            j = i
            for _ in range(n // g):
                current_cycle_elements.append(arr[j])
                j = (j + k) % n
            
            current_cycle_elements.sort()
            
            median_val = current_cycle_elements[len(current_cycle_elements) // 2]
            
            for val in current_cycle_elements:
                total_operations += abs(val - median_val)
                
        return total_operations
```
