# Binary Search — Revision Notes

> Python only. Follow the AlgoMaster course order. One problem at a time. These notes summarize the Binary Search patterns and the problems already practiced. Focus on reusable reasoning, not memorizing solutions.

## Progress

- ✅ Standard Binary Search
- ✅ Boundary Search / First True
- ✅ Lower Bound and Upper Bound
- ✅ Modified Binary Search
- ✅ Binary Search on Answer
- ⏸️ Combined Binary Search + Greedy problems — revisit after learning Greedy
- ⬜ Binary Search on Matrix
- ⬜ Binary Search Advanced

---

# 1) Sub-Pattern Map

```text
Binary Search
|
+-- Standard Binary Search
|   +-- Search exact target in sorted array
|   +-- while left <= right
|
+-- Boundary Binary Search
|   +-- First True / First Bad
|   +-- Lower Bound: first value >= target
|   +-- Upper Bound: first value > target
|
+-- Modified Binary Search
|   +-- Rotated sorted array
|   +-- Rotated array with duplicates
|   +-- Minimum in rotated array
|   +-- Peak element
|   +-- Mountain array
|
+-- Binary Search on Answer
    +-- Find minimum feasible answer (First True)
    +-- Feasibility check: can this answer work?
    +-- Koko Eating Bananas
    +-- Capacity to Ship Packages Within D Days
    +-- Minimum Number of Days to Make m Bouquets

Combined problems on hold until Greedy is learned:
    +-- Magnetic Force Between Two Balls
    +-- Split Array Largest Sum
```

## Quick Recognition

```text
Sorted array + exact target?          -> Standard Binary Search
First / last occurrence or boundary?  -> Boundary Search
Rotated / mountain / peak structure?  -> Modified Binary Search
Minimum answer with yes/no check?     -> Binary Search on Answer
```

---

# 2) Core Idea

Binary Search repeatedly removes the part of the search space that cannot contain the answer.

```text
Search space
    ↓
Check the middle
    ↓
Decide which side can still contain the answer
    ↓
Discard the other side
    ↓
Repeat
```

Before coding, answer these questions:

1. What exactly am I searching for?
2. What do `left` and `right` represent?
3. What does checking `mid` tell me?
4. Which half can I safely discard?
5. Should the loop use `left <= right` or `left < right`?

Binary Search needs sorted order or a monotonic condition.

---

# 3) Standard Binary Search

## When to use

Use when the array is sorted and you need to find an exact target.

## Template — Closed Interval

The search range includes both ends: `[left, right]`.

```python
def binary_search(nums, target):
    left = 0
    right = len(nums) - 1

    while left <= right:
        mid = (left + right) // 2

        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

## Pointer decisions

```text
nums[mid] == target -> return mid
nums[mid] < target  -> left = mid + 1
nums[mid] > target  -> right = mid - 1
```

We use `mid + 1` or `mid - 1` because `mid` has already been checked and is not the answer.

- Time: `O(log n)`
- Extra space: `O(1)`

## Problems practiced

- Binary Search
- Guess Number Higher or Lower
- Search Insert Position (boundary variant is also useful)
- Valid Perfect Square

### Valid Perfect Square — key idea

Search for an integer `mid` whose square equals `num`.

```python
if mid * mid == num:
    return True
elif mid * mid < num:
    left = mid + 1
else:
    right = mid - 1
```

Avoid floating-point square roots when the task is to solve it with Binary Search.

### Guess Number Higher or Lower — key idea

The API tells whether the guess is too low, too high, or correct.

```text
guess(mid) == 0  -> found
guess(mid) == -1 -> guess is too high; move left
guess(mid) == 1  -> guess is too low; move right
```

---

# 4) Boundary Binary Search — First True

## Recognition

Use when a monotonic condition changes once:

```text
False False False True True True
                  ↑
              first true
```

## Template

```python
while left < right:
    mid = (left + right) // 2

    if condition(mid):
        right = mid
    else:
        left = mid + 1

return left
```

Why?

- If `condition(mid)` is true, `mid` might be the first true, so keep it: `right = mid`.
- If false, the first true must be after `mid`: `left = mid + 1`.

## First Bad Version

```python
left = 1
right = n

