## Reorder List
**Language:** python
**Tags:** python,linked list,two-pointers,linked-list-reversal

### Description:
<p>This code reorders a singly linked list in-place such that elements are interleaved from the beginning and end of the original list.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This function reorders a singly linked list <code>L0 -&gt; L1 -&gt; L2 -&gt; ... -&gt; Ln-1 -&gt; Ln</code> into the pattern <code>L0 -&gt; Ln -&gt; L1 -&gt; Ln-1 -&gt; L2 -&gt; Ln-2 -&gt; ...</code>. The operation is performed in-place, modifying the existing list nodes' <code>next</code> pointers without creating new nodes.</p>
<h3>2. How It Works</h3>
<p>The reordering process consists of three main stages:</p>
<ul>
<li><strong>Find Middle:</strong> It uses the "slow and fast pointer" technique to locate the middle of the linked list. The <code>slow</code> pointer will stop at the end of the first half (or the node just before the start of the second half for odd-length lists).</li>
<li><strong>Split and Reverse Second Half:</strong><ul>
<li>The list is split into two independent halves by setting <code>slow.next = None</code>.</li>
<li>The second half of the list is then iteratively reversed.</li>
</ul>
</li>
<li><strong>Merge Halves Alternately:</strong><ul>
<li>The first half and the now-reversed second half are merged by alternately picking nodes. The <code>L0 -&gt; Ln -&gt; L1 -&gt; Ln-1 -&gt; ...</code> pattern is achieved by linking <code>L0</code> to <code>Ln</code>, <code>Ln</code> to <code>L1</code>, <code>L1</code> to <code>Ln-1</code>, and so on. This continues until the second (reversed) half is exhausted.</li>
</ul>
</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>In-Place Modification:</strong> The primary design choice is to reorder the list without creating new nodes, achieving O(1) auxiliary space complexity. This requires careful manipulation of <code>next</code> pointers.</li>
<li><strong>Slow/Fast Pointers:</strong> This is an efficient way to find the middle of a linked list in a single pass (O(N) time) without knowing the list's length beforehand.</li>
<li><strong>Iterative Linked List Reversal:</strong> A standard, efficient, and in-place algorithm is used to reverse the second half of the list.</li>
<li><strong>Alternating Merge Logic:</strong> The core reordering logic involves taking one node from the first half, then one from the reversed second half, and linking them, then repeating with the next nodes. This is done with temporary pointers (<code>temp1</code>, <code>temp2</code>) to preserve the remaining list segments.</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity: O(N)</strong><ul>
<li>Finding the middle: O(N) for traversing up to N/2 nodes.</li>
<li>Reversing the second half: O(N) for traversing up to N/2 nodes.</li>
<li>Merging the two halves: O(N) for traversing up to N/2 pairs of nodes.</li>
<li>Overall, the total time complexity is dominated by these linear traversals.</li>
</ul>
</li>
<li><strong>Space Complexity: O(1)</strong><ul>
<li>Only a constant number of pointers (<code>slow</code>, <code>fast</code>, <code>head1</code>, <code>head2</code>, <code>prev</code>, <code>curr</code>, <code>next_node</code>, <code>p1</code>, <code>p2</code>, <code>temp1</code>, <code>temp2</code>) are used, regardless of the list's size.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong>Empty List (<code>head</code> is <code>None</code>):</strong> Handled by the initial check <code>if not head</code>. The function returns immediately.</li>
<li><strong>Single Node List (<code>head.next</code> is <code>None</code>):</strong> Handled by the initial check <code>if not head.next</code>. The function returns immediately, as a single node list is already "reordered".</li>
<li><strong>Two Node List (e.g., <code>1 -&gt; 2</code>):</strong><ul>
<li>Slow/fast pointers: <code>slow</code> stops at <code>1</code>.</li>
<li>Split: <code>head1 = 1</code>, <code>head2 = 2</code>. <code>1.next = None</code>.</li>
<li>Reverse <code>head2</code>: <code>2</code> remains <code>2</code> (reversed). <code>head2_reversed = 2</code>.</li>
<li>Merge: <code>p1=1</code>, <code>p2=2</code>. Links <code>1 -&gt; 2</code>. The result <code>1 -&gt; 2</code> is correct for this case.</li>
</ul>
</li>
<li><strong>Odd Length List (e.g., <code>1 -&gt; 2 -&gt; 3 -&gt; 4 -&gt; 5</code>):</strong><ul>
<li><code>slow</code> ends at <code>3</code>.</li>
<li><code>head1 = 1 -&gt; 2 -&gt; 3</code>, <code>head2 = 4 -&gt; 5</code>. <code>3.next = None</code>.</li>
<li><code>head2_reversed = 5 -&gt; 4</code>.</li>
<li>Merge results in <code>1 -&gt; 5 -&gt; 2 -&gt; 4 -&gt; 3</code>. The middle node of the first half (3) correctly becomes the last node of the reordered list. The <code>while p2</code> loop ensures the merging stops correctly when the shorter reversed second half is exhausted.</li>
</ul>
</li>
<li><strong>Even Length List (e.g., <code>1 -&gt; 2 -&gt; 3 -&gt; 4</code>):</strong><ul>
<li><code>slow</code> ends at <code>2</code>.</li>
<li><code>head1 = 1 -&gt; 2</code>, <code>head2 = 3 -&gt; 4</code>. <code>2.next = None</code>.</li>
<li><code>head2_reversed = 4 -&gt; 3</code>.</li>
<li>Merge results in <code>1 -&gt; 4 -&gt; 2 -&gt; 3</code>. All nodes are correctly interleaved.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability:</strong> The code is well-structured and uses standard algorithms (slow/fast pointers, iterative reversal) which are generally well-understood. Variable names are mostly descriptive. <code>temp1</code> and <code>temp2</code> are common in linked list manipulations, but could be slightly more explicit if desired (e.g., <code>next_p1</code>, <code>next_p2</code>).</li>
<li><strong>Performance:</strong> The current approach is optimal in terms of both time (O(N)) and space (O(1)) complexity. No significant algorithmic improvements are possible under the constraint of in-place modification.</li>
<li><strong>Alternative Approach (using a stack):</strong><ul>
<li>One could find the middle, then push all nodes from the second half onto a stack.</li>
<li>Then, pop from the stack and interleave with nodes from the first half.</li>
<li>This would achieve the same O(N) time complexity but would require O(N) space for the stack, making it less space-efficient than the current solution.</li>
</ul>
</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This algorithm operates purely on internal data structures and does not involve external input processing or sensitive data, so no direct security concerns are present.</li>
<li><strong>Performance:</strong> The code is highly performant given the constraints. It avoids recursion (which could lead to stack overflow for very long lists) and allocates minimal memory. The constant factor overheads are also small, making it practical for typical linked list sizes.</li>
</ul>


### Code:
```python
class Solution:
    def reorderList(self, head: Optional[ListNode]) -> None:
        if not head or not head.next:
            return

        slow = head
        fast = head
        while fast.next and fast.next.next:
            slow = slow.next
            fast = fast.next.next

        head1 = head
        head2 = slow.next
        slow.next = None

        prev = None
        curr = head2
        while curr:
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node
        head2_reversed = prev

        p1 = head1
        p2 = head2_reversed
        while p2:
            temp1 = p1.next
            temp2 = p2.next

            p1.next = p2
            p2.next = temp1

            p1 = temp1
            p2 = temp2
```
