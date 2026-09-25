# Two Pointers — Revision Notes

## 1) When do I know it is a Two Pointers problem?

Use **Two Pointers** when two indices can move through the same array or string while maintaining a useful relationship.

Common clues:

* Remove / compress / keep elements in-place
* Compare elements from two ends
* Search for a pair or triplet
* Sorted array
* Palindrome
* Maximum area
* Trapping water
* O(1) extra-space in-place manipulation

### Main families

1. Same Direction — Writer / Reader
2. Opposite Direction — Left / Right
3. Fixed Pointer + Two Pointers — 3Sum
4. Opposite Direction + Running Boundaries — Trapping Rain Water

---

# 2) Core Idea

Instead of checking every possible pair with nested loops, move two pointers according to the structure of the problem.

Typical complexity:

```text
O(n)
```

For 3Sum:

```text
O(n²)
```

because one pointer is fixed and the other two scan the remaining array.

---

# 3) Master Templates

## A) Same Direction — Writer / Reader

```python
l = 0

for r in range(len(nums)):

    if useful:
        nums[l] = nums[r]
        l += 1

return l
```

Meaning:

```text
l = writer
r = reader
```

The first `l` positions contain the valid elements.

---

## B) Same Direction — Sorted Duplicate Removal

```python
l = 0

for r in range(1, len(nums)):

    if nums[l] != nums[r]:
        l += 1
        nums[l] = nums[r]

return l + 1
```

Here `l` is the index of the last valid element, so the count is `l + 1`.

---

## C) Opposite Direction

```python
l = 0
r = len(arr) - 1

while l < r:

    # inspect arr[l] and arr[r]

    if condition:
        l += 1
    else:
        r -= 1
```

Visual:

```text
l →              ← r
```

---

# 4) Pattern 1 — Same Direction: Remove Duplicates

## Goal

Given a sorted array, keep each value only once.

### Key observation

Because the array is sorted, duplicates are adjacent:

```text
[1,1,2,2,3,3,4]
```

### Pointer roles

```text
l = last unique position
r = scanning pointer
```

### Rule

Duplicate:

```python
nums[l] == nums[r]
```

Move `r`.

New unique value:

```python
nums[l] != nums[r]
```

Do:

```python
l += 1
nums[l] = nums[r]
```

### Template

```python
l = 0

for r in range(1, len(nums)):

    if nums[l] != nums[r]:
        l += 1
        nums[l] = nums[r]

return l + 1
```

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 5) Pattern 2 — Same Direction: Remove Element

## Goal

Remove all occurrences of `val` in-place.

### Useful condition

Keep the element when:

```python
nums[r] != val
```

### Template

```python
l = 0

for r in range(len(nums)):

    if nums[r] != val:
        nums[l] = nums[r]
        l += 1

return l
```

### Important

Here `l` means the **number of kept elements**.

So return:

```python
l
```

not `l + 1`.

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 6) Pattern 3 — Same Direction: Move Zeroes

## Goal

Move all zeroes to the end while maintaining non-zero order.

Example:

```text
[0,1,0,3,12]
```

becomes:

```text
[1,3,12,0,0]
```

### Useful condition

```python
nums[r] != 0
```

### First pass

```python
l = 0

for r in range(len(nums)):

    if nums[r] != 0:
        nums[l] = nums[r]
        l += 1
```

### Fill remaining positions

```python
while l < len(nums):
    nums[l] = 0
    l += 1
```

### Mental model

```text
+------------------+------------------+
| valid / non-zero | remaining area   |
+------------------+------------------+
                   ^
                   l
```

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 7) Same Direction Mental Model

For these problems:

```text
r = Reader
l = Writer
```

`r` scans every element.

`l` moves only when an element should be kept.

General template:

```python
l = 0

for r in range(len(nums)):

    if useful:
        nums[l] = nums[r]
        l += 1
```

Only `useful` changes.

Examples:

```text
Remove Duplicates → new unique value
Remove Element    → nums[r] != val
Move Zeroes       → nums[r] != 0
```

---

# 8) Pattern 4 — Opposite Direction: Container With Most Water

## Goal

Choose two lines that create the maximum container area.

### Area formula

```python
area = (r - l) * min(height[l], height[r])
```

### Why `min()`?

The shorter wall limits the water.

### Pointer rule

If:

```python
height[l] < height[r]:
```

move:

```python
l += 1
```

Otherwise:

```python
r -= 1
```

### Template