while left < right:
    mid = (left + right) // 2

    if isBadVersion(mid):
        right = mid
    else:
        left = mid + 1

return left
```

Remember:

```text
Bad  -> right = mid       # mid may be the answer
Good -> left = mid + 1    # answer must be later
```

## Lower Bound — First Position >= target

```python
left = 0
right = len(nums)

while left < right:
    mid = (left + right) // 2

    if nums[mid] < target:
        left = mid + 1
    else:
        right = mid

return left
```

The result can be `len(nums)` if every element is smaller than the target.

## Upper Bound — First Position > target

```python
left = 0
right = len(nums)

while left < right:
    mid = (left + right) // 2

    if nums[mid] <= target:
        left = mid + 1
    else:
        right = mid

return left
```

## First and Last Position of Element

```text
first position = lower_bound(target)
last position  = upper_bound(target) - 1
```

After finding the lower bound, check that it is in range and actually contains the target.

## Problems practiced

- First Bad Version
- Search Insert Position
- Find Smallest Letter Greater Than Target
- Find First and Last Position of Element in Sorted Array

### Find Smallest Letter Greater Than Target

Find the first letter strictly greater than `target`. If the boundary reaches the end, wrap around to index `0`.

Important comparison:

```python
if letters[mid] <= target:
    left = mid + 1
else:
    right = mid
```

### Search Insert Position

Find the first index where `nums[index] >= target`. That index is either the target's position or where it should be inserted.

---

# 5) Modified Binary Search

## Recognition

Use when the array is not fully sorted but still has exploitable structure: rotated order, duplicates, a peak, or a mountain shape.

## A) Search in Rotated Sorted Array — Distinct Values

At least one half around `mid` is sorted.

Reusable decision:

1. Check whether the left half is sorted.
2. If it is, check whether the target lies in that half's value range.
3. Search that half if it does; otherwise search the other half.
4. If the left half is not sorted, the right half is sorted; apply the same idea there.

```python
if nums[left] <= nums[mid]:
    # left half is sorted
    if nums[left] <= target < nums[mid]:
        right = mid - 1
    else:
        left = mid + 1
else:
    # right half is sorted
    if nums[mid] < target <= nums[right]:
        left = mid + 1
    else:
        right = mid - 1
```

## B) Search in Rotated Sorted Array II — Duplicates

Duplicates can make it impossible to identify the sorted side.

When:

```python
nums[left] == nums[mid] == nums[right]
```

Shrink both ends:

```python
left += 1
right -= 1
```

Otherwise use the sorted-half logic. Worst-case time can become `O(n)`.

## C) Find Minimum in Rotated Sorted Array

Compare `nums[mid]` with `nums[right]`.

```text
nums[mid] > nums[right]  -> minimum is right of mid
nums[mid] <= nums[right] -> minimum is at mid or left of it
```

```python
left = 0
right = len(nums) - 1

while left < right:
    mid = (left + right) // 2

    if nums[mid] > nums[right]:
        left = mid + 1
    else:
        right = mid

return nums[left]
```

## D) Find Peak Element

Compare the slope around `mid`.

```text
nums[mid] < nums[mid + 1] -> uphill to the right -> left = mid + 1
otherwise                 -> peak at mid or left -> right = mid
```

This searches a slope boundary, not an exact target.

## E) Find in Mountain Array

A mountain array increases to a peak, then decreases.

Reusable plan:

```text
1. Binary Search for the peak index.
2. Binary Search the increasing side.
3. If not found, Binary Search the decreasing side.
```

Ascending side uses normal comparisons; descending side reverses them.

## Problems practiced

- Search in Rotated Sorted Array
- Search in Rotated Sorted Array II
- Find Minimum in Rotated Sorted Array
- Find Peak Element
- Find in Mountain Array

---

# 6) Binary Search on Answer

## Recognition

The problem asks for a minimum or maximum possible answer, such as speed, capacity, or days.

Ask:

> For a candidate answer `x`, can I write a yes/no function that checks whether `x` is feasible?

If feasibility is monotonic, Binary Search the answer range, not an array index.

## A) Minimize a Feasible Answer — First True

```text
candidate:  1  2  3  4  5  6  7
feasible:   F  F  F  T  T  T  T
                    ↑
                 first true
