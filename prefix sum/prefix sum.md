# Prefix Sum — Revision Notes

## Sub-Pattern Map

```
Prefix Sum
|
+-- 1A. Basic Prefix Sum
|   +-- Range Sum Query          -> p[r+1] - p[l]
|   +-- Find Pivot Index         -> right = total - left - nums[i]
|   +-- Difference Array         -> +1 at start, -1 at end, running sum
|
+-- 1B. Prefix Sum + Hash Map
    +-- Subarray Sum == K        -> freq map, look for current_sum - k
    +-- Subarray Divisible by K  -> freq map, same remainder
    +-- Continuous Subarray Sum  -> first-index map, same remainder, len >= 2
    +-- Contiguous Array         -> first-index map, 0->-1, 1->+1, same prefix
```

### How to choose: freq map vs first-index map?

```
COUNT subarrays    -> freq map   (store frequency)
LONGEST subarray   -> first-index map (store earliest index, never overwrite)
TRUE/FALSE exists  -> first-index map (check index distance)
```

## 1. Prefix Sum Basics

Prefix Sum stores the cumulative sum up to each position.

Example:

```python
nums = [2, 4, 3, 5]
```

Prefix:

```text
[2, 6, 9, 14]
```

Core idea:

```text
prefix[i] = sum of nums[0 ... i]
```

With a leading zero:

```python
p = [0] * (len(nums) + 1)

for i in range(len(nums)):
    p[i + 1] = p[i] + nums[i]
```

Range sum:

```text
sum(left ... right) = p[right + 1] - p[left]
```

Complexity:
- Build: O(n)
- Query: O(1)
- Space: O(n)

---

## 2. Running Sum of 1D Array

Example:

```python
nums = [1, 2, 3, 4]
```

Output:

```text
[1, 3, 6, 10]
```

In-place solution pattern:

```python
class Solution(object):
    def runningSum(self, nums):
        for i in range(1, len(nums)):
            nums[i] += nums[i - 1]
        return nums
```

Key idea:

```text
current value + previous running sum
```

Complexity:
- Time: O(n)
- Extra Space: O(1)

---

## 3. Find Pivot Index

Find an index where:

```text
left sum == right sum
```

Example:

```python
nums = [1, 7, 3, 6, 5, 6]
```

Index `3`:

```text
left  = 1 + 7 + 3 = 11
right = 5 + 6 = 11
```

Pattern:

```python
total = sum(nums)
left_sum = 0

for i in range(len(nums)):
    right_sum = total - left_sum - nums[i]

    if left_sum == right_sum:
        return i

    left_sum += nums[i]

return -1
```

Important:

```text
right = total - left - nums[i]
```

The current element belongs to neither side.

Complexity:
- Time: O(n)
- Space: O(1)

---

## 4. Maximum Population Year

Each log is:

```text
[birth, death]
```

The person is counted in the birth year but not in the death year.

So:

```text
birth → +1
death → -1
```

Years are `1950` to `2050`, so:

```python
changes = [0] * 101
```

Map a year to an index:

```text
year - 1950
```

Update:

```python
changes[birth - 1950] += 1
changes[death - 1950] -= 1
```

Then use a running sum:

```python
population += changes[i]
```

`changes[i]` means:

```text
what changed in this year
```

`population` means:

```text
cumulative population after applying that change
```

Core code pattern:

```python
class Solution(object):
    def maximumPopulation(self, logs):
        changes = [0] * 101

        for birth, death in logs:
            changes[birth - 1950] += 1
            changes[death - 1950] -= 1

        population = 0
        max_population = 0
        answer = 1950

        for i in range(101):
            population += changes[i]

            if population > max_population:
                max_population = population
                answer = 1950 + i

        return answer
```

Why `>` instead of `>=`?

Because the required year is the earliest year with the maximum population.

---

# Prefix Sum + Hash Map

## Decision Table

| Problem                   | Map Key      | Map Value   | Initialize | Look Up           |
|---------------------------|--------------|-------------|------------|-------------------|
| Subarray Sum == K         | prefix_sum   | frequency   | `{0: 1}`   | `current_sum - k` |
| Subarray Divisible by K   | remainder    | frequency   | `{0: 1}`   | `current_sum % k` |
| Continuous Subarray Sum   | remainder    | first index | `{0: -1}`  | `current_sum % k` |
| Contiguous Array (0s, 1s) | balance sum  | first index | `{0: -1}`  | current balance   |

