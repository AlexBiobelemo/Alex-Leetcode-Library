## Contains Duplicate III
**Language:** python
**Tags:** python,oop,sliding window,hashing,hashmap

### Description:
<p>This code implements a solution for the "Contains Duplicate III" problem, often found on platforms like LeetCode. The problem asks to determine if there are two distinct indices <code>i</code> and <code>j</code> in an array <code>nums</code> such that <code>abs(nums[i] - nums[j]) &lt;= valueDiff</code> and <code>abs(i - j) &lt;= indexDiff</code>.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>The code aims to efficiently check for "almost duplicate" numbers within a specified sliding window.</p>
<ul>
<li><strong>"Almost duplicate"</strong>: Two numbers are considered almost duplicates if their absolute difference is at most <code>valueDiff</code>.</li>
<li><strong>"Nearby"</strong>: The indices of these two numbers must be within <code>indexDiff</code> distance of each other (inclusive).</li>
<li>The function returns <code>True</code> if such a pair exists, and <code>False</code> otherwise.</li>
</ul>
<hr>
<h3>2. How It Works</h3>
<p>The algorithm uses a "bucketing" strategy combined with a sliding window:</p>
<ol>
<li><p><strong>Bucket Sizing (<code>w</code>)</strong>: A crucial constant <code>w = valueDiff + 1</code> is calculated. This <code>w</code> defines the size of each "bucket." Numbers <code>num</code> are mapped to a <code>bucket_id = num // w</code>.</p>
<ul>
<li>The key property is: if two numbers <code>a</code> and <code>b</code> fall into the <em>same</em> bucket (i.e., <code>a // w == b // w</code>), then it's guaranteed that <code>abs(a - b) &lt;= valueDiff</code>. This is because the maximum difference within a bucket of size <code>w</code> is <code>w - 1</code>, which equals <code>valueDiff</code>.</li>
</ul>
</li>
<li><p><strong>Iterating with a Sliding Window</strong>: The code iterates through the <code>nums</code> array using index <code>i</code> and value <code>num</code>.</p>
</li>
<li><p><strong>Checking for Duplicates</strong>: For each <code>num</code>, it performs three checks:</p>
<ul>
<li><strong>Same Bucket</strong>: It calculates <code>bucket_id</code>. If <code>bucket_id</code> already exists in the <code>buckets</code> dictionary, it immediately returns <code>True</code> (as <code>nums[bucket_id]</code> is already in the window and guaranteed to satisfy <code>valueDiff</code>).</li>
<li><strong>Adjacent Buckets</strong>: It checks <code>bucket_id - 1</code> and <code>bucket_id + 1</code>. If an adjacent bucket exists, it checks if <code>abs(num - buckets[adjacent_bucket_id]) &lt;= valueDiff</code>. If true, it returns <code>True</code>. This is necessary because numbers can be in different (but adjacent) buckets and still satisfy <code>valueDiff</code> (e.g., <code>num=0, valueDiff=1, w=2</code>. <code>num</code> is in bucket 0. <code>other=1</code> is also in bucket 0. <code>other=2</code> is in bucket 1, <code>abs(0-2)=2 &gt; valueDiff</code>. <code>other=-1</code> is in bucket -1, <code>abs(0-(-1))=1 &lt;= valueDiff</code>).</li>
</ul>
</li>
<li><p><strong>Updating Buckets</strong>:</p>
<ul>
<li>The current <code>num</code> is added to the <code>buckets</code> dictionary, mapping <code>bucket_id</code> to <code>num</code>. This effectively stores the latest number encountered for that bucket ID within the window.</li>
</ul>
</li>
<li><p><strong>Maintaining Window (<code>indexDiff</code>)</strong>:</p>
<ul>
<li>After processing <code>num</code>, if the current index <code>i</code> is greater than or equal to <code>indexDiff</code>, it means the element at <code>nums[i - indexDiff]</code> is now outside the <code>indexDiff</code> window and must be removed from consideration.</li>
<li>The <code>old_num = nums[i - indexDiff]</code> and its <code>old_bucket_id = old_num // w</code> are determined.</li>
<li><code>del buckets[old_bucket_id]</code> removes this entry from the <code>buckets</code> dictionary.</li>
</ul>
</li>
<li><p><strong>No Duplicates</strong>: If the loop completes without returning <code>True</code>, it means no such pair was found, and the function returns <code>False</code>.</p>
</li>
</ol>
<hr>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Bucketing Strategy</strong>: The core idea is to transform the <code>valueDiff</code> condition into a bucket problem. By choosing <code>w = valueDiff + 1</code>, any two numbers in the same bucket inherently satisfy the <code>valueDiff</code> constraint. This greatly reduces the number of comparisons needed.</li>
<li><strong>Hash Map for Buckets (<code>dict</code>)</strong>: A hash map (<code>buckets</code> dictionary in Python) is used to store <code>bucket_id -&gt; number</code>. This allows for O(1) average time complexity for insertion, deletion, and lookup of bucket IDs.</li>
<li><strong>Sliding Window</strong>: The <code>indexDiff</code> constraint is handled by explicitly removing elements from the <code>buckets</code> dictionary once their index falls out of the <code>indexDiff</code> range. This keeps the <code>buckets</code> dictionary small and relevant.</li>
<li><strong>Checking Adjacent Buckets</strong>: This is crucial. While numbers in the <em>same</em> bucket are guaranteed to satisfy <code>valueDiff</code>, numbers in <em>adjacent</em> buckets might also satisfy it (e.g., <code>valueDiff=10</code>, <code>w=11</code>. <code>num=0</code> is in bucket 0. <code>other=10</code> is in bucket 0. <code>other=11</code> is in bucket 1. <code>abs(0-11)=11 &gt; valueDiff</code>. But <code>num=10</code> in bucket 0, <code>other=11</code> in bucket 1. <code>abs(10-11)=1 &lt;= valueDiff</code> - this example shows need for <code>valueDiff</code> check. More typical: <code>num=0</code> in bucket 0, <code>valueDiff=2</code>, <code>w=3</code>. <code>other=2</code> in bucket 0, <code>abs(0-2)&lt;=2</code>. <code>other=3</code> in bucket 1, <code>abs(0-3) &gt; 2</code>. Wait, my <code>w</code> example was off. <code>w = valueDiff + 1</code>. So, if <code>valueDiff=2</code>, <code>w=3</code>. Bucket 0 contains <code>[0,1,2]</code>, bucket 1 contains <code>[3,4,5]</code>. <code>num=2</code> (bucket 0), <code>other=3</code> (bucket 1). <code>abs(2-3) = 1 &lt;= valueDiff</code>. So yes, checking adjacent buckets is essential.</li>
<li><strong>Trade-off</strong>: The bucketing approach sacrifices some precision (it stores only <em>one</em> number per bucket ID at a time) for speed. It relies on the specific properties of <code>w</code> and <code>valueDiff</code> to guarantee correctness.</li>
</ul>
<hr>
<h3>4. Complexity</h3>
<ul>
<li><p><strong>Time Complexity</strong>: O(N) on average.</p>
<ul>
<li>The loop runs <code>N</code> times (where <code>N</code> is the length of <code>nums</code>).</li>
<li>Inside the loop, operations like integer division, dictionary lookups, insertions, and deletions take O(1) time on average for hash maps.</li>
<li>In the worst-case scenario for hash maps (e.g., extreme hash collisions leading to a linked list behavior), operations could degrade to O(K) where K is the number of elements in the map, potentially leading to O(N * indexDiff) or even O(N^2) overall. However, Python's dict implementation is highly optimized to avoid such worst-cases for common inputs.</li>
</ul>
</li>
<li><p><strong>Space Complexity</strong>: O(indexDiff) in the worst case.</p>
<ul>
<li>The <code>buckets</code> dictionary stores at most <code>indexDiff + 1</code> elements, as elements outside the sliding window are removed.</li>
</ul>
</li>
</ul>
<hr>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>indexDiff = 0</code></strong>: The window size is effectively 1. <code>i &gt;= 0</code> always true, so <code>del</code> occurs immediately. <code>buckets</code> will only ever contain one element (the current <code>num</code>). The only way to return <code>True</code> is if <code>bucket_id in buckets</code> (meaning <code>num</code> is a duplicate of itself, which isn't distinct indices) or <code>abs(num - buckets[bucket_id - 1]) &lt;= valueDiff</code> (not possible as no previous elements). For distinct indices <code>i != j</code>, this should always be <code>False</code>. The code would indeed return <code>False</code> as intended.</li>
<li><strong><code>valueDiff = 0</code></strong>: <code>w</code> becomes 1. <code>bucket_id</code> becomes <code>num</code>. The check <code>if bucket_id in buckets</code> becomes <code>if num in buckets</code>, effectively checking for exact duplicates. This correctly handles <code>valueDiff = 0</code>.</li>
<li><strong>Negative numbers</strong>: Python's <code>//</code> (floor division) handles negative numbers consistently for bucketing (e.g., <code>-1 // 3</code> is -1, <code>1 // 3</code> is 0). The logic remains correct.</li>
<li><strong>Empty <code>nums</code></strong>: The loop won't execute, and <code>False</code> is returned, which is correct.</li>
<li><strong>Large <code>indexDiff</code> (&gt;= N)</strong>: The <code>del</code> condition <code>i &gt;= indexDiff</code> might not be met for many elements. <code>buckets</code> will grow up to <code>N</code> elements, still fitting the O(indexDiff) (or O(N)) space complexity. The logic holds.</li>
<li><strong>Correctness of <code>del</code></strong>: The <code>del buckets[old_bucket_id]</code> logic for the sliding window implicitly assumes that if <code>old_bucket_id</code> exists in <code>buckets</code>, it corresponds to <code>old_num</code>. If another number <code>Y</code> (still in the window) had also fallen into <code>old_bucket_id</code> <em>after</em> <code>old_num</code>, <code>Y</code> would have overwritten <code>old_num</code>'s entry in <code>buckets</code>. Then, deleting <code>old_bucket_id</code> would incorrectly remove <code>Y</code> from consideration. However, for this specific problem, this bucket-based solution pattern is commonly accepted, suggesting that either the problem constraints mitigate this specific collision scenario, or the "early exit" conditions (especially <code>if bucket_id in buckets: return True</code>) are robust enough to cover these cases before such an incorrect deletion would lead to a false negative.</li>
</ul>
<hr>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Robust <code>del</code> operation</strong>: To prevent a <code>KeyError</code> if <code>old_bucket_id</code> was already removed or overwritten by another element in the window with the same bucket ID, <code>buckets.pop(old_bucket_id, None)</code> could be used instead of <code>del buckets[old_bucket_id]</code>. This is a minor robustness improvement rather than a functional change.</li>
<li><strong>Clarity of <code>w</code></strong>: Adding a comment explaining why <code>w = valueDiff + 1</code> is chosen (guaranteeing <code>abs(a-b) &lt;= valueDiff</code> for <code>a,b</code> in same bucket) would enhance readability for an educator or future maintainer.</li>
<li><strong>Alternative Data Structures (for general approach)</strong>:<ul>
<li><strong>Sorted List/TreeSet</strong>: A common alternative for this problem (without explicit bucketing) is to maintain a <code>SortedList</code> (like Java's <code>TreeSet</code> or <code>SortedList</code> from Python's <code>sortedcontainers</code> library) of numbers within the <code>indexDiff</code> window. For each <code>num</code>, one would perform a range query (e.g., find elements between <code>num - valueDiff</code> and <code>num + valueDiff</code>) in the <code>SortedList</code>. This approach typically has a time complexity of O(N log(indexDiff)).</li>
<li><strong>Balanced Binary Search Tree</strong>: Similar to a SortedList, a custom BST could be used.</li>
</ul>
</li>
<li>The current bucket-based solution is generally more performant (O(N) average) than the sorted list approach (O(N log(indexDiff))) when <code>indexDiff</code> is large.</li>
</ul>
<hr>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Hash Collisions</strong>: The performance of dictionary operations (insertion, lookup, deletion) relies on good hash function distribution. In a theoretical worst-case scenario with extreme hash collisions, operations could degrade from average O(1) to O(K) (where K is the number of elements in the dictionary), potentially turning the overall time complexity into O(N * indexDiff) or even O(N^2). However, Python's <code>dict</code> uses robust hashing and collision resolution strategies (e.g., randomized hashing for strings in recent Python versions) that make this highly unlikely in practice for typical integer inputs.</li>
<li><strong>Integer Overflow</strong>: Python's integers automatically handle arbitrary precision, so there are no concerns about integer overflow for <code>num</code> or <code>bucket_id</code> calculations, regardless of their magnitude.</li>
<li><strong>No Direct Security Implications</strong>: This algorithm is purely mathematical and computational; it doesn't process external untrusted input in a way that would directly introduce typical security vulnerabilities (e.g., injection, access control issues).</li>
</ul>


### Code:
```python
class Solution:
    def containsNearbyAlmostDuplicate(self, nums: List[int], indexDiff: int, valueDiff: int) -> bool:
        w = valueDiff + 1
        
        buckets = {}
        
        for i, num in enumerate(nums):
            bucket_id = num // w
            
            if bucket_id in buckets:
                return True
            
            if (bucket_id - 1) in buckets and abs(num - buckets[bucket_id - 1]) <= valueDiff:
                return True
            
            if (bucket_id + 1) in buckets and abs(num - buckets[bucket_id + 1]) <= valueDiff:
                return True
            
            buckets[bucket_id] = num
            
            if i >= indexDiff:
                old_num = nums[i - indexDiff]
                old_bucket_id = old_num // w
                del buckets[old_bucket_id]
                
        return False
```
