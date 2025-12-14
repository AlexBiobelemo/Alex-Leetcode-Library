## Number of Pairs of Interchangeable Rectangles
**Language:** python
**Tags:** python,oop,hashmap,gcd,combinatorics

### Description:
This code calculates the number of pairs of "interchangeable" rectangles from a given list. Two rectangles are considered interchangeable if their aspect ratios (width / height) are the same.

### 1. Overview & Intent

The primary goal of this code is to count how many unique pairs of rectangles exist in an input list such that the rectangles within each pair have identical aspect ratios. It achieves this by standardizing each rectangle's ratio and then counting combinations of rectangles that share the same standardized ratio.

### 2. How It Works

1.  **Initialize Counter:** A `defaultdict(int)` named `ratio_counts` is created. This dictionary will store the frequency of each unique, simplified aspect ratio encountered.
2.  **Process Rectangles:**
    *   The code iterates through each `[width, height]` pair in the input `rectangles` list.
    *   For each pair, it calculates the Greatest Common Divisor (GCD) of its width and height using `math.gcd()`.
    *   It then divides both the width and height by this `common_divisor` to get `simplified_width` and `simplified_height`. This step is crucial because it ensures that rectangles like `[1, 2]` and `[2, 4]` (both having a 1:2 ratio) are recognized as having the same simplified ratio `(1, 2)`.
    *   The simplified `(width, height)` tuple is used as a key in `ratio_counts`, and its count is incremented.
3.  **Calculate Total Pairs:**
    *   After all rectangles have been processed and their simplified ratios counted, `total_pairs` is initialized to 0.
    *   The code then iterates through the *values* (the counts) stored in `ratio_counts`.
    *   For any `count` greater than or equal to 2 (meaning there are at least two rectangles with that specific ratio), it calculates the number of unique pairs that can be formed from these `count` rectangles. This is done using the combination formula "n choose 2": `count * (count - 1) / 2`.
    *   This result is added to `total_pairs`.
4.  **Return Result:** Finally, `total_pairs` holds the total count of interchangeable rectangle pairs and is returned.

### 3. Key Design Decisions

*   **Simplifying Ratios with GCD:** This is the most critical design choice. Instead of using floating-point division (`width / height`) which can lead to precision issues (e.g., 0.3333333333 and 0.3333333334 might be considered different), `math.gcd` provides an exact, integer-based method to normalize ratios. Storing `(simplified_width, simplified_height)` ensures perfect comparison.
*   **`defaultdict(int)`:** This data structure simplifies the counting process. When a new simplified ratio is encountered, it automatically gets an initial count of 0 before being incremented, avoiding explicit `if key not in dict` checks.
*   **Tuple as Dictionary Key:** Using `(simplified_width, simplified_height)` as a key is effective because tuples are immutable and hashable in Python, making them suitable for dictionary keys.
*   **Combinations Formula:** The use of `n * (n - 1) // 2` correctly calculates the number of unique pairs from a group of `n` items. The integer division `//` ensures the result is an integer, as expected for counts.

### 4. Complexity

*   **Time Complexity:** `O(N * log(max_dim))`
    *   The first loop iterates `N` times (where `N` is the number of rectangles).
    *   Inside the loop, `math.gcd(width, height)` takes logarithmic time, approximately `O(log(min(width, height)))`. Let `max_dim` be the maximum possible value for width or height. So, each GCD operation is `O(log(max_dim))`.
    *   Dictionary operations (hashing a tuple, incrementing a count) are typically `O(1)` on average.
    *   The second loop iterates through the `ratio_counts.values()`. In the worst case, all `N` rectangles have unique ratios, so this loop runs `N` times. Operations inside are `O(1)`.
    *   Combining these, the dominant factor is `N` times the GCD calculation, leading to `O(N * log(max_dim))`.
*   **Space Complexity:** `O(N)`
    *   The `ratio_counts` dictionary stores at most `N` unique simplified ratio tuples. Each tuple contains two integers.
    *   In the worst case (all rectangles have distinct ratios), `ratio_counts` will store `N` entries.

### 5. Edge Cases & Correctness

*   **Empty Input List (`rectangles = []`):** The `ratio_counts` dictionary remains empty. Both loops are skipped, and `0` is correctly returned.
*   **Single Rectangle (`rectangles = [[w, h]]`):** `ratio_counts` will contain one entry with a count of `1`. The `if count >= 2` condition will not be met, and `0` is correctly returned (no pairs can be formed from a single rectangle).
*   **All Rectangles Have Same Ratio:** All rectangles will simplify to the same `(simplified_width, simplified_height)` tuple. `ratio_counts` will have one entry with a count equal to `N` (total rectangles). The formula `N * (N - 1) // 2` will correctly calculate all possible pairs.
*   **Dimensions of Zero:** Problem constraints for competitive programming typically specify positive dimensions. If dimensions could be zero, `math.gcd(0, X)` returns `X`, and `math.gcd(0, 0)` returns `0`. This would lead to `simplified_width` or `simplified_height` being `0` or `0/0` (if common_divisor is 0), which might need specific handling depending on problem rules (e.g., if ratios like `0/5` or `5/0` are allowed and how they should be compared). Assuming positive integer dimensions as is standard.
*   **Large Integer Dimensions:** Python's integers have arbitrary precision, so `math.gcd` will handle very large width/height values without overflow. The `log(max_dim)` factor would increase, but correctness is maintained.

### 6. Improvements & Alternatives

*   **Readability:** The code is already very readable with clear variable names and comments. No significant readability improvements are necessary.
*   **Performance (Minor):** The current approach is optimal for correctness and efficiency given the problem constraints. There are no obvious major performance bottlenecks that don't involve fundamental changes to the problem definition (like allowing floating-point comparisons).
*   **Alternative Ratio Representation:** While the `(simplified_width, simplified_height)` tuple is excellent, one could theoretically create a custom `Fraction` class or convert the ratio to a string like `f"{simplified_width}/{simplified_height}"` to use as keys. However, the tuple is Pythonic and efficient.

### 7. Security/Performance Notes

*   **Performance:** The use of `math.gcd` is efficient for integer greatest common divisor calculations. Python's `defaultdict` and dictionary operations are highly optimized. The overall `O(N log(max_dim))` complexity is generally good for typical constraints (`N` up to 10^5, dimensions up to 10^9).
*   **Security:** This code is purely computational and operates on provided input lists. There are no external dependencies, file I/O, network operations, or user-supplied string interpretations that could introduce security vulnerabilities. It's a self-contained algorithm.

### Code:
```python
import math
from collections import defaultdict
from typing import List

class Solution:
    def interchangeableRectangles(self, rectangles: List[List[int]]) -> int:
        ratio_counts = defaultdict(int) # Dictionary to store frequencies of simplified (width, height) tuples

        for width, height in rectangles:
            common_divisor = math.gcd(width, height) # Calculate the greatest common divisor
            simplified_width = width // common_divisor # Simplify the width
            simplified_height = height // common_divisor # Simplify the height
            ratio_counts[(simplified_width, simplified_height)] += 1 # Increment count for this simplified ratio

        total_pairs = 0
        for count in ratio_counts.values():
            # If 'count' rectangles have the same ratio, the number of pairs is count * (count - 1) / 2
            if count >= 2:
                total_pairs += (count * (count - 1)) // 2 # Add combinations of 2 from 'count' items

        return total_pairs
```
