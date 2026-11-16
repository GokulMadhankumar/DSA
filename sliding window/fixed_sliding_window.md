# Fixed Sliding Window — Revision Notes

## Sub-Pattern Map

```
Fixed Sliding Window  (window size = k, constant)
|
+-- Window Sum / Average
|   +-- max sum, min sum, max average of size k
|   +-- State: window_sum
|
+-- Window Count
|   +-- count elements satisfying a condition inside the window
|   +-- State: count
|
+-- Window Center Output
|   +-- result belongs to the CENTER of the window, not the start
|   +-- window size = 2*k + 1, center = r - k
|   +-- State: window_sum
|
+-- Distinct Window (Freq Map + len check)
|   +-- all elements in window must be unique
|   +-- Condition: len(freq) == k
|   +-- State: window_sum + freq map
|
+-- Frequency Map Equality (Anagram Match)
    +-- current window must match target pattern exactly
    +-- Condition: window == need
    +-- State: need map + window map
    +-- Starting index formula: start = r - k + 1
```

### Fixed vs Variable — Quick Rule

```
Window size given as k (constant)? -> Fixed Sliding Window
Longest / shortest / at most k?   -> Variable Sliding Window
```

## 1) When do I know it is a **fixed sliding window** problem?

Use **fixed sliding window** when:

* The subarray / substring size is **constant**
* The problem says:

  * “subarray of size `k`”
  * “substring of length `k`”
  * “take `k` elements”
  * “window size = `2*k+1`”
  * “every contiguous block of length `k`”

### Keywords

* subarray of size `k`
* substring of length `k`
* average of every window
* maximum/minimum sum of size `k`
* count vowels in substring of length `k`
* every radius `k`
* every permutation/anagram window of size `len(p)`

---

# 2) Core Idea

Instead of recomputing every window from scratch:

```python
sum(nums[i:i+k])
```

for each position, we:

1. build the **first window**
2. store its information
3. slide by **1**
4. remove left element
5. add right element

That gives **O(n)** instead of **O(n*k)**.

---

# 3) Master Formula

If the current window size is `k` and the right pointer is at `r`, then:

```python
left = r - k
```

The element leaving is:

```python
nums[r-k]
```

The new element entering is:

```python
nums[r]
```

---

# 4) Generic Fixed Sliding Window Template

## A) For sum-based problems

```python
window_sum = sum(arr[:k])
answer = ...

# use first window
# answer = ...

for r in range(k, len(arr)):
    window_sum -= arr[r-k]   # remove left
    window_sum += arr[r]     # add right

    # update answer using current window_sum
```

---

## B) For count-based / condition-based problems

```python
count = information from first window

# process first window

for r in range(k, len(arr)):
    # remove arr[r-k]
    # add arr[r]

    # update answer
```

---

## C) For frequency-map based problems

```python
freq = {}

# build first window
for i in range(k):
    freq[arr[i]] = freq.get(arr[i], 0) + 1

# check first window

for r in range(k, len(arr)):
    left = arr[r-k]
    right = arr[r]

    # remove left
    freq[left] -= 1
    if freq[left] == 0:
        del freq[left]

    # add right
    freq[right] = freq.get(right, 0) + 1

    # check current window
```

---

# 5) Fixed Sliding Window Problem Types You Learned

---

# Pattern 1 — **Window Sum**

## Example: Maximum Average Subarray I

### Goal

Find max sum / max average among all windows of size `k`.

### Idea

* Compute first window sum
* Slide
* Remove left, add right
* Track maximum

## Template

```python
window_sum = sum(nums[:k])
maxi = window_sum

for r in range(k, len(nums)):
    window_sum -= nums[r-k]
    window_sum += nums[r]
    maxi = max(maxi, window_sum)
```

### Use for

* maximum sum of size `k`
* minimum sum of size `k`
* average of every fixed window
* count of something that can be updated in O(1)

---

# Pattern 2 — **Window Count**

## Example: Maximum Number of Vowels in a Substring of Given Length

### Goal

Track how many characters satisfying a condition are inside the current window.

### Example condition

```python
ch in "aeiou"
```

### Idea

