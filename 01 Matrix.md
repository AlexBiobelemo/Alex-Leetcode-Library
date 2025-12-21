## 01 Matrix
**Language:** python
**Tags:** python,oop,bfs,queue

### Description:
### 1. Overview & Intent

This code aims to solve the "01 Matrix" problem. Given a binary matrix `mat` (containing only `0`s and `1`s), the goal is to find the distance of the nearest `0` for each cell. The distance between two adjacent cells is `1`.

The approach used is a **multi-source Breadth-First Search (BFS)**. This is a highly suitable algorithm for finding the shortest path in an unweighted graph (or grid, which can be modeled as a graph) where each step has a uniform cost (in this case, `1`). By starting the BFS simultaneously from all cells containing `0`, the algorithm efficiently propagates the minimum distance to all `1` cells in a layer-by-layer fashion.

### 2. How It Works

The code operates in two main phases: initialization and BFS traversal.

1.  **Initialization:**
    *   It first determines the dimensions `m` (rows) and `n` (columns) of the input matrix `mat`.
    *   A new matrix `dist` of the same dimensions is created. This `dist` matrix will store the calculated shortest distances. Initially, all cells in `dist` are set to `math.inf` (infinity), except for the `0` cells.
    *   A `collections.deque` named `queue` is initialized. This will serve as the queue for the BFS algorithm.
    *   The code then iterates through the entire input `mat`. If a cell `mat[r][c]` contains a `0`:
        *   Its corresponding distance `dist[r][c]` is set to `0` (as its distance to the nearest `0` is itself).
        *   The coordinates `(r, c)` are added to the `queue`. These `0` cells are the starting points for our multi-source BFS.

2.  **BFS Traversal:**
    *   A list `directions` is defined to represent the four cardinal movements: up, down, left, and right.
    *   The main BFS loop begins: `while queue:`
        *   It `popleft()`s a cell `(r, c)` from the queue. This is the current cell being processed.
        *   For each `(dr, dc)` in `directions`:
            *   It calculates the coordinates `(nr, nc)` of the neighboring cell.
            *   **Boundary Check:** It verifies if `(nr, nc)` is within the bounds of the matrix (`0 <= nr < m` and `0 <= nc < n`).
            *   **Visited Check:** It checks if `dist[nr][nc]` is still `math.inf`. This condition is crucial:
                *   If `dist[nr][nc]` is `math.inf`, it means this neighbor has not yet been visited, or a shorter path to it hasn't been found yet. Since BFS explores layer by layer, the first time we reach a cell, it's guaranteed to be via the shortest path.
                *   If `dist[nr][nc]` is *not* `math.inf`, it means we've already found a path to this neighbor (and it must be the shortest or an equally short path), so we don't need to process it again from the current cell.
            *   If the neighbor is valid and unvisited (`dist[nr][nc] == math.inf`):
                *   The distance to this neighbor is updated: `dist[nr][nc] = dist[r][c] + 1`. This means the neighbor is one step further from a `0` than the current cell.
                *   The neighbor `(nr, nc)` is then added to the `queue` to be processed in a subsequent iteration.
    *   Once the `queue` is empty, all reachable cells will have had their shortest distances calculated.
    *   Finally, the `dist` matrix is returned.

### 3. Key Design Decisions

*   **Multi-Source BFS:** This is the core algorithmic choice. Instead of running a separate BFS from each `1` to find the nearest `0` (which would be inefficient), starting BFS from all `0`s simultaneously and expanding outwards ensures that the first time a `1` is reached, it's via its shortest path to *any* `0`.
*   **`collections.deque`:** Using `collections.deque` for the queue is a standard and efficient practice for BFS in Python. It provides O(1) time complexity for `append()` (adding to the right) and `popleft()` (removing from the left), which are critical for BFS performance. A standard Python list's `pop(0)` operation would be O(N), leading to a much slower overall algorithm.
*   **`math.inf` as Unvisited Marker:** Initializing distances to `math.inf` serves a dual purpose: it indicates that a cell has not yet been visited, and it ensures that any calculated finite distance will always be smaller, allowing for correct distance updates. It effectively acts as the "visited" set for the BFS.
*   **In-place Distance Tracking:** The `dist` matrix not only stores the result but also implicitly manages the "visited" state of cells during the BFS. This avoids the need for a separate `visited` set or matrix, simplifying the code.

### 4. Complexity Analysis

*   **Time Complexity: O(m * n)**
    *   **Initialization:** Creating the `dist` matrix and iterating through `mat` to populate the initial `queue` takes O(m * n) time, where `m` is the number of rows and `n` is the number of columns.
    *   **BFS Traversal:** Each cell `(r, c)` in the matrix is added to the queue at most once. When a cell is popped from the queue, its four neighbors are examined. Therefore, each cell and each edge (connection between adjacent cells) is processed a constant number of times.
    *   Combining these, the total time complexity is dominated by the O(m * n) operations.

