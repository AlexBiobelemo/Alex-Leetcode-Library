## Sliding Puzzle
**Language:** python
**Tags:** python,oop,bfs,queue

### Description:
This code implements a solution to the "Sliding Puzzle" problem, which is a variation of the 15-puzzle on a 2x3 board. The goal is to find the minimum number of moves to transform a given initial board configuration into a target configuration "123450" by sliding the '0' (empty) tile.

---

### 1. Overview & Intent

The code aims to solve the 2x3 sliding puzzle problem by finding the shortest sequence of moves to reach a specific target state. It leverages a Breadth-First Search (BFS) algorithm, which is ideal for finding the shortest path in an unweighted graph where each move has a uniform cost (1 move). The "states" of the puzzle (board configurations) are treated as nodes in a graph, and a "move" (sliding the '0' tile) represents an edge between two states.

---

### 2. How It Works

1.  **Initialization**:
    *   `target_state = "123450"`: Defines the desired final configuration as a string.
    *   `start_state`: The input `board` (a 2x3 list of lists of integers) is flattened and converted into a string (e.g., `[[1,2,3],[4,0,5]]` becomes `"123405"`). This string representation serves as a unique identifier for each board state.
    *   **Early Exit**: If `start_state` is already `target_state`, it immediately returns `0` moves.
    *   `adj`: A precomputed adjacency list. This is a crucial optimization. It maps each possible position of the '0' tile (from 0 to 5 on the flattened board) to a list of indices where the '0' tile can legally swap with. This avoids complex boundary checks during the search.
        ```
        Board indices:
        0 1 2
        3 4 5
        ```
        For example, if '0' is at index `0`, it can swap with tiles at index `1` (right) or `3` (down).
    *   `q = collections.deque([(start_state, 0)])`: Initializes a double-ended queue for BFS. Each element is a tuple `(board_string, moves_count)`. The initial state is added with `0` moves.
    *   `visited = {start_state}`: A set to store all board states that have already been visited. This prevents cycles and redundant processing, ensuring that each state is processed at most once.

2.  **BFS Traversal**:
    *   The `while q:` loop continues as long as there are states to explore.
    *   `current_board_str, moves = q.popleft()`: Retrieves the next state to process and the number of moves taken to reach it.
    *   `zero_idx = current_board_str.find('0')`: Finds the current position of the empty tile ('0') on the flattened board string.
    *   **Generate Next States**:
        *   It iterates through `neighbor_idx` in `adj[zero_idx]`. These are the positions where the '0' tile can move.
        *   For each `neighbor_idx`:
            *   `new_board_list = list(current_board_str)`: The current board string is converted to a mutable list of characters to facilitate swapping.
            *   `new_board_list[zero_idx], new_board_list[neighbor_idx] = ...`: The '0' tile is swapped with the tile at `neighbor_idx`.
            *   `new_board_str = "".join(new_board_list)`: The modified list is converted back into a string, representing the new board state.
    *   **Check New State**:
        *   `if new_board_str == target_state:`: If the newly generated state is the target, the shortest path has been found. It returns `moves + 1` (since one more move was just made).
        *   `if new_board_str not in visited:`: If this new state has not been visited before:
            *   `visited.add(new_board_str)`: Add it to the `visited` set.
            *   `q.append((new_board_str, moves + 1))`: Add the new state and its corresponding move count to the queue for future exploration.

3.  **Unsolvable Puzzle**:
    *   If the `while q:` loop finishes and the `target_state` was never reached, it means the puzzle is impossible to solve from the `start_state`. In this case, the function returns `-1`.

---

### 3. Key Design Decisions

*   **Breadth-First Search (BFS)**: This is the core algorithm choice. BFS guarantees finding the shortest path (minimum number of moves) in an unweighted graph, which perfectly fits the problem's requirement.
*   **State Representation (String)**:
    *   The board state is represented as a 6-character string (e.g., "123450").
    *   **Advantages**: Strings are immutable and hashable in Python, making them suitable for use as keys in the `visited` set (for efficient lookup) and for direct comparison. They are also easy to construct and manipulate for this small fixed size.
    *   **Trade-offs**: Converting to a list for swapping and back to a string incurs some overhead, but for a 6-character string, this overhead is minimal and acceptable. An alternative could be a tuple of integers, which is also immutable and hashable.