* Count vowels in first window
* When sliding:

  * if left char is vowel → decrement
  * if right char is vowel → increment

## Template

```python
count = 0

for i in range(k):
    if s[i] in "aeiou":
        count += 1

maxi = count

for r in range(k, len(s)):
    if s[r-k] in "aeiou":
        count -= 1
    if s[r] in "aeiou":
        count += 1
    maxi = max(maxi, count)
```

### Important intuition

If a vowel leaves the window, that vowel is **no longer inside the current window**, so we reduce the count.

---

# Pattern 3 — **Window Center Output**

## Example: K Radius Subarray Averages

### Problem idea

For every index `i`, take:

* `k` elements left
* current element
* `k` elements right

So window size is:

```python
2*k + 1
```

### Why?

Because:

```python
left = k
current = 1
right = k

total = k + 1 + k = 2*k + 1
```

### Important difference

This is still a fixed sliding window, but the answer belongs to the **center** of the window, not to the window start.

### Template idea

```python
window = 2*k + 1
window_sum = sum(nums[:window])

ans = [-1] * n
ans[k] = window_sum // window

for r in range(window, n):
    window_sum -= nums[r-window]
    window_sum += nums[r]

    center = r - k
    ans[center] = window_sum // window
```

### Key formula

If the current window ends at `r`, then:

```python
center = r - k
```

---

# Pattern 4 — **Window Sum + Frequency Map + Distinct Condition**

## Example: Maximum Sum of Distinct Subarrays With Length K

### Goal

Among windows of size `k`, find the maximum sum where **all elements are distinct**.

### Extra tool

Use a frequency map:

```python
freq[value] = count in current window
```

### Distinct check

If all elements in the window are distinct, then:

```python
len(freq) == k
```

Why?

Because:

* window size = `k`
* if every element appears once, there must be `k` unique keys

### Template

```python
freq = {}
window_sum = 0

for i in range(k):
    window_sum += nums[i]
    freq[nums[i]] = freq.get(nums[i], 0) + 1

maxi = window_sum if len(freq) == k else 0

for r in range(k, len(nums)):
    left = nums[r-k]
    right = nums[r]

    window_sum -= left
    freq[left] -= 1
    if freq[left] == 0:
        del freq[left]

    window_sum += right
    freq[right] = freq.get(right, 0) + 1

    if len(freq) == k:
        maxi = max(maxi, window_sum)
```

---

# Pattern 5 — **Frequency Map Equality**

## Example 1: Find All Anagrams in a String

## Example 2: Permutation in String

These are very important.

---

# 5A) Find All Anagrams in a String

## Goal

Find all starting indices in `s` where a substring is an anagram of `p`.

### Window size

```python
k = len(p)
```

### Maps used

1. `need` → frequency of characters in `p`
2. `window` → frequency of current window in `s`

### Condition

If current window is an anagram of `p`, then:

```python
window == need
```

### Template

```python
need = {}
window = {}
k = len(p)

for ch in p:
    need[ch] = need.get(ch, 0) + 1

for i in range(k):
    window[s[i]] = window.get(s[i], 0) + 1

ans = []
if window == need:
    ans.append(0)

for r in range(k, len(s)):
    left = s[r-k]
    right = s[r]

    window[left] -= 1
    if window[left] == 0:
        del window[left]

    window[right] = window.get(right, 0) + 1

    if window == need:
        ans.append(r-k+1)
```

### Starting index formula

If the current window ends at `r`, then its start is:

```python
start = r - k + 1
```

---

# 5B) Permutation in String

## Goal

Return `True` if **any** substring of `s2` is a permutation of `s1`.

This is almost the same as **Find All Anagrams**.

### Difference

Instead of storing all indices, the moment a match is found:

```python
return True
```

If no window matches:

```python
return False
```

### Template

```python
need = {}
window = {}
k = len(s1)

if k > len(s2):
    return False

for ch in s1:
    need[ch] = need.get(ch, 0) + 1

for i in range(k):
    window[s2[i]] = window.get(s2[i], 0) + 1

if window == need:
    return True

for r in range(k, len(s2)):
    left = s2[r-k]
    right = s2[r]

    window[left] -= 1
    if window[left] == 0:
        del window[left]

    window[right] = window.get(right, 0) + 1

    if window == need:
        return True

return False
```