```

```python
while left < right:
    mid = (left + right) // 2

    if feasible(mid):
        right = mid
    else:
        left = mid + 1

return left
```

## B) Maximize a Feasible Answer — Last True

```text
candidate:  1  2  3  4  5  6  7
feasible:   T  T  T  T  F  F  F
                 ↑
              last true
```

Use an upper midpoint so the loop makes progress:

```python
while left < right:
    mid = (left + right + 1) // 2

    if feasible(mid):
        left = mid
    else:
        right = mid - 1

return left
```

## Feasibility Function

The check asks:

```text
Can this candidate answer satisfy all constraints?
```

The check may use another technique (often Greedy). Binary Search chooses a candidate; the check validates it. Learn the component patterns separately before treating a combined problem as mastered.

---

# 7) Problems Practiced — Binary Search on Answer

## A) Koko Eating Bananas

### Goal

Find the minimum eating speed `k` so Koko can finish all piles within `h` hours.

### Search space

```python
left = 1
right = max(piles)
```

### Feasibility idea

At speed `mid`, calculate total hours needed. Each pile takes a whole number of hours:

```python
hours += (pile + mid - 1) // mid
```

If total hours `<= h`, this speed works; try a smaller speed. Otherwise, increase the speed.

### Key lesson

Integer ceiling division:

```python
ceil(a / b) == (a + b - 1) // b
```

This avoids using floating-point `ceil()`.

## B) Capacity to Ship Packages Within D Days

### Goal

Find the minimum ship capacity that allows all packages to be shipped within `days`.

### Search space

```python
left = max(weights)
right = sum(weights)
```

Why?

- Capacity cannot be smaller than the heaviest package.
- Carrying all packages in one day is an upper bound.

### Feasibility idea

For a candidate capacity, scan packages in their original order and count how many days are needed without exceeding capacity.

If required days `<= days`, the capacity works; try a smaller capacity. Otherwise, increase capacity.

Note: the feasibility scan uses a greedy packing rule. The Binary Search pattern is the outer search.

## C) Minimum Number of Days to Make m Bouquets

### Goal

Find the minimum day on which at least `m` bouquets can be made. Each bouquet needs `k` adjacent bloomed flowers.

### Impossible case

```python
if m * k > len(bloomDay):
    return -1
```

There are not enough flowers even if all bloom.

### Search space

```python
left = min(bloomDay)
right = max(bloomDay)
```

### Bloom condition

A flower has bloomed by day `mid` when:

```python
bloom <= mid
```

### Feasibility idea

Scan from left to right:

- Count consecutive bloomed flowers.
- When the count reaches `k`, make one bouquet and reset the consecutive count to `0`.
- If a flower has not bloomed, reset the consecutive count to `0`.
- Check whether at least `m` bouquets can be made.

If feasible, search an earlier day. If not feasible, search a later day.

### Important coding lessons

- Finish scanning the entire array before updating Binary Search boundaries.
- Check `bouquets_made >= m`, not `> m`.
- With `while left < right`, when `mid` works and we want the minimum, use `right = mid` so `mid` remains a candidate.
- Return after the Binary Search loop.

---

# 8) Combined Problems — On Hold Until Greedy Is Learned

These are in the course sequence, but are intentionally not marked as completed.

## A) Magnetic Force Between Two Balls

- LeetCode 1552
- Binary Search on Answer + Greedy feasibility check
- Search goal: maximize the minimum distance.
- Greedy check: try placing balls while respecting a candidate minimum distance.

**Status: On hold.** Learn Greedy first, then return to this problem.

## B) Split Array Largest Sum

- LeetCode 410
- Binary Search on Answer + Greedy feasibility check
- Search goal: minimize the largest subarray sum.
- Feasibility check: determine whether the array can be split within the candidate maximum sum.

**Status: On hold.** Learn Greedy first, then return to this problem.

---

# 9) First-True vs Last-True — Quick Comparison

| Goal | Feasibility shape | Boundary update |
|---|---|---|
| Minimum feasible answer | `F F F T T T` | Works: `right = mid` |
| Maximum feasible answer | `T T T F F F` | Works: `left = mid` |

Templates:

### First True

```python
while left < right:
    mid = (left + right) // 2

    if feasible(mid):
        right = mid
    else:
        left = mid + 1

