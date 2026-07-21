# Variable Sliding Window --- Revision Notes

## 1) When do I know it is a **variable sliding window** problem?

Use **variable sliding window** when:

-   The window size is **NOT fixed**
-   The problem asks for the **longest** or **smallest** valid window.
-   The window expands until a condition is met, then shrinks.

### Keywords

-   longest substring
-   longest subarray
-   shortest substring
-   minimum window
-   at most `k`
-   no duplicates
-   distinct characters
-   continuous subarray

------------------------------------------------------------------------

# 2) Core Idea

Instead of using a fixed window size:

1.  Expand the window (`r++`)
2.  Maintain the current window state
3.  If the window becomes invalid (or valid for minimum-window
    problems), shrink from the left.
4.  Repeat until the end.

Every element enters and leaves the window at most once.

Time Complexity: **O(n)**

------------------------------------------------------------------------

# 3) Master Formula

``` python
l = 0

for r in range(len(arr)):

    # Add right element

    while condition:
        # Remove left element
        l += 1

    # Update answer
```

The only thing that changes between problems is the **condition**.

------------------------------------------------------------------------

# 4) Generic Variable Sliding Window Templates

## A) Longest Window

``` python
l = 0

for r in range(len(arr)):

    # add right

    while invalid:
        # remove left
        l += 1

    ans = max(ans, r-l+1)
```

------------------------------------------------------------------------

## B) Minimum Window

``` python
l = 0

for r in range(len(arr)):

    # add right

    while valid:

        ans = min(ans, r-l+1)

        # remove left
        l += 1
```

------------------------------------------------------------------------

# 5) Variable Sliding Window Patterns

------------------------------------------------------------------------

# Pattern 1 --- Set Based Window

## Example

Longest Substring Without Repeating Characters

Maximum Erasure Value

### Goal

Maintain a window with **no duplicate elements**.

### Data Structure

``` python
seen = set()
```

### Template

``` python
while nums[r] in seen:
    seen.remove(nums[l])
    l += 1

seen.add(nums[r])

ans = max(ans, r-l+1)
```

------------------------------------------------------------------------

# Pattern 2 --- Frequency Map + At Most K Distinct

## Example

Fruit Into Baskets

### Goal

Allow at most `k` distinct elements.

### Condition

``` python
while len(freq) > k:
```

### Template

``` python
freq[x] = freq.get(x,0)+1

while len(freq) > k:
    freq[arr[l]] -= 1

    if freq[arr[l]] == 0:
        del freq[arr[l]]

    l += 1

ans = max(ans,r-l+1)
```

------------------------------------------------------------------------

# Pattern 3 --- Character Replacement

## Example

Longest Repeating Character Replacement

### Formula

``` python
(window_length - maxFreq) > k
```

### Template

``` python
freq[ch] = freq.get(ch,0)+1
maxFreq = max(maxFreq,freq[ch])

while (r-l+1)-maxFreq > k:
    freq[s[l]] -= 1
    l += 1

ans = max(ans,r-l+1)
```

------------------------------------------------------------------------

# Pattern 4 --- Running Sum

## Example

Minimum Size Subarray Sum

### Goal

Find the minimum length whose sum is at least target.

### Condition

``` python
while windowSum >= target:
```

### Template

``` python
windowSum += nums[r]

while windowSum >= target:

    ans = min(ans,r-l+1)

    windowSum -= nums[l]
    l += 1
```

------------------------------------------------------------------------

# Pattern 5 --- Need Map + Window Map

## Example

Minimum Window Substring

### Need

-   need map
-   window map
-   have
-   needCount

### Meaning

``` python
needCount = len(need)
```

``` python
have = satisfied unique characters
```

### Template

``` python
build need

needCount = len(need)

for r in range(len(s)):

    window[s[r]] = window.get(s[r],0)+1

    if s[r] in need and window[s[r]] == need[s[r]]:
        have += 1

    while have == needCount:

        update minimum answer

        window[s[l]] -= 1

        if s[l] in need and window[s[l]] < need[s[l]]:
            have -= 1

        l += 1
```

------------------------------------------------------------------------

# 6) How to Identify Which Template to Use

## A) Longest without duplicates

Use **Set**

Condition

``` python
while nums[r] in seen
```

------------------------------------------------------------------------

## B) At most K distinct

Use **Frequency Map**

Condition

``` python
while len(freq) > k
```

------------------------------------------------------------------------

## C) Character replacement

Use **Frequency Map + maxFreq**

Condition

``` python
while (r-l+1)-maxFreq > k
```

------------------------------------------------------------------------

## D) Minimum sum

Use **Running Sum**

Condition

``` python
while windowSum >= target
```

------------------------------------------------------------------------

## E) Minimum Window Substring

Use **Need + Window Maps**

Condition

``` python
while have == needCount
```

------------------------------------------------------------------------

# 7) Common Mistakes

## Mistake 1

Updating answer before shrinking/expanding correctly.

------------------------------------------------------------------------

## Mistake 2

Using `if` instead of `while` for shrinking.

------------------------------------------------------------------------

## Mistake 3

Forgetting to decrease frequency when removing left.

------------------------------------------------------------------------

## Mistake 4

Not deleting zero-frequency keys.

------------------------------------------------------------------------

## Mistake 5

Confusing **invalid** and **valid** shrink conditions.

Longest problems:

``` python
while invalid:
```

Minimum problems:

``` python
while valid:
```

------------------------------------------------------------------------

# 8) Mental Model

``` text
Expand right
      ↓
Update state
      ↓
Condition met?
      ↓
Shrink left
      ↓
Update answer
```

------------------------------------------------------------------------

# 9) Master Skeleton

``` python
l = 0

for r in range(len(arr)):

    # Expand
    add right

    # Shrink
    while condition:
        remove left
        l += 1

    # Update answer
```

------------------------------------------------------------------------

# 10) Problems You've Learned

-   Longest Substring Without Repeating Characters
-   Maximum Erasure Value
-   Fruit Into Baskets
-   Longest Repeating Character Replacement
-   Minimum Size Subarray Sum
-   Minimum Window Substring

------------------------------------------------------------------------

# 11) Quick Revision Table

  Pattern                 State            Condition
  ----------------------- ---------------- -----------------------
  No Duplicate            set              `nums[r] in seen`
  At Most K Distinct      freq map         `len(freq) > k`
  Character Replacement   freq + maxFreq   `(window-maxFreq)>k`
  Minimum Sum             running sum      `windowSum >= target`
  Minimum Window          need + window    `have == needCount`

------------------------------------------------------------------------

# 12) Before Moving On

You should be comfortable with:

-   Expanding and shrinking correctly
-   Knowing when to use `if` vs `while`
-   Using set, frequency map and running sum
-   Maintaining `have` and `needCount`
-   Choosing longest vs minimum window logic