---

# 6) How to Identify Which Fixed-Window Template to Use

Use this checklist.

---

## A) Does the problem ask for sum / average / max sum?

Use **window sum**.

Examples:

* maximum average
* maximum sum of size `k`
* minimum sum of size `k`

---

## B) Does the problem ask “how many characters/elements satisfying a condition are in the window?”

Use **window count**.

Examples:

* vowels count
* number of zeros / ones
* number of elements satisfying some condition

---

## C) Does the problem ask for a result at the center of the window?

Use **window center output**.

Example:

* K Radius Subarray Averages

---

## D) Does the problem ask for distinctness / duplicates in a fixed window?

Use **frequency map + distinct check**.

Condition often becomes:

```python
len(freq) == k
```

---

## E) Does the problem ask whether the current window matches another pattern/string exactly?

Use **frequency map equality**.

Condition becomes:

```python
window == need
```

Examples:

* Find All Anagrams
* Permutation in String

---

# 7) Common Mistakes in Fixed Sliding Window

---

## Mistake 1 — Forgetting to build the first window

Always process the first window **before** sliding.

---

## Mistake 2 — Wrong element removed

When window ends at `r`, the leaving element is:

```python
arr[r-k]
```

Not `arr[r-1]`.

---

## Mistake 3 — Overwriting index variable

Bad:

```python
for r in range(k, len(s)):
    r = s[r]
```

Now `r` is no longer an index.

Better:

```python
for r in range(k, len(s)):
    left = s[r-k]
    right = s[r]
```

---

## Mistake 4 — Not deleting zero-frequency keys

If using frequency map:

```python
freq[x] -= 1
if freq[x] == 0:
    del freq[x]
```

If you don’t delete zero-count keys, comparisons like:

```python
window == need
```

or checks like:

```python
len(freq) == k
```

can become wrong.

---

## Mistake 5 — Forgetting edge cases

Common fixed-window edge cases:

```python
if k > len(arr):
    ...
```

```python
if k == 0:
    ...
```

For K Radius Averages:

```python
window = 2*k + 1
if window > n:
    return [-1] * n
```

---

# 8) Fixed Sliding Window Mental Model

Whenever you see a fixed-window problem, think:

```text
Build first window
↓
Store sum / count / map
↓
Slide one step
↓
Remove left
Add right
↓
Update answer / compare condition
```

---

# 9) Fixed Sliding Window Master Skeleton

```python
k = window_size

# Step 1: Build first window
state = ...

for i in range(k):
    # update state with arr[i]

# Step 2: Use first window
# update answer

# Step 3: Slide
for r in range(k, len(arr)):
    left = arr[r-k]
    right = arr[r]

    # remove left from state
    # add right to state

    # update answer / check condition
```

---

# 10) Problems You’ve Learned Under Fixed Sliding Window

## Sum / Count

* Maximum Average Subarray I
* Maximum Number of Vowels in a Substring of Given Length

## Special fixed window

* K Radius Subarray Averages

## Fixed window + frequency map

* Maximum Sum of Distinct Subarrays With Length K
* Find All Anagrams in a String
* Permutation in String

---

# 11) Quick Revision Table

| Pattern         | State Maintained    | Condition / Output          |
| --------------- | ------------------- | --------------------------- |
| Window Sum      | `window_sum`        | max/min/sum/average         |
| Window Count    | `count`             | max count / condition count |
| Center Output   | `window_sum`        | answer stored at center     |
| Distinct Window | `window_sum + freq` | `len(freq) == k`            |
| Anagram Match   | `need + window`     | `window == need`            |

---

# 12) What to Remember Before Moving to Variable Sliding Window

You should be comfortable with:

* building the first window
* removing left / adding right
* updating sum in O(1)
* updating count in O(1)
* using a frequency map for the current window
* deleting keys when count becomes 0
* comparing current window with a target pattern
* understanding when answer belongs to window start vs center

If these feel natural, you’re ready for **variable sliding window**.