return left
```

### Last True

```python
while left < right:
    mid = (left + right + 1) // 2

    if feasible(mid):
        left = mid
    else:
        right = mid - 1

return left
```

---

# 10) Common Mistakes

## Mistake 1 — Mixing loop conditions

Exact search with a closed interval:

```python
while left <= right:
```

Boundary search:

```python
while left < right:
```

Choose based on what the interval represents.

## Mistake 2 — Removing `mid` when it may be the answer

For first-true search, keep a possible answer:

```python
right = mid
```

Do not use `mid - 1` with this particular `while left < right` template.

## Mistake 3 — Infinite loops in last-true search

If you use:

```python
left = mid
```

use an upper midpoint:

```python
mid = (left + right + 1) // 2
```

Otherwise `mid` may equal `left` and the interval may not shrink.

## Mistake 4 — Confusing indexes with answer values

In Binary Search on Answer, `left`, `right`, and `mid` represent possible answers, not array indexes.

## Mistake 5 — Updating Binary Search before feasibility checking is complete

Complete the full feasibility check first. Only then update `left` or `right`.

## Mistake 6 — Not checking impossible cases

For bouquets, check whether `m * k` exceeds the number of flowers before searching.

## Mistake 7 — Forgetting monotonicity

Binary Search on Answer needs a one-direction change:

```text
F F F T T T
```

or:

```text
T T T F F F
```

---

# 11) Quick Template Sheet

## Exact Target

```python
left, right = 0, len(nums) - 1

while left <= right:
    mid = (left + right) // 2

    if nums[mid] == target:
        return mid
    elif nums[mid] < target:
        left = mid + 1
    else:
        right = mid - 1

return -1
```

## First True

```python
while left < right:
    mid = (left + right) // 2

    if condition(mid):
        right = mid
    else:
        left = mid + 1

return left
```

## Last True

```python
while left < right:
    mid = (left + right + 1) // 2

    if condition(mid):
        left = mid
    else:
        right = mid - 1

return left
```

## Find Minimum in Rotated Sorted Array

```python
while left < right:
    mid = (left + right) // 2

    if nums[mid] > nums[right]:
        left = mid + 1
    else:
        right = mid

return nums[left]
```

---

# 12) Problems You Have Practiced

## Standard / Boundary Binary Search

- Binary Search
- First Bad Version
- Valid Perfect Square
- Search Insert Position
- Guess Number Higher or Lower
- Find Smallest Letter Greater Than Target
- Find First and Last Position of Element in Sorted Array

## Modified Binary Search

- Search in Rotated Sorted Array
- Search in Rotated Sorted Array II
- Find Minimum in Rotated Sorted Array
- Find Peak Element
- Find in Mountain Array

## Binary Search on Answer

- Koko Eating Bananas
- Capacity to Ship Packages Within D Days
- Minimum Number of Days to Make m Bouquets

## On hold — Greedy not learned yet

- Magnetic Force Between Two Balls
- Split Array Largest Sum

---

# 13) Next Course Sections

After completing Binary Search on Answer:

1. Binary Search on Matrix
   - Search a 2D Matrix
   - Search a 2D Matrix II
2. Binary Search Advanced
   - Random Pick with Weight
   - Find K Closest Elements
   - Heaters
   - Median of Two Sorted Arrays
   - Find K-th Smallest Pair Distance

Continue in the exact AlgoMaster order. Do not skip ahead.

---

# Final Mental Model

```text
1. Define the search space.
2. Define what left and right represent.
3. Identify what checking mid tells you.
4. Prove which half can be discarded.
5. Choose the correct loop and midpoint.
6. Make sure every iteration shrinks the search space.
7. For answer search, finish the feasibility check before updating boundaries.
```

**Learning rule:** Understand the pattern independently → practice its problems → master it → then solve combined-pattern problems after learning their other component patterns.
