## Smallest String Starting From Leaf
**Language:** python
**Tags:** python,dfs,binary tree,recursion

### Description:
This code finds the lexicographically smallest string that can be formed by traversing a binary tree from any leaf node up to the root. Node values are integers `0-25`, mapping to characters `'a'-'z'`.

---

### 1. Overview & Intent

The primary goal of this code is to identify the "smallest" possible string achievable by tracing a path from any leaf node upwards to the root of a binary tree. The "smallest" here refers to lexicographical order (like dictionary order). Each node's integer value is mapped to a lowercase letter (`0 -> 'a'`, `1 -> 'b'`, ..., `25 -> 'z'`).

---

### 2. How It Works

The solution employs a Depth-First Search (DFS) algorithm to explore all possible paths from the root down to every leaf node.

*   **Initialization**: A global-like variable `smallest_string` is initialized to `"~"`. This character is lexicographically larger than any lowercase letter, ensuring that the first valid path string encountered will always be smaller and replace this initial value.
*   **Recursive DFS**: A nested helper function `dfs(node, current_path_string)` is used:
    *   It takes the current `node` and a string `current_path_string` representing the path accumulated so far (from the root down to the parent of the current node).
    *   **Path Building**: For each `node`, it converts `node.val` into its corresponding character (`'a'` to `'z'`). This character is then *prepended* to `current_path_string` to form `new_path_string`. This prepending is crucial because we want the leaf-to-root order; by adding characters at the front as we go deeper, the string effectively builds up in reverse order of traversal, which matches the leaf-to-root requirement.
    *   **Leaf Check**: If `node` is a leaf (has no left or right children), `new_path_string` represents a complete path from that leaf to the root. This string is then compared with `smallest_string`. If `new_path_string` is lexicographically smaller, `smallest_string` is updated.
    *   **Recurse**: If `node` is not a leaf, the `dfs` function is called recursively for its left and right children, passing the `new_path_string`.
*   **Start**: The DFS is initiated from the `root` with an empty string `""`.
*   **Return**: After all paths are explored, the final `smallest_string` found is returned.

---

### 3. Key Design Decisions

*   **Depth-First Search (DFS)**: This is a natural choice for exploring all paths from root to leaf in a tree. Recursion provides a clean way to implement this traversal.
*   **String Prepending (`current_char + current_path_string`)**: This is the most critical design choice. Instead of appending characters, prepending ensures that the string is built in the desired leaf-to-root order. For example, if the path is `A -> B -> C` (root to leaf), and we want `CBA`, prepending `C` to `B`, then `B` to `A` achieves this.
*   **`nonlocal` Keyword**: Used to allow the inner `dfs` function to modify the `smallest_string` variable defined in the `smallestFromLeaf` outer scope.
*   **Initialization with `"~"`**: A clever trick to set an initial `smallest_string` value that is guaranteed to be lexicographically larger than any valid path string composed of lowercase letters. This avoids needing `None` checks for the first valid path.

---

### 4. Complexity

Let `N` be the number of nodes in the tree and `L` be the maximum depth (height) of the tree.

*   **Time Complexity**: `O(N * L)`
    *   Each node in the tree is visited exactly once.
    *   At each node, a new string is created using string concatenation: `new_path_string = current_char + current_path_string`. In Python, string concatenation creates a new string object, which takes time proportional to the sum of the lengths of the strings being concatenated.
    *   The maximum length of `current_path_string` can be `L` (for a path to a leaf node).
    *   Therefore, each string operation inside the DFS can take up to `O(L)` time.
    *   Since there are `N` nodes, the total time complexity is `O(N * L)`. In the worst case (a skewed tree where `L` approaches `N`), this can become `O(N^2)`.
