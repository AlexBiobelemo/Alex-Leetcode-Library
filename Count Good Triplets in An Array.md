## Count Good Triplets in An Array
**Language:** python
**Tags:** python,oop,fenwick tree,prefix sums

### Description:
<p>This code efficiently counts "good triplets" across two permutations <code>nums1</code> and <code>nums2</code> using a Fenwick Tree (also known as a Binary Indexed Tree, or BIT).</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<ul>
<li><strong>Problem Statement</strong>: The <code>goodTriplets</code> method aims to find the number of triplets <code>(i, j, k)</code> such that <code>0 &lt;= i &lt; j &lt; k &lt; n</code>, and for these specific indices:<ol>
<li>The values in <code>nums1</code> are strictly increasing: <code>nums1[i] &lt; nums1[j] &lt; nums1[k]</code></li>
<li>The values in <code>nums2</code> are strictly increasing: <code>nums2[i] &lt; nums2[j] &lt; nums2[k]</code></li>
</ol>
<ul>
<li><code>nums1</code> and <code>nums2</code> are guaranteed to be permutations of <code>0</code> to <code>n-1</code>.</li>
</ul>
</li>
<li><strong>FenwickTree (BIT)</strong>: This class implements a Fenwick Tree, a data structure that allows for efficient prefix sum queries and single-point updates on an array. Both operations take <code>O(log N)</code> time, where <code>N</code> is the size of the array. It's particularly useful for counting elements within a range.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The core idea is to transform the problem from two arrays into a single array, then use two sweeps with Fenwick Trees to count elements that satisfy the increasing conditions.</p>
<ol>
<li><p><strong>Preprocessing &amp; Transformation</strong>:</p>
<ul>
<li><code>pos1</code>: An array where <code>pos1[value]</code> stores the <em>index</em> of <code>value</code> in <code>nums1</code>. For example, if <code>nums1 = [2, 0, 1]</code>, <code>pos1 = [1, 2, 0]</code>.</li>
<li><code>pos2</code>: An array where <code>pos2[value]</code> stores the <em>index</em> of <code>value</code> in <code>nums2</code>.</li>
<li><code>new_arr</code>: This is the crucial transformation. <code>new_arr[idx]</code> stores <code>pos2[nums1[idx]]</code>. This means <code>new_arr[idx]</code> gives the <em>index in <code>nums2</code></em> where the <em>value that is at <code>nums1[idx]</code></em> is located.<ul>
<li>Effectively, a good triplet <code>(i, j, k)</code> now means <code>i &lt; j &lt; k</code> AND <code>nums1[i] &lt; nums1[j] &lt; nums1[k]</code> AND <code>new_arr[i] &lt; new_arr[j] &lt; new_arr[k]</code>. The problem statement is slightly different from the way it's phrased above, but the code implicitly handles <code>nums1[i] &lt; nums1[j] &lt; nums1[k]</code> by iterating <code>j</code> over the original indices <code>0..n-1</code>. The <code>new_arr</code> then focuses on the corresponding positions in <code>nums2</code>.</li>
</ul>
</li>
</ul>
</li>
<li><p><strong>Counting Left Elements (<code>left_counts</code>)</strong>:</p>
<ul>
<li>A Fenwick Tree <code>bit_left</code> is initialized.</li>
<li>The code iterates through <code>j</code> from <code>0</code> to <code>n-1</code>.</li>
<li>For each <code>j</code>, <code>left_counts[j] = bit_left.query(new_arr[j] - 1)</code>. This counts how many <code>i &lt; j</code> exist such that <code>new_arr[i] &lt; new_arr[j]</code>. In other words, how many elements <em>to the left</em> of <code>j</code> have a smaller <code>new_arr</code> value.</li>
<li>After computing <code>left_counts[j]</code>, <code>bit_left.update(new_arr[j], 1)</code> adds <code>new_arr[j]</code> to the Fenwick Tree, making it available for subsequent queries (for <code>j+1</code>, etc.).</li>
</ul>
</li>
<li><p><strong>Counting Right Elements (<code>right_counts</code>)</strong>:</p>
<ul>
<li>Another Fenwick Tree <code>bit_right</code> is initialized.</li>
<li>The code iterates through <code>j</code> from <code>n-1</code> down to <code>0</code>.</li>
<li>For each <code>j</code>, <code>right_counts[j] = bit_right.query(n - 1) - bit_right.query(new_arr[j])</code>. This calculates how many <code>k &gt; j</code> exist such that <code>new_arr[k] &gt; new_arr[j]</code>. This is done by querying the total count of elements seen so far (<code>bit_right.query(n - 1)</code>) and subtracting the count of elements less than or equal to <code>new_arr[j]</code> (<code>bit_right.query(new_arr[j])</code>).</li>
<li>After computing <code>right_counts[j]</code>, <code>bit_right.update(new_arr[j], 1)</code> adds <code>new_arr[j]</code> to <code>bit_right</code>, making it available for previous <code>j</code> queries.</li>
</ul>
</li>
<li><p><strong>Final Summation</strong>:</p>
<ul>
<li>The total number of good triplets is <code>sum(left_counts[j] * right_counts[j])</code> for all <code>j</code>. Each <code>left_counts[j]</code> gives the count of valid <code>i</code>s for a fixed <code>j</code>, and <code>right_counts[j]</code> gives the count of valid <code>k</code>s. Multiplying them provides the total number of triplets with <code>j</code> as the middle element.</li>
</ul>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Fenwick Tree for Range Queries</strong>: The choice of Fenwick Tree is critical for its <code>O(log N)</code> efficiency in getting prefix sums and performing point updates. This allows the sweep-line approach to run fast.</li>
<li><strong>Transformation (<code>new_arr</code>)</strong>: By mapping <code>nums1[idx]</code> to its position in <code>nums2</code> (<code>pos2[nums1[idx]]</code>), the problem simplifies. We effectively reduce the two increasing sequence conditions (<code>nums1[i] &lt; nums1[j] &lt; nums1[k]</code> and <code>nums2[i] &lt; nums2[j] &lt; nums2[k]</code>) to counting <code>i &lt; j &lt; k</code> where <code>new_arr[i] &lt; new_arr[j] &lt; new_arr[k]</code>. The values in <code>nums1</code> are implicitly handled by the iteration over <code>j</code> (which defines the index <code>j</code>).</li>
<li><strong>Two-Pass Sweep</strong>: The use of a forward sweep for <code>left_counts</code> and a backward sweep for <code>right_counts</code> is a standard pattern for problems involving "left-of" and "right-of" conditions. Each sweep leverages a separate Fenwick Tree to maintain counts of elements encountered so far.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<p>Let <code>N</code> be the length of <code>nums1</code> (and <code>nums2</code>).</p>
<ul>
<li><strong>Time Complexity</strong>: <code>O(N log N)</code><ul>
<li>Building <code>pos1</code> and <code>pos2</code>: <code>O(N)</code>.</li>
<li>Building <code>new_arr</code>: <code>O(N)</code>.</li>
<li>First pass (<code>left_counts</code>): <code>N</code> Fenwick Tree <code>query</code> operations and <code>N</code> <code>update</code> operations. Each operation takes <code>O(log N)</code>. Total: <code>O(N log N)</code>.</li>
<li>Second pass (<code>right_counts</code>): <code>N</code> Fenwick Tree <code>query</code> operations and <code>N</code> <code>update</code> operations. Total: <code>O(N log N)</code>.</li>
<li>Final summation: <code>O(N)</code>.</li>
<li>Overall: <code>O(N log N)</code>.</li>
</ul>
</li>
<li><strong>Space Complexity</strong>: <code>O(N)</code><ul>
<li><code>pos1</code>, <code>pos2</code>, <code>new_arr</code>, <code>left_counts</code>, <code>right_counts</code>: Each requires <code>O(N)</code> space.</li>
<li><code>FenwickTree</code> instances (<code>self.tree</code>): Each requires <code>O(N)</code> space.</li>
<li>Overall: <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Small <code>N</code> (N &lt; 3)</strong>: If <code>N</code> is 0, 1, or 2, it's impossible to form a triplet <code>(i, j, k)</code> with <code>i &lt; j &lt; k</code>. The code correctly handles this: <code>left_counts</code> and <code>right_counts</code> arrays will contain zeros or values that result in a final <code>total_triplets</code> of <code>0</code>. For instance, if <code>N=2</code>, the loops for <code>left_counts</code> and <code>right_counts</code> will run, but <code>left_counts[j]</code> and <code>right_counts[j]</code> will never both be non-zero for any <code>j</code>.</li>
<li><strong><code>FenwickTree.query(index &lt; 0)</code></strong>: The <code>query</code> method explicitly checks <code>if index &lt; 0: return 0</code>, which is correct. This prevents errors when <code>new_arr[j]</code> is 0 and <code>query(new_arr[j] - 1)</code> is called as <code>query(-1)</code>.</li>
<li><strong><code>FenwickTree</code> Size</strong>: The Fenwick Tree is initialized with <code>size = n</code>, allowing it to handle indices <code>0</code> to <code>n-1</code> (which are the values in <code>new_arr</code>). This is correct.</li>
<li><strong>Correctness of <code>right_counts</code> query</strong>: <code>bit_right.query(n - 1) - bit_right.query(new_arr[j])</code> correctly calculates the count of elements <code>x</code> such that <code>new_arr[j] &lt; x &lt;= n-1</code>. This precisely counts elements <code>new_arr[k]</code> where <code>new_arr[k] &gt; new_arr[j]</code>.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability</strong>:<ul>
<li>Add comments explaining the purpose of <code>pos1</code>, <code>pos2</code>, and <code>new_arr</code>, as this is the most critical transformation in the algorithm.</li>
<li>The <code>FenwickTree</code> class is well-defined, but for educational purposes, a short docstring explaining its purpose would be beneficial.</li>
</ul>
</li>
<li><strong>Consolidate BIT logic</strong>: If Fenwick Tree is only used here, it could potentially be implemented inline or as a helper function to reduce class overhead, though the current class structure is clear for reusability.</li>
<li><strong>Alternative Data Structures</strong>:<ul>
<li><strong>Segment Tree</strong>: A Segment Tree could also solve this problem with similar <code>O(N log N)</code> time and <code>O(N)</code> space complexities. It offers more flexibility for complex range queries (e.g., sum, min, max), but for simple prefix sums and point updates, a Fenwick Tree is generally simpler to implement.</li>
<li><strong>Merge Sort based approach</strong>: Problems like counting inversions or "smaller elements to the right" can be solved using a modified merge sort. This approach could be adapted for good triplets, also yielding <code>O(N log N)</code> time. It might be conceptually more involved for three elements than the current two-pass BIT method.</li>
</ul>
</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Performance</strong>: The <code>O(N log N)</code> time complexity is optimal for this class of counting problems. For typical constraints (<code>N</code> up to <code>10^5</code>), this performs well. Python's overhead might make it slower than a C++ implementation for very large <code>N</code>, but it is generally acceptable.</li>
<li><strong>Security</strong>: There are no specific security vulnerabilities in this code. It performs standard numerical operations and array accesses within defined bounds.</li>
</ul>