```python
l = 0
r = len(height) - 1
ans = 0

while l < r:

    area = (r - l) * min(height[l], height[r])
    ans = max(ans, area)

    if height[l] < height[r]:
        l += 1
    else:
        r -= 1

return ans
```

### Golden rule

```text
Move the shorter wall.
```

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 9) Pattern 5 — Opposite Direction: Two Sum II

## Goal

Find two numbers in a sorted array whose sum equals `target`.

Example:

```text
numbers = [2,7,11,15]
target = 9
```

### Template

```python
l = 0
r = len(numbers) - 1

while l < r:

    s = numbers[l] + numbers[r]

    if s == target:
        return [l + 1, r + 1]

    elif s < target:
        l += 1

    else:
        r -= 1

return [-1, -1]
```

### Pointer rule

```text
sum < target
→ need bigger sum
→ l++

sum > target
→ need smaller sum
→ r--
```

### Why it works

The array is sorted:

```text
l++ → value increases → sum increases
r-- → value decreases → sum decreases
```

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 10) Pattern 6 — Opposite Direction: Valid Palindrome

## Goal

Check whether a string is a palindrome after:

* ignoring non-alphanumeric characters
* ignoring case

Example:

```text
"A man, a plan, a canal: Panama"
```

Treat it as:

```text
amanaplanacanalpanama
```

### Pointer rules

```text
left invalid
→ l++

right invalid
→ r--

different characters
→ False

same characters
→ l++, r--
```

### Template

```python
l = 0
r = len(s) - 1

while l < r:

    if not s[l].isalnum():
        l += 1

    elif not s[r].isalnum():
        r -= 1

    elif s[l].lower() != s[r].lower():
        return False

    else:
        l += 1
        r -= 1

return True
```

### Important

Do not return `True` after one matching pair.

Continue until:

```python
l >= r
```

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 11) Pattern 7 — Fixed Pointer + Two Pointers: 3Sum

## Goal

Find all unique triplets whose sum is zero.

```text
a + b + c = 0
```

Example:

```text
nums = [-1,0,1,2,-1,-4]
```

Output:

```text
[[-1,-1,2],[-1,0,1]]
```

### Step 1 — Sort

```python
nums.sort()
```

Example:

```text
[-4,-1,-1,0,1,2]
```

### Step 2 — Fix `i`

For each `i`:

```text
i = fixed
l = i + 1
r = end
```

Then solve a Two Sum II style problem.

### Template

```python
nums.sort()
ans = []

for i in range(len(nums) - 2):

    if i > 0 and nums[i] == nums[i - 1]:
        continue

    l = i + 1
    r = len(nums) - 1

    while l < r:

        s = nums[i] + nums[l] + nums[r]

        if s == 0:
            ans.append([nums[i], nums[l], nums[r]])

            l += 1
            r -= 1

            while l < r and nums[l] == nums[l - 1]:
                l += 1

            while l < r and nums[r] == nums[r + 1]:
                r -= 1

        elif s < 0:
            l += 1

        else:
            r -= 1

return ans
```

### Pointer rule

```text
sum < 0  → l++
sum > 0  → r--
sum == 0 → save triplet
```

### Duplicate handling

Skip duplicate `i`:

```python
if i > 0 and nums[i] == nums[i - 1]:
    continue
```

After finding a triplet, skip duplicate `l` and `r` values.

### Complexity

```text
Sorting      : O(n log n)
Two pointers : O(n²)
Overall       : O(n²)
```

---

# 12) Pattern 8 — Opposite Direction + Left/Right Maximums

## Example

Trapping Rain Water

### Goal

Calculate total water trapped between bars.

Example:

```text
[0,1,0,2,1,0,1,3,2,1,2,1]
```

Answer:

```text
6
```

### Key formula

For a position:

```text
water = min(max_left, max_right) - height[i]
```

Instead of calculating both maximums repeatedly, maintain:

```python
leftMax
rightMax
```

### Correct pointer decision

Compare the **current boundary heights**:

```python
if height[l] <= height[r]:
    process left
else:
    process right
```

### Template

```python
l = 0
r = len(height) - 1

leftMax = 0
rightMax = 0
water = 0

while l < r:

    if height[l] <= height[r]:

        if height[l] >= leftMax:
            leftMax = height[l]
        else:
            water += leftMax - height[l]

        l += 1

    else:

        if height[r] >= rightMax:
            rightMax = height[r]
        else:
            water += rightMax - height[r]

        r -= 1

return water
```

### Important distinction

Container With Most Water:

```text
Compare current heights
Move shorter wall
```

Trapping Rain Water:

