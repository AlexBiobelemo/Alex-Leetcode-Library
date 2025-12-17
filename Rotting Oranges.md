## Rotting Oranges
**Language:** python
**Tags:** python,bfs,queue,grid

### Description:
### 1. Overview & Intent

This code aims to solve the "Rotting Oranges" problem. Given a grid representing a farm of oranges, where `0` is an empty cell, `1` is a fresh orange, and `2` is a rotten orange, the goal is to determine the minimum time (in minutes) until all fresh oranges have rotted. A rotten orange can rot any adjacent fresh orange (up, down, left, right) in one minute. If it's impossible for all fresh oranges to rot, the function should return `-1`.

The chosen approach is a Breadth-First Search (BFS), which is ideal for finding the shortest path or minimum time in unweighted graphs (or grid problems that can be modeled as such). The "time" here corresponds to the "level" in the BFS traversal.

### 2. How It Works

The solution proceeds in a few distinct phases:

1.  **Initialization (Pre-BFS Scan)**:
    *   It first determines the dimensions of the grid (`m` rows, `n` columns).
    *   A `collections.deque` named `q` is initialized. This will serve as the queue for the BFS, storing the coordinates of rotten oranges.
    *   A counter `fresh_oranges` is initialized to track the total number of fresh oranges present in the grid.
    *   The code iterates through every cell (`r`, `c`) of the grid:
        *   If `grid[r][c]` is `2` (rotten), its coordinates `(r, c)` are added to the `q`. These are the initial sources of rot.
        *   If `grid[r][c]` is `1` (fresh), `fresh_oranges` is incremented.
    *   An early exit condition: If `fresh_oranges` is `0` after the initial scan, it means there are no fresh oranges to rot, so `0` minutes are needed.

2.  **BFS Traversal (Minute-by-Minute Rotting)**:
    *   `minutes` is initialized to `0`. This variable will store the elapsed time.
    *   `directions` is a list of tuples `[(0, 1), (0, -1), (1, 0), (-1, 0)]` representing the four cardinal directions (right, left, down, up).
    *   The main `while q:` loop continues as long as there are rotten oranges in the queue to process. Each iteration of this outer loop effectively represents one minute passing.
    *   **Level Processing**: Inside the `while` loop, `size = len(q)` captures the number of oranges that rotted in the *previous* minute. The inner `for _ in range(size):` loop processes *only* these oranges. This is the key to a level-order BFS, ensuring that all oranges at a certain "distance" (time) from the initial rotten oranges are processed before moving to the next distance.
    *   For each `(r, c)` (a rotten orange) popped from the queue:
        *   It iterates through the `directions` to find its neighbors `(nr, nc)`.
        *   **Validity Check**: It checks if the neighbor `(nr, nc)` is within the grid boundaries (`0 <= nr < m` and `0 <= nc < n`).
        *   **Rotting Condition**: If the neighbor is valid and `grid[nr][nc]` is `1` (fresh):
            *   The fresh orange is rotted: `grid[nr][nc] = 2`.
            *   `fresh_oranges` is decremented.
            *   The newly rotten orange `(nr, nc)` is added to the `q` to be processed in the *next* minute.
            *   A flag `rotted_in_this_minute` is set to `True` to indicate that at least one orange rotted in the current minute.
    *   **Minute Increment**: After processing all oranges that rotted in the *previous* minute (the inner `for` loop completes), if `rotted_in_this_minute` is `True`, it means new oranges were rotted, so `minutes` is incremented. This ensures that `minutes` only increases when actual rotting occurs, preventing an unnecessary increment if the queue only contained rotten oranges with no fresh neighbors.

3.  **Final Result**:
    *   After the `while q` loop finishes (meaning no more fresh oranges can be reached or all have rotted), the code checks `fresh_oranges` again:
        *   If `fresh_oranges == 0`, all fresh oranges have rotted, and `minutes` holds the correct minimum time.
        *   If `fresh_oranges > 0`, it means some fresh oranges were unreachable by the rot, so `-1` is returned.

### 3. Key Design Decisions

*   **Breadth-First Search (BFS)**: This is the fundamental algorithm choice. BFS guarantees finding the shortest path (or minimum time in this context) because it explores all nodes at a given "distance" (time level) before moving to the next distance.
*   **`collections.deque`**: Python's `deque` (double-ended queue) is used for the BFS queue. It provides O(1) time complexity for `append()` (adding to the right) and `popleft()` (removing from the left), which are crucial for efficient BFS. A standard list would have O(N) for `pop(0)`.
*   **In-place Grid Modification**: When a fresh orange `grid[nr][nc]` is rotted, its value is changed from `1` to `2`. This serves two purposes:
    1.  It marks the orange as rotten, reflecting the state change.
    2.  It acts as a "visited" set, preventing the BFS from re-processing already rotten oranges or adding them to the queue multiple times.
*   **`fresh_oranges` Counter**: Maintaining a separate counter for fresh oranges is highly efficient. Instead of scanning the entire grid at the end to check for remaining fresh oranges, this counter provides an O(1) check.
*   **Level-Order Processing with `size` and `rotted_in_this_minute`**: The pattern of capturing `size = len(q)` at the beginning of each outer loop iteration and then iterating `size` times is a standard and robust way to implement level-order BFS

### Code:
```python
import collections

class Solution:
    def orangesRotting(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        q = collections.deque()
        fresh_oranges = 0

        for r in range(m):
            for c in range(n):
                if grid[r][c] == 2:
                    q.append((r, c))
                elif grid[r][c] == 1:
                    fresh_oranges += 1

        if fresh_oranges == 0:
            return 0

        minutes = 0
        directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]

        while q:
            rotted_in_this_minute = False
            size = len(q)
            for _ in range(size):
                r, c = q.popleft()

                for dr, dc in directions:
                    nr, nc = r + dr, c + dc

                    if 0 <= nr < m and 0 <= nc < n and grid[nr][nc] == 1:
                        grid[nr][nc] = 2
                        fresh_oranges -= 1
                        q.append((nr, nc))
                        rotted_in_this_minute = True
            
            if rotted_in_this_minute:
                minutes += 1
            
        if fresh_oranges == 0:
            return minutes
        else:
            return -1
```