### Code:
```python
from typing import List

class FenwickTree:
    def __init__(self, size):
        self.tree = [0] * (size + 1)
        self.size = size

    def update(self, index, delta):
        index += 1
        while index <= self.size:
            self.tree[index] += delta
            index += index & (-index)

    def query(self, index):
        if index < 0: return 0
        index += 1
        s = 0
        while index > 0:
            s += self.tree[index]
            index -= index & (-index)
        return s

class Solution:
    def goodTriplets(self, nums1: List[int], nums2: List[int]) -> int:
        n = len(nums1)

        pos1 = [0] * n
        for i in range(n):
            pos1[nums1[i]] = i
        
        pos2 = [0] * n
        for i in range(n):
            pos2[nums2[i]] = i

        new_arr = [0] * n
        for i in range(n):
            new_arr[i] = pos2[nums1[i]]

        left_counts = [0] * n
        right_counts = [0] * n

        bit_left = FenwickTree(n)
        for j in range(n):
            left_counts[j] = bit_left.query(new_arr[j] - 1)
            bit_left.update(new_arr[j], 1)

        bit_right = FenwickTree(n)
        for j in range(n - 1, -1, -1):
            right_counts[j] = bit_right.query(n - 1) - bit_right.query(new_arr[j])
            bit_right.update(new_arr[j], 1)

        total_triplets = 0
        for j in range(n):
            total_triplets += left_counts[j] * right_counts[j]
        
        return total_triplets
```