```text
Maintain leftMax/rightMax
Compare current boundary heights
Process the smaller current side
```

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# 13) How to Identify the Pattern

## Remove / compress / keep elements?

Think:

```text
Same Direction
Writer + Reader
```

Examples:

* Remove Duplicates
* Remove Element
* Move Zeroes

---

## Pair / sum / palindrome / both ends?

Think:

```text
Opposite Direction
Left + Right
```

Examples:

* Two Sum II
* Valid Palindrome
* Container With Most Water

---

## Triplets?

Think:

```text
Sort
+
Fix one
+
Two Pointers
```

Example:

* 3Sum

---

## Water trapped between bars?

Think:

```text
Two Pointers
+
leftMax/rightMax
```

Example:

* Trapping Rain Water

---

# 14) Common Mistakes

## Mistake 1 — Mixing pointer roles

Same direction:

```text
l = writer
r = reader
```

Opposite direction:

```text
l = left
r = right
```

---

## Mistake 2 — Wrong return for in-place filtering

Remove Duplicates:

```python
return l + 1
```

Remove Element:

```python
return l
```

Move Zeroes:

```python
# no return
```

---

## Mistake 3 — Moving both pointers without reason

Two Sum II:

```text
sum < target → l++
sum > target → r--
```

Container:

```text
shorter wall → move that pointer
```

---

## Mistake 4 — Forgetting sort in 3Sum

```python
nums.sort()
```

is required for the two-pointer sum logic and easy duplicate handling.

---

## Mistake 5 — Forgetting duplicate skipping in 3Sum

Need to skip:

* duplicate fixed values
* duplicate left values
* duplicate right values

---

## Mistake 6 — Wrong area width

Container With Most Water:

```python
r - l
```

not:

```python
r - 1
```

---

## Mistake 7 — Returning True too early in palindrome

One matching pair is not enough.

Continue comparing until the pointers meet.

---

# 15) Mental Models

## Same Direction

```text
[ Valid Section | Unknown Section ]
       ^
       l
                     ^
                     r
```

`r` reads.

`l` writes.

---

## Opposite Direction

```text
l →                  ← r
```

Pointers start at both ends and move inward.

---

## 3Sum

```text
i = fixed

l →              ← r
```

---

## Trapping Rain Water

```text
left boundary      right boundary
      ↓                   ↓
   leftMax             rightMax
```

Process the side whose current boundary is smaller.

---

# 16) Quick Revision Table

| Pattern | Pointer Style | Main Rule |
|---|---|---|
| Remove Duplicates | Writer + Reader | keep new unique |
| Remove Element | Writer + Reader | `nums[r] != val` |
| Move Zeroes | Writer + Reader | `nums[r] != 0` |
| Container With Most Water | Opposite | move shorter wall |
| Two Sum II | Opposite | sum `<` target → `l++` |
| Valid Palindrome | Opposite | skip / compare |
| 3Sum | Fixed + Opposite | sum `< 0` → `l++` |
| Trapping Rain Water | Opposite + maxes | process smaller current boundary |

---

# 17) Problems You Completed

## Same Direction

* Remove Duplicates from Sorted Array
* Remove Element
* Move Zeroes

## Opposite Direction

* Container With Most Water
* Two Sum II
* Valid Palindrome
* 3Sum
* Trapping Rain Water

---

# 18) Interview Checklist

Before coding, ask:

```text
1. What does each pointer represent?

2. Same direction or opposite direction?

3. What makes an element useful?

4. When should l move?

5. When should r move?

6. Do I need sorting?

7. Do I need duplicate skipping?

8. What exactly should I return?

9. What invariant is always true?

10. Can I reduce the brute-force approach?
```

---

# 19) Two Pointer Master Cheat Sheet

```text
REMOVE / COMPRESS
        ↓
Writer + Reader
        ↓
Same Direction

PAIR / SUM / PALINDROME
        ↓
Left + Right
        ↓
Opposite Direction

TRIPLETS
        ↓
Sort + Fix i + Two Pointers

CONTAINER
        ↓
Move shorter wall

TRAPPED WATER
        ↓
leftMax + rightMax
        ↓
Process smaller current boundary
```

---

# 20) Final Things to Remember

Do not memorize every solution.

Memorize **pointer meaning and movement rule**.

```text
Same Direction:
l = writer
r = reader
```

```text
Opposite Direction:
l = left
r = right
```

```text
3Sum:
i = fixed
l = left search
r = right search
```

Once the pointer roles and invariant are clear, the implementation becomes much easier.