*   **Space Complexity: O(m * n)**
    *   **`dist` matrix:** This matrix stores the results and requires O(m * n) space.
    *   **`queue`:** In the worst-case scenario (e.g., a checkerboard pattern or a matrix mostly filled with `1`s surrounding a few `0`s), the queue could potentially hold up to O(m * n) cells.
    *   The `directions` list takes constant space.
    *   Therefore, the total space complexity is O(m * n).

### 5. Edge Cases & Correctness

*   **Matrix with only `0`s:** If `mat` contains only `0`s, the `dist` matrix will correctly be all `0`s. The BFS will initialize all `dist[r][c]` to `0` and add all cells to the queue. When processed, their neighbors will already have `0` distance or be out of bounds, so no updates will occur.
*   **Matrix with only `1`s:** This scenario is typically disallowed by problem constraints (e.g., "at least one `0` is guaranteed"). If it were allowed, the `queue` would remain empty, and `dist` would be returned with all `math.inf` values, which is technically correct as no `0` is reachable.
*   **Single-cell matrix:**
    *   `[[0]]`: `dist` will be `[[0]]`. Correct.
    *   `[[1]]`: (Assuming problem constraints allow this and no `0` exists). `dist` will be `[[math.inf]]`. Correct.
*   **Rectangular Matrices:** The code correctly handles matrices where `m != n` due to separate `m` and `n` variables.
*   **Correctness of Visited Check:** The `dist[nr][nc] == math.inf` check is fundamental to BFS's correctness for shortest paths in unweighted graphs. By only processing neighbors whose distances are still `math.inf`, we ensure that the first path found to any cell is the shortest one.

### 6. Improvements & Alternatives

*   **Readability:** The code is already very clear and follows standard Python conventions. Variable names are descriptive. No significant readability improvements are needed.
*   **Robustness (Input Validation):** For a production-grade solution, adding input validation could be beneficial:
    ```python
    if not mat or not mat[0]:
        raise ValueError("Input matrix cannot be empty.")
    ```
*   **Alternatives (Dynamic Programming):**
    This problem can also be solved using dynamic programming with two passes:
    1.  **First Pass (Top-Left to Bottom-Right):** Iterate through the matrix. For each cell `(r, c)`:
        *   If `mat[r][c] == 0`, `dist[r][c] = 0`.
        *   If `mat[r][c] == 1`, `dist[r][c] = min(dist[r-1][c], dist[r][c-1]) + 1` (handling boundary conditions by treating out-of-bounds as `math.inf`).
    2.  **Second Pass (Bottom-Right to Top-Left):** Iterate through the matrix in reverse. For each cell `(r, c)`:
        *   `dist[r][c] = min(dist[r][c], dist[r+1][c] + 1, dist[r][c+1] + 1)` (again, handling boundary conditions).
    This DP approach also has O(m*n) time and O(m*n) space complexity. It might offer slight performance advantages in some environments due to better cache locality and no queue overhead, but conceptually it achieves the same "spreading" of minimum distances. For an educator, it's a good alternative to discuss.

### 7. Security/Performance Notes

*   **Security:** This code does not interact with external systems, user input beyond the matrix itself, or sensitive data. Therefore, there are no direct security concerns applicable to this specific algorithm.
*   **Performance:**
    *   The chosen BFS algorithm is optimal for the problem's constraints (unweighted graph, shortest path). You cannot achieve a better asymptotic time complexity than O(m*n).
    *   The use of `collections.deque` is a good performance choice for the queue, preventing O(N) operations that would occur with a standard Python list's `pop(0)`.
    *   For extremely large matrices (e.g., millions of cells), Python's overhead for list-of-lists and object creation might become a factor. In such highly performance-critical scenarios, one might consider using libraries like NumPy for more efficient array operations, though the core algorithm would remain the same. For typical competitive programming constraints, the current implementation is perfectly adequate.

### Code:
```python
from typing import List
import collections
import math

class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:
        m = len(mat)
        n = len(mat[0])
        
        dist = [[math.inf for _ in range(n)] for _ in range(m)]
        queue = collections.deque()
        
        for r in range(m):
            for c in range(n):
                if mat[r][c] == 0:
                    dist[r][c] = 0
                    queue.append((r, c))
        
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
        
        while queue:
            r, c = queue.popleft()
            
            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                
                if 0 <= nr < m and 0 <= nc < n and dist[nr][nc] == math.inf:
                    dist[nr][nc] = dist[r][c] + 1
                    queue.append((nr, nc))
                    
        return dist
```