*   **Precomputed Adjacency List (`adj`)**:
    *   This is a highly effective optimization. Instead of calculating valid moves for the '0' tile on the fly (e.g., checking if `zero_idx` is on the edge or corner), the `adj` list provides constant-time lookup for all possible moves from any given `zero_idx`.
    *   **Trade-offs**: It's hardcoded for a 2x3 board, making the solution less generalizable to N x M puzzles without modifying this structure. However, for a fixed problem size, it's very efficient.
*   **`collections.deque` for Queue**: `deque` provides O(1) time complexity for `append()` and `popleft()` operations, which are crucial for efficient BFS. A standard Python `list` would have O(N) for `pop(0)`.
*   **`set` for `visited` States**: Using a `set` allows for average O(1) time complexity for adding elements (`add()`) and checking for existence (`in`), which is essential for preventing redundant computations and infinite loops in graph traversal.

---

### 4. Complexity Analysis

Let `N` be the total number of tiles (6 for a 2x3 board).
The number of possible permutations for 6 distinct tiles is `6! = 720`. This is the maximum number of unique states (nodes in our graph). Let `V` be the number of reachable states, which is at most `6!`.
Let `L` be the length of the board string (6).
Let `D` be the maximum degree of any node (maximum number of neighbors for '0', which is 3).

*   **Time Complexity**:
    *   Each state (node) is visited and processed at most once due to the `visited` set.
    *   For each state:
        *   `current_board_str.find('0')`: O(L)
        *   Looping through neighbors (`adj[zero_idx]`): At most `D` iterations.
        *   Inside the loop:
            *   `list(current_board_str)`: O(L)
            *   Swapping: O(1)
            *   `"".join(new_board_list)`: O(L)
            *   `new_board_str not in visited`: Average O(L) (due to string hashing and comparison).
    *   Total time complexity is approximately `O(V * (L + D * L))`, which simplifies to `O(V * L * D)`.
    *   Since `V` (max 720), `L` (6), and `D` (3) are all small constants, the overall time complexity is effectively **O(1)** relative to the input size (which is fixed). If we consider the state space as variable, it's `O(N! * N * D)`.

*   **Space Complexity**:
    *   `visited` set: Stores up to `V` board states. Each state is a string of length `L`. So, `O(V * L)`.
    *   `q` deque: In the worst case, it can hold a significant portion of the reachable states. Each element is `(string, int)`, so `O(V * L)`.
    *   Total space complexity is `O(V * L)`.
    *   Again, since `V` and `L` are constants for this problem, the space complexity is effectively **O(1)**.

---

### 5. Edge Cases & Correctness

*   **Start State is Target State**: Handled correctly by `if start_state == target_state: return 0`.
*   **Unsolvable Puzzle**: The BFS will explore all reachable states. If the target state is not among them, the queue will eventually become empty, and the function correctly returns `-1`. (Note: Not all 6! permutations are reachable from a given start state; the parity of permutations determines reachability. BFS naturally explores only the reachable component.)
*   **Minimum Moves**: BFS guarantees finding the minimum number of moves because it explores states level by level, ensuring that the first time the target state is encountered, it's via the shortest path.
*   **Input Validity**: The problem implicitly assumes a valid 2x3 board containing unique integers from 0 to 5. The code does not perform explicit input validation (e.g., checking dimensions, content, or uniqueness of tiles), which is common in competitive programming contexts.

---

### 6. Improvements & Alternatives

*   **Generalization**:
    *   The `adj` list is hardcoded for a 2x3 board. For a more general N x M sliding puzzle, this list would need to be generated dynamically based on `zero_idx`'s row and column (e.g., `row = zero_idx // M`, `col = zero_idx % M`) and checking `(row-1, col)`, `(row+1, col)`, `(row, col-1)`, `(row, col+1)` bounds.
