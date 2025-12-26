## Transform to Chessboard
**Language:** python
**Tags:** python,object-oriented programming,matrix,greedy algorithm,bit manipulation

### Description:
<p>This code addresses the "Transform to Chessboard" problem, a common algorithmic challenge. It cleverly reduces a 2D problem into a series of 1D checks and calculations, leveraging properties unique to chessboard patterns.</p>
<hr>
<h3>1. Overview &amp; Intent</h3>
<p>This Python code aims to determine the minimum number of moves (swaps of adjacent rows or columns) required to transform a given <code>n x n</code> binary grid (<code>board</code>) into a valid chessboard pattern. If transformation is impossible, it returns <code>-1</code>.</p>
<p>A valid chessboard pattern implies:</p>
<ul>
<li>Adjacent cells (horizontally or vertically) must have different values.</li>
<li>This leads to alternating rows and alternating columns (e.g., <code>0101...</code>, <code>1010...</code>, <code>0101...</code> or <code>1010...</code>, <code>0101...</code>, <code>1010...</code>).</li>
</ul>
<h3>2. How It Works</h3>
<p>The solution breaks down the problem into three main logical steps:</p>
<ol>
<li><p><strong>Structural Validity Check (XOR Invariant):</strong></p>
<ul>
<li>It first iterates through the entire board to check a fundamental property: for any <code>(i, j)</code>, the relationship <code>board[i][j] ^ board[i][0] ^ board[0][j] ^ board[0][0]</code> must always be <code>0</code>.</li>
<li>This invariant ensures that the entire grid's pattern is consistent with a chessboard, where each cell's value is determined by its row's first element and column's first element, relative to <code>board[0][0]</code>. If this check fails for any cell, the board cannot be transformed, and <code>-1</code> is returned.</li>
</ul>
</li>
<li><p><strong>Calculate Row Swaps:</strong></p>
<ul>
<li>It extracts the first element of each row (<code>board[i][0]</code>) into a 1D array (<code>row_first_elements</code>).</li>
<li>It then calls a helper function <code>calc_swaps</code> on this 1D array. This function calculates the minimum swaps needed to make this 1D array an alternating pattern (either <code>0101...</code> or <code>1010...</code>).</li>
</ul>
</li>
<li><p><strong>Calculate Column Swaps:</strong></p>
<ul>
<li>Similarly, it extracts the first element of each column (<code>board[0][j]</code>) into a 1D array (<code>col_first_elements</code>).</li>
<li>It calls <code>calc_swaps</code> again for this array.</li>
</ul>
</li>
<li><p><strong>Sum and Return:</strong></p>
<ul>
<li>If either <code>row_moves</code> or <code>col_moves</code> is <code>float('inf')</code> (indicating an impossible transformation for that dimension), the function returns <code>-1</code>.</li>
<li>Otherwise, the total minimum moves are the sum of <code>row_moves</code> and <code>col_moves</code>.</li>
</ul>
</li>
</ol>
<p><strong><code>calc_swaps</code> Helper Function Details:</strong></p>
<ul>
<li>This function takes a 1D array and its <code>length</code>.</li>
<li>It counts the occurrences of <code>0</code>s and <code>1</code>s (<code>count_0</code>, <code>count_1</code>).</li>
<li>It checks if the counts of <code>0</code>s and <code>1</code>s are balanced. For a valid chessboard row/column of length <code>n</code>:<ul>
<li>If <code>n</code> is even, <code>count_0</code> must equal <code>count_1</code>.</li>
<li>If <code>n</code> is odd, <code>count_0</code> and <code>count_1</code> can differ by at most 1 (e.g., <code>(n+1)/2</code> of one, <code>(n-1)/2</code> of the other).</li>
<li>If <code>abs(count_0 - count_1) &gt; 1</code>, it returns <code>float('inf')</code>, as it's impossible to form an alternating pattern.</li>
</ul>
</li>
<li>It calculates <code>mismatch_0</code>: the number of elements that don't match the <code>0101...</code> pattern (i.e., <code>arr[i] != (i % 2)</code>).</li>
<li>It calculates <code>mismatch_1</code>: the number of elements that don't match the <code>1010...</code> pattern (i.e., <code>arr[i] != ((i + 1) % 2)</code>).</li>
<li>For a valid alternating pattern, the number of mismatches must be even, because each swap corrects two misplaced elements. If <code>mismatch_0</code> or <code>mismatch_1</code> is odd, that target pattern is impossible for the given array.</li>
<li>The minimum swaps required for a valid alternating pattern is <code>mismatch // 2</code>. It returns the minimum of the valid options, or <code>float('inf')</code> if both are impossible.</li>
</ul>
<h3>3. Key Design Decisions</h3>
<ul>
<li><strong>Decoupling 2D into 1D:</strong> The most significant design choice is reducing the 2D problem to two 1D problems. If a board is a valid chessboard, all its rows must be one of two alternating patterns, and similarly for its columns. Crucially, all rows (or columns) must either be identical or perfectly inverted versions of each other. This allows independent calculation of row and column moves.</li>
<li><strong>XOR Invariant for Structural Check:</strong> The <code>(board[i][j] ^ board[i][0] ^ board[0][j] ^ board[0][0]) != 0</code> check is elegant and efficient. It verifies if the relative pattern within the board is consistent with a chessboard. It essentially checks if <code>board[i][j]</code> is <code>board[i][0] ^ board[0][j] ^ board[0][0]</code> (after possibly flipping <code>board[i][0]</code> or <code>board[0][j]</code> to ensure the <code>0,0</code> cell matches).</li>
<li><strong><code>calc_swaps</code> Logic for 1D Arrays:</strong><ul>
<li><strong>Balance Check:</strong> <code>abs(count_0 - count_1) &gt; 1</code> is essential for ruling out impossible arrays early.</li>
<li><strong>Even Mismatches:</strong> The requirement <code>mismatch_X % 2 == 0</code> is critical because each swap corrects two positions. If an odd number of elements are "wrong", it's impossible to fix with pairwise swaps.</li>
<li><strong><code>mismatch // 2</code>:</strong> Each swap moves two elements closer to their target positions (or moves one into place and the one it swaps with also into place if it's a perfect swap). Thus, the number of swaps is half the number of elements out of place.</li>
</ul>
</li>
</ul>
<h3>4. Complexity</h3>
<ul>
<li><strong>Time Complexity:</strong><ul>
<li>The initial XOR structural check iterates through all <code>n * n</code> cells: <code>O(N^2)</code>.</li>
<li>Building <code>row_first_elements</code> and <code>col_first_elements</code> takes <code>O(N)</code> each.</li>
<li><code>calc_swaps</code> iterates through a 1D array of length <code>N</code>: <code>O(N)</code>. It's called twice.</li>
<li>Total time complexity: <code>O(N^2)</code>. This is optimal as the entire board must be inspected at least once.</li>
</ul>
</li>
<li><strong>Space Complexity:</strong><ul>
<li><code>row_first_elements</code> and <code>col_first_elements</code> lists each store <code>N</code> integers: <code>O(N)</code>.</li>
<li>Auxiliary variables within <code>calc_swaps</code> are <code>O(1)</code>.</li>
<li>Total space complexity: <code>O(N)</code>.</li>
</ul>
</li>
</ul>
<h3>5. Edge Cases &amp; Correctness</h3>
<ul>
<li><strong><code>n=1</code> (1x1 board):</strong><ul>
<li>The XOR check <code>for i in range(n)...</code> might not iterate for <code>i=0, j=0</code> if it's defined such that <code>i &gt; 0</code> or <code>j &gt; 0</code>. However, the current code correctly iterates for <code>i=0, j=0</code> which doesn't trigger the <code>!= 0</code> condition.</li>
<li><code>row_first_elements</code> will be <code>[board[0][0]]</code>, and <code>col_first_elements</code> will be <code>[board[0][0]]</code>.</li>
<li><code>calc_swaps([X], 1)</code> will correctly return <code>0</code> moves (e.g., <code>[0]</code> matches <code>0%2</code> perfectly, <code>[1]</code> matches <code>(0+1)%2</code> perfectly).</li>
<li>Result: <code>0</code> moves, which is correct for a 1x1 board.</li>
</ul>
</li>
<li><strong><code>n</code> is even vs. <code>n</code> is odd:</strong> The <code>abs(count_0 - count_1) &gt; 1</code> check correctly handles the requirement that counts of 0s and 1s must be perfectly balanced for even <code>n</code>, and differ by exactly 1 for odd <code>n</code>.</li>
<li><strong>Impossible configurations:</strong><ul>
<li>Boards failing the initial XOR invariant check return <code>-1</code> immediately.</li>
<li>Boards where <code>row_first_elements</code> or <code>col_first_elements</code> cannot form an alternating pattern (due to imbalanced 0/1 counts or odd mismatches) result in <code>calc_swaps</code> returning <code>float('inf')</code>, which correctly propagates to a final <code>-1</code>.</li>
</ul>
</li>
</ul>
<h3>6. Improvements &amp; Alternatives</h3>
<ul>
<li><strong>Readability of XOR Check:</strong> The line <code>if (board[i][j] ^ board[i][0] ^ board[0][j] ^ board[0][0]) != 0:</code> is highly concise but might be opaque to someone unfamiliar with the underlying mathematical property. A short comment explaining its purpose would enhance readability for maintainers.<ul>
<li><em>Example Comment:</em> <code># Check if the relative pattern of cells is consistent with a chessboard.</code></li>
</ul>
</li>
<li><strong>Early Exit in <code>calc_swaps</code>:</strong> The <code>abs(count_0 - count_1) &gt; 1</code> check could theoretically be moved to the beginning of <code>calc_swaps</code> before iterating to count mismatches, for a very minor optimization in edge cases, though its current position is perfectly fine.</li>
<li><strong>Alternative for <code>mismatch</code> counting:</strong> Instead of <code>mismatch_0</code> and <code>mismatch_1</code>, one could count how many <code>0</code>s are in <code>1</code>-positions and <code>1</code>s are in <code>0</code>-positions for a target pattern. This is effectively what <code>mismatch_0</code> captures for the <code>0101...</code> pattern.</li>
</ul>
<h3>7. Security/Performance Notes</h3>
<ul>
<li><strong>Security:</strong> This code does not interact with external systems, user input, or sensitive data, so there are no direct security implications.</li>
<li><strong>Performance:</strong> The <code>O(N^2)</code> time complexity is optimal for this problem, as any solution <em>must</em> at least inspect every cell of the <code>N x N</code> board. The <code>O(N)</code> space complexity is also efficient, storing only a few helper arrays proportional to one dimension. No significant performance bottlenecks are apparent.</li>
</ul>