*   **Space Complexity**: `O(L^2)`
    *   **Recursion Stack**: The maximum depth of the recursion stack is `O(L)`.
    *   **String Storage**: At each level of recursion, a new `new_path_string` is created and stored on the stack frame. The length of these strings grows from `1` up to `L`.
    *   In the worst case (a skewed tree), there are `L` stack frames, each holding a string of length up to `L`. This cumulative storage leads to `O(1 + 2 + ... + L) = O(L^2)` space complexity.

---

### 5. Edge Cases & Correctness

*   **Empty Tree (`root` is `None`)**:
    *   The initial call `dfs(root, "")` will immediately return because `node` is `None`. `smallest_string` will remain `"~"`. If the problem expects an empty string or specific error for an empty tree, this behavior would need adjustment. However, generally, tree problems assume at least one node.
*   **Single Node Tree**:
    *   The root is also a leaf. `dfs(root, "")` will correctly form `chr(root.val + ord('a'))`, detect it's a leaf, and update `smallest_string`.
*   **Skewed Tree (e.g., linked list)**:
    *   The DFS will correctly traverse all nodes, though with the worst-case `O(N^2)` time and `O(N^2)` space complexity.
*   **All nodes have the same value**:
    *   The algorithm correctly handles this, performing lexicographical comparisons as usual.
*   **Multiple paths result in the same smallest string**:
    *   Due to the `if new_path_string < smallest_string:` check, the algorithm will pick the first one encountered that is truly the smallest. If multiple paths tie for the smallest, any of them is a valid result.

---

### 6. Improvements & Alternatives

*   **Performance (String Concatenation)**: The `O(N*L)` time and `O(L^2)` space are the main bottlenecks. This can be significantly improved by using a `list` of characters to build the path and converting it to a string only when a leaf node is reached.
    *   **Idea**: Pass a `list` of characters (representing the path from root to current node) to the DFS. `append` the current node's character to the list. When at a leaf, `"".join(reversed(path_list))` to form the leaf-to-root string. Remember to `pop()` the character upon returning from a recursive call (backtracking).
    *   **Impact**:
        *   **Time Complexity**: Reduces to `O(N)` (list appends are amortized `O(1)`, `join` and `reversed` are `O(L)` once per leaf, `N` nodes visited, at most `N` leaf path conversions).
        *   **Space Complexity**: Reduces to `O(L)` (for the recursion stack and the single list of characters representing the current path).
*   **Iterative DFS/BFS**: While recursion is clean, an iterative approach using an explicit stack (for DFS) or queue (for BFS) could be used. However, the logic for building and comparing strings would remain similar. BFS might seem intuitive for "smallest," but since string length and characters matter, the depth-first approach is more direct here.

---

### 7. Security/Performance Notes

*   **Performance**: As highlighted in the Complexity section, the repeated string concatenation can lead to `O(N^2)` time complexity in worst-case scenarios (tall, skewed trees). For large trees, this could cause significant performance degradation or even exceed time limits in competitive programming environments. The list-based alternative is highly recommended for optimal performance.
*   **Security**: No inherent security vulnerabilities are apparent in this code. It processes tree node values (integers) and converts them to characters, which is a safe operation. It doesn't handle external input directly in a way that could lead to injection or similar issues.

### Code:
```python
class Solution:
    def smallestFromLeaf(self, root: Optional[TreeNode]) -> str:
        smallest_string = "~" # Initialize with a string lexicographically larger than any possible path

        def dfs(node, current_path_string):
            nonlocal smallest_string

            if not node:
                return

            # Prepend the current node's character to the path string
            # node.val is in [0, 25], map it to 'a' through 'z'
            current_char = chr(node.val + ord('a'))
            new_path_string = current_char + current_path_string

            # If it's a leaf node
            if not node.left and not node.right:
                # Compare the current path string with the smallest found so far
                if new_path_string < smallest_string:
                    smallest_string = new_path_string
                return

            # Recurse for left and right children
            dfs(node.left, new_path_string)
            dfs(node.right, new_path_string)

        dfs(root, "") # Start DFS from the root with an empty path string
        return smallest_string
```