## 5. Subarray Sum Equals K

Goal:

```text
count contiguous subarrays whose sum == k
```

Example:

```python
nums = [1, 2, 3]
k = 3
```

Valid:

```text
[1, 2]
[3]
```

Answer:

```text
2
```

### Main formula

Let:

```text
current_sum = current prefix sum
earlier_sum = an earlier prefix sum
```

The subarray between them is:

```text
current_sum - earlier_sum
```

We need:

```text
current_sum - earlier_sum = k
```

Therefore:

```text
earlier_sum = current_sum - k
```

So look for:

```text
current_sum - k
```

### Hash Map

Store:

```text
prefix_sum → frequency
```

Initialize:

```python
prefix_count = {0: 1}
```

The initial `0` represents the prefix before the array starts.

Important line:

```python
count += prefix_count.get(current_sum - k, 0)
```

Meaning:

```text
How many previous times have I seen the required prefix sum?
Add that frequency to count.
```

Update frequency:

```python
prefix_count[current_sum] = prefix_count.get(current_sum, 0) + 1
```

Meaning:

```text
Increase the current prefix sum's frequency by 1.
```

Pattern:

```python
class Solution(object):
    def subarraySum(self, nums, k):
        count = 0
        prefix_count = {0: 1}
        current_sum = 0

        for num in nums:
            current_sum += num

            count += prefix_count.get(current_sum - k, 0)

            prefix_count[current_sum] = (
                prefix_count.get(current_sum, 0) + 1
            )

        return count
```

Complexity:
- Time: O(n)
- Space: O(n)

---

## 6. Subarray Sums Divisible by K

Goal:

```text
count subarrays whose sum % k == 0
```

### Main idea

Calculate:

```python
remainder = current_sum % k
```

If two prefix sums have the same remainder:

```text
prefix_A % k == prefix_B % k
```

then:

```text
(prefix_B - prefix_A) % k == 0
```

So the subarray between them is divisible by `k`.

### Hash Map

Store:

```text
remainder → frequency
```

Initialize:

```python
remainder_count = {0: 1}
```

Important:

```python
count += remainder_count.get(remainder, 0)
```

Then:

```python
remainder_count[remainder] = (
    remainder_count.get(remainder, 0) + 1
)
```

Pattern:

```python
class Solution(object):
    def subarraysDivByK(self, nums, k):
        remainder_count = {0: 1}
        current_sum = 0
        count = 0

        for num in nums:
            current_sum += num
            remainder = current_sum % k

            count += remainder_count.get(remainder, 0)

            remainder_count[remainder] = (
                remainder_count.get(remainder, 0) + 1
            )

        return count
```

Complexity:
- Time: O(n)
- Space: O(n)

---

## 7. Continuous Subarray Sum

Goal:

Find a contiguous subarray:
- length at least 2
- sum is a multiple of `k`

Example:

```python
nums = [23, 2, 4, 6, 7]
k = 6
```

`[2, 4]` has sum `6`, so the answer is `True`.

### Main idea

Again use:

```python
remainder = current_sum % k
```

Same remainder means the difference between the prefix sums is divisible by `k`.

But here we need only True/False.

Store:

```text
remainder → first index
```

Initialize:

```python
seen = {0: -1}
```

`-1` represents the position before the array starts.

Important condition:

```python
if i - seen[remainder] >= 2:
    return True
```

This checks the minimum subarray length.

Keep the first index:

```python
if remainder not in seen:
    seen[remainder] = i
```

Do not overwrite the first index.

Pattern:

```python
class Solution(object):
    def checkSubarraySum(self, nums, k):
        seen = {0: -1}
        current_sum = 0

        for i in range(len(nums)):
            current_sum += nums[i]
            remainder = current_sum % k

            if remainder in seen:
                if i - seen[remainder] >= 2:
                    return True
            else:
                seen[remainder] = i

        return False
```

Complexity:
- Time: O(n)
- Space: O(n)

---

## 8. Contiguous Array

Goal:

Find the maximum length contiguous subarray with equal numbers of `0` and `1`.

### Transformation

Convert:

```text
0 → -1
1 → +1
```