*   **Readability/Constants**:
    *   Define constants for board dimensions (e.g., `ROWS = 2`, `COLS = 3`, `BOARD_SIZE = ROWS * COLS`) to make the code more self-documenting, especially if `adj` were to be generated dynamically.
*   **State Representation (Tuple of Integers)**:
    *   Instead of a string, a `tuple` of integers (e.g., `(1, 2, 3, 4, 5, 0)`) could be used for state representation. Tuples are also immutable and hashable.
    *   **Potential Benefit**: Integer comparisons and hashing might be marginally faster than string operations, though for 6 elements, the difference is likely negligible.
    *   **Trade-off**: Swapping elements in a tuple would require converting to a list, performing the swap, and converting back to a tuple, similar to the string approach.
*   **A\* Search**: For larger puzzles where the state space is too vast for plain BFS, A\* search with a good heuristic (e.g., Manhattan distance of all tiles to their target positions, excluding '0') could significantly reduce the search space. However, for a 2x3 puzzle, BFS is perfectly adequate and simpler to implement.
*   **IDA\* (Iterative Deepening A\*)**: If memory were a critical constraint (BFS can consume a lot of memory for very large state spaces), IDA\* could be an alternative, as it has O(depth) space complexity. Not necessary here.

---

### 7. Security/Performance Notes

*   **Security**: There are no inherent security vulnerabilities in this code. It operates purely on internal data structures and does not interact with external systems or user input in a way that could introduce risks.
*   **Performance**:
    *   The code is highly performant for the given problem constraints (2x3 board). The small, fixed state space (max 720 reachable states) means the BFS completes very quickly.
    *   The use of `collections.deque` and `set` are optimal choices for BFS in Python, contributing to its efficiency.
    *   String operations, while not O(1), are very efficient for short, fixed-length strings like 6 characters. The overhead is minimal.

### Code:
```python
import collections

class Solution:
    def slidingPuzzle(self, board: List[List[int]]) -> int:
        target_state = "123450"
        
        # Convert the initial board to a string representation
        start_state = "".join(str(x) for row in board for x in row)
        
        # If the starting state is already the target, 0 moves are needed
        if start_state == target_state:
            return 0
            
        # Adjacency list for swapping the '0' tile
        # Each index corresponds to a position on the flattened 2x3 board (0-5)
        # The values are the indices of adjacent positions that '0' can swap with.
        # Board indices:
        # 0 1 2
        # 3 4 5
        adj = [
            [1, 3],     # Neighbors of index 0 (row 0, col 0)
            [0, 2, 4],  # Neighbors of index 1 (row 0, col 1)
            [1, 5],     # Neighbors of index 2 (row 0, col 2)
            [0, 4],     # Neighbors of index 3 (row 1, col 0)
            [1, 3, 5],  # Neighbors of index 4 (row 1, col 1)
            [2, 4]      # Neighbors of index 5 (row 1, col 2)
        ]
        
        # BFS queue: stores tuples of (current_board_string, moves_count)
        q = collections.deque([(start_state, 0)])
        
        # Set to keep track of visited board states to avoid cycles and redundant processing
        visited = {start_state}
        
        while q:
            current_board_str, moves = q.popleft()
            
            # Find the current position of the '0' tile
            zero_idx = current_board_str.find('0')
            
            # Explore all possible moves from the current state
            for neighbor_idx in adj[zero_idx]:
                # Create the new board string by swapping '0' with its neighbor
                new_board_list = list(current_board_str)
                new_board_list[zero_idx], new_board_list[neighbor_idx] = \
                    new_board_list[neighbor_idx], new_board_list[zero_idx]
                new_board_str = "".join(new_board_list)
                
                # If this new state is the target, we have found the shortest path
                if new_board_str == target_state:
                    return moves + 1
                
                # If this new state has not been visited, add it to the queue
                if new_board_str not in visited:
                    visited.add(new_board_str)
                    q.append((new_board_str, moves + 1))
                    
        # If the queue becomes empty and the target state was never reached,
        # it means the puzzle is impossible to solve.
        return -1
```