### Code:
```python
class Solution:
    def movesToChessboard(self, board: List[List[int]]) -> int:
        n = len(board)

        # Helper function to calculate minimum swaps for a 1D array
        # to match an alternating pattern (0101... or 1010...)
        def calc_swaps(arr, length):
            mismatch_0 = 0 # count of elements not matching 0101...
            mismatch_1 = 0 # count of elements not matching 1010...
            count_0 = 0
            count_1 = 0

            for i in range(length):
                if arr[i] == 0:
                    count_0 += 1
                else:
                    count_1 += 1

                # Compare with target pattern 0101...
                if arr[i] != (i % 2):
                    mismatch_0 += 1
                # Compare with target pattern 1010...
                if arr[i] != ((i + 1) % 2):
                    mismatch_1 += 1

            # Check if counts of 0s and 1s are balanced
            # For a valid chessboard, the number of 0s and 1s in any row/column
            # can differ by at most 1.
            if abs(count_0 - count_1) > 1:
                return float('inf') # Impossible

            res = float('inf')


            if mismatch_0 % 2 == 0:
                res = min(res, mismatch_0 // 2)
            
            if mismatch_1 % 2 == 0:
                res = min(res, mismatch_1 // 2)
            
            return res

        for i in range(n):
            for j in range(n):
                if (board[i][j] ^ board[i][0] ^ board[0][j] ^ board[0][0]) != 0:
                    return -1

        row_first_elements = [board[i][0] for i in range(n)]
        row_moves = calc_swaps(row_first_elements, n)

        col_first_elements = [board[0][j] for j in range(n)]
        col_moves = calc_swaps(col_first_elements, n)

        if row_moves == float('inf') or col_moves == float('inf'):
            return -1
        
        return row_moves + col_moves
```