Then:

```text
equal 0s and 1s
        ↓
equal -1s and +1s
        ↓
subarray sum = 0
```

Example:

```text
[0, 1, 0, 1]

↓ convert

[-1, +1, -1, +1]

↓ running sum

[-1, 0, -1, 0]
```

### Hash Map

Store:

```text
prefix_sum → first index
```

Initialize:

```python
seen = {0: -1}
```

When the same prefix sum appears again:

```python
length = i - seen[prefix_sum]
```

Then maximize:

```python
if length > max_len:
    max_len = length
```

Keep only the first index of each prefix sum.

Pattern:

```python
class Solution(object):
    def findMaxLength(self, nums):
        seen = {0: -1}
        prefix_sum = 0
        max_len = 0

        for i in range(len(nums)):
            if nums[i] == 0:
                prefix_sum += -1
            else:
                prefix_sum += 1

            if prefix_sum in seen:
                length = i - seen[prefix_sum]

                if length > max_len:
                    max_len = length
            else:
                seen[prefix_sum] = i

        return max_len
```

Complexity:
- Time: O(n)
- Space: O(n)

---

# Quick Comparison

| Problem | State Stored | Map Stores |
|---|---|---|
| Range Sum Query | Prefix sum | Array |
| Running Sum | Running sum | Array |
| Find Pivot Index | Left/total sum | No map |
| Maximum Population | Population change | Difference array |
| Subarray Sum Equals K | Prefix sum | Frequency |
| Subarray Sums Divisible by K | Remainder | Frequency |
| Continuous Subarray Sum | Remainder | First index |
| Contiguous Array | Prefix/balance sum | First index |

---

# Most Important Mental Models

### Exact target

```text
subarray sum == k
→ look for current_sum - k
```

### Divisible by K

```text
same prefix remainder
→ divisible subarray
```

### Longest / minimum length condition

```text
remainder or prefix sum → first index
```

### Counting all subarrays

```text
remainder or prefix sum → frequency
```

### Contiguous Array

```text
0 → -1
1 → +1
same prefix sum → balanced subarray
```

---

# Common Mistakes

### Mistake 1

Using:

```python
{0, 1}
```

instead of:

```python
{0: 1}
```

The first is a set; the second is a dictionary.

### Mistake 2

Writing:

```python
map[state] = 1
```

every time.

That resets the frequency.

Use:

```python
map[state] = map.get(state, 0) + 1
```

### Mistake 3

Overwriting the first index.

For longest subarray problems, keep the earliest index.

### Mistake 4

Confusing:

```text
remainder
```

with:

```text
index
```

For Continuous Subarray Sum:

```python
i - seen[remainder] >= 2
```

The subtraction checks the subarray length.

### Mistake 5

For Range Sum with a leading zero:

```text
prefix[right + 1] - prefix[left]
```

---

# Prefix Sum Pattern Recognition

When you see:

```text
range sum
subarray sum
contiguous subarray
running sum
cumulative sum
divisible by K
equal number of 0s and 1s
count subarrays
maximum subarray length
```

Think:

```text
PREFIX SUM
    ↓
Track cumulative state
    ↓
Ask what previous state makes the condition true
    ↓
Use a Hash Map when needed
    ↓
Frequency OR First Index
```

---

# Completed AlgoMaster Prefix Sum Problems

## Basic Prefix Sum

- ✅ Range Sum Query — Immutable
- ✅ Running Sum of 1D Array
- ✅ Find Pivot Index
- ✅ Maximum Population Year

## Prefix Sum + Hash Map

- ✅ Subarray Sum Equals K
- ✅ Subarray Sums Divisible by K
- ✅ Continuous Subarray Sum
- ✅ Contiguous Array

---

# Final Cheat Sheet

```text
Range Sum
→ prefix[right + 1] - prefix[left]

Pivot Index
→ right = total - left - nums[i]

Maximum Population
→ birth +1
→ death -1
→ running sum

Subarray Sum = K
→ previous prefix = current_sum - k
→ frequency map

Divisible by K
→ same remainder
→ frequency map

Continuous Subarray Sum
→ same remainder
→ first index
→ length >= 2

Contiguous Array
→ 0 = -1
→ 1 = +1
→ same prefix sum
→ first index
→ maximize length
```
