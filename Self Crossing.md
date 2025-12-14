## Self Crossing
**Language:** python
**Tags:** python,oop,geometry,array

### Description:
### 1. Overview & Intent

The code defines a `Solution` class with a single method `isSelfCrossing`.

*   **Intent:** Determine if a path defined by a sequence of distances will cross itself. The path starts at `(0,0)`, moves `distance[0]` units in the positive Y direction (or typically positive X, but the orientation doesn't change the self-crossing logic), then turns 90 degrees left, moves `distance[1]`, turns 90 degrees left, moves `distance[2]`, and so on.
*   **Problem Context:** This is a classic geometric path problem often found in competitive programming. The key challenge is to efficiently detect intersections without simulating the path point by point.

---

### 2. How It Works

The solution leverages a specific set of geometric conditions to detect self-intersections. Instead of tracking (x,y) coordinates or performing general line segment intersection tests, it identifies three distinct patterns of segments that result in a crossing.

The path is built segment by segment. For each new segment `distance[i]`, the code checks if this segment intersects any previous segments based on their lengths relative to each other.

1.  **Initialization:** Handles paths with fewer than 4 segments, which cannot cross themselves, by returning `False`.
2.  **Iteration:** It iterates from the 4th segment (`i = 3`) up to the last segment. This is because the first possible intersection patterns require at least 4 segments.
3.  **Case 1: Outer Cross (i.e., `d[i]` crosses `d[i-3]`)**
    *   This occurs when the current segment `d[i]` extends beyond `d[i-2]` (the segment parallel to `d[i]` from two steps back) *and* the segment `d[i-1]` (perpendicular to `d[i]`) is shorter than or equal to `d[i-3]` (the segment parallel to `d[i-1]` from three steps back). Imagine an "unwinding" or "pinching" spiral.
4.  **Case 2: Inner Cross (i.e., `d[i]` crosses `d[i-4]`)**
    *   Requires at least 5 segments (`i >= 4`).
    *   This is a more specific case where `d[i-1]` (perpendicular to `d[i]`) is exactly equal to `d[i-3]` (the segment parallel to `d[i-1]` from three steps back). In this scenario, `d[i]` crosses `d[i-4]` if the sum of `d[i]` and `d[i-4]` is greater than or equal to `d[i-2]`. This typically describes an outward spiral that "pokes through" a previous layer.
5.  **Case 3: Complex Inner Cross (i.e., `d[i]` crosses `d[i-5]`)**
    *   Requires at least 6 segments (`i >= 5`).
    *   This is the most complex scenario, representing an "inward" spiral that has shrunk so much it crosses a segment from even further back. It involves a combination of conditions relating `d[i]`, `d[i-1]`, `d[i-2]`, `d[i-3]`, `d[i-4]`, and `d[i-5]`, ensuring specific length relationships for both parallel and perpendicular segments to allow the current segment `d[i]` to cross `d[i-5]`.
6.  **Return `False`:** If no crossing is detected after checking all segments, the function returns `False`.

---

### 3. Key Design Decisions

*   **Geometric Pattern Matching:** The most crucial design decision is to forgo explicit coordinate tracking and general line-segment intersection algorithms. Instead, the solution relies on pre-analyzed geometric patterns of self-crossing that arise from the "always turn 90 degrees left" rule. This makes the solution extremely efficient and robust against floating-point inaccuracies.
*   **Local Checks:** The algorithm only needs to compare the current segment `d[i]` with a small, fixed window of previous segments (`d[i-1]` to `d[i-5]`). This is because segments far apart in the sequence are unlikely to cross unless they are part of a very large, complex spiral, which these three patterns are designed to cover.
*   **Early Exit:** As soon as any of the three crossing conditions are met, the function immediately returns `True`. This optimizes for common cases where crossings occur early in the path.

---

### 4. Complexity

*   **Time Complexity: O(N)**
    *   The code iterates through the `distance` list exactly once, from `i = 3` to `n-1`.
    *   Inside the loop, it performs a constant number of array accesses, comparisons, and arithmetic operations for each `i`.
    *   Therefore, the time complexity is directly proportional to the number of segments `N`.
*   **Space Complexity: O(1)**
    *   The algorithm only uses a few integer variables (`n`, `i`) to keep track of the loop and indices.
    *   It does not allocate any new data structures whose size depends on `N`.
    *   Thus, the auxiliary space complexity is constant.

---

### 5. Edge Cases & Correctness

*   **`n < 4` (Not enough segments):** The code correctly handles this by returning `False` at the beginning. It's impossible for a path with 0, 1, 2, or 3 segments to cross itself under these rules.
*   **Minimum Segments for Cases:**
    *   Case 1 (cross `d[i-3]`) requires `i >= 3`, which is the starting point of the loop. This means at least 4 segments (`distance[0]` to `distance[3]`).
    *   Case 2 (cross `d[i-4]`) requires `i >= 4`. The `if i >= 4:` check ensures this. This means at least 5 segments.
    *   Case 3 (cross `d[i-5]`) requires `i >= 5`. The `if i >= 5:` check ensures this. This means at least 6 segments.
    *   These minimum segment checks are crucial for preventing `IndexError` and ensuring correctness.
*   **Zero-length segments:** While `distance` values are typically positive in such problems, if `0` were allowed, it means a segment that effectively doesn't move. The geometric conditions would still hold, as `0` would be treated as a valid length in the comparisons. For example, `0 >= d[i-2]` would be false unless `d[i-2]` is also zero or negative (which is usually not allowed).
*   **Correctness:** The three cases identified cover all possible scenarios for self-crossing paths generated by consecutive 90-degree left turns. This is a known result in computational geometry, making the solution robust.

---

### 6. Improvements & Alternatives

*   **Comments & Clarity:** The existing comments are good. For educational purposes, one might expand them slightly to explain the geometric intuition behind *why* each condition implies a cross. For example, explicitly stating "the outer segment extends beyond the inner" for `d[i] >= d[i-2]` in Case 1.
*   **Visual Aids:** For explaining this code, especially in an educational context, visual diagrams for each of the three crossing cases would be immensely helpful. (This is outside the scope of code review, but a note for teaching).
*   **Alternative (Less Efficient):** A more general but less efficient approach would be to track the `(x, y)` coordinates of each segment's start and end points and then perform pairwise line segment intersection tests for all non-adjacent segments. This would typically lead to O(N^2) time complexity and introduce challenges with floating-point precision. The current approach is highly optimized for this specific problem.
*   **Code Structure:** The nested `if` statements for `i >= 4` and `i >= 5` are clear. They could be combined with `and` into the condition for the inner `if` (e.g., `if i >= 4 and distance[i-1] == distance[i-3]...`), but the current structure is also perfectly readable.

---

### 7. Security/Performance Notes

*   **Performance:** The O(N) time complexity and O(1) space complexity are optimal for this problem, as every segment must at least be considered to determine if a crossing occurs. There are no obvious performance bottlenecks.
*   **Security:** This code does not involve any external inputs, network communication, file operations, or complex data manipulation that could lead to security vulnerabilities. Security considerations are not applicable here.

### Code:
```python
import collections
from typing import List

class Solution:
    def isSelfCrossing(self, distance: List[int]) -> bool:
        n = len(distance)
        if n < 4: # Not enough segments to cross
            return False

        for i in range(3, n):
            # Case 1: Current segment d[i] crosses segment d[i-3].
            # This happens when an outward spiral gets pinched.
            #   d[i] >= d[i-2] (current segment extends past the previous parallel segment)
            #   d[i-1] <= d[i-3] (the segment before current is shorter than its parallel segment from 3 steps ago)
            if distance[i] >= distance[i-2] and distance[i-1] <= distance[i-3]:
                return True

            # Case 2: Current segment d[i] crosses segment d[i-4].
            # This is a specific outward spiral getting pinched, where d[i-1] and d[i-3] are equal.
            # Requires at least 5 segments (i >= 4).
            #   d[i-1] == d[i-3] (the segment before current is equal to its parallel segment from 3 steps ago)
            #   d[i] + d[i-4] >= d[i-2] (current segment plus segment 4 steps ago is long enough to cross d[i-2])
            if i >= 4:
                if distance[i-1] == distance[i-3] and distance[i] + distance[i-4] >= distance[i-2]:
                    return True

            # Case 3: Current segment d[i] crosses segment d[i-5].
            # This is an inward spiral that has shrunk and crosses itself again.
            # Requires at least 6 segments (i >= 5).
            #   d[i-2] >= d[i-4] (inner parallel segment is longer or equal to outer parallel segment)
            #   d[i-3] >= d[i-1] (inner perpendicular segment is longer or equal to outer perpendicular segment)
            #   d[i-1] + d[i-5] >= d[i-3] (previous segment + segment 5 steps ago >= segment 3 steps ago)
            #   d[i] + d[i-4] >= d[i-2] (current segment + segment 4 steps ago >= segment 2 steps ago)
            if i >= 5:
                if (distance[i-2] >= distance[i-4] and
                    distance[i-3] >= distance[i-1] and
                    distance[i-1] + distance[i-5] >= distance[i-3] and
                    distance[i] + distance[i-4] >= distance[i-2]):
                    return True

        return False
```
