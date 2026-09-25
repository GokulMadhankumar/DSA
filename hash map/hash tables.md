# Hash Tables — Revision Notes

## 1) When should I think about Hash Tables?

Think **Hash Table / Hash Map / Hash Set** when the problem needs fast lookup or needs to remember something about values seen earlier.

Common clues:
- Frequency / counting
- Duplicate detection
- Fast existence check
- Previous occurrence / last index
- Mapping one thing to another
- Grouping similar items
- Remaining/available frequency
- Prefix sum + lookup
- Previous state / remainder

### Main mental model

```text
HASH TABLE
    ↓
Remember information
    ↓
Look it up quickly
```

Typical average-time operations:

```text
insert   → O(1)
lookup   → O(1)
delete   → O(1)
```

---

# 2) Core Hash Table Patterns

```text
HASH TABLE
│
├── Frequency / Counting
├── Duplicate Detection
├── Membership / Fast Lookup
├── Last Seen / Index Mapping
├── Complement / Pair Lookup
├── Pattern Matching / Bidirectional Mapping
├── Grouping / Canonical Key
├── Consumption / Remaining Frequency
├── Prefix Sum + Hash Map
└── State / Remainder Hashing
```

Some problems combine Hash Tables with Greedy or Binary Search. Those are **held until the other pattern is learned separately**.

---

# 3) Pattern 1 — Frequency / Counting

Use a dictionary when the question asks:

- How many times?
- Frequency
- Count occurrences
- Compare counts
- Most/least frequent

### Core template

```python
freq = {}

for x in arr:
    freq[x] = freq.get(x, 0) + 1
```

Mental model:

```text
value → count
```

Example:

```text
[a, b, a, c, a, b]

a → 3
b → 2
c → 1
```

---

# 4) Frequency Counting — Bottleneck

## Maximum Number of Balloons

Target:

```text
balloon
```

Required:

```text
b → 1
a → 1
l → 2
o → 2
n → 1
```

For every required character:

```text
available // required
```

Then take the minimum.

### Pattern

```text
available frequency
        ↓
required frequency
        ↓
available // required
        ↓
MINIMUM
```

The smallest ratio is the **bottleneck**.

### Problem completed

- Maximum Number of Balloons

---

# 5) Frequency Counting — Pair Counting

## Number of Good Pairs

A good pair satisfies:

```text
i < j
nums[i] == nums[j]
```

Instead of checking every pair, count how many equal values appeared before.

### Core template

```python
freq = {}
ans = 0

for x in nums:
    ans += freq.get(x, 0)
    freq[x] = freq.get(x, 0) + 1
```

### Why?

If `x` has appeared `3` times, the current `x` creates `3` new pairs.

Example:

```text
[1, 1, 1]

first 1  → +0
second 1 → +1
third 1  → +2
```

Total:

```text
3
```

### Pattern

```text
current value
      ↓
previous occurrences
      ↓
add previous count
      ↓
increase frequency
```

### Problem completed

- Number of Good Pairs

---

# 6) Frequency Counting — Consumption

## Ransom Note

Count available characters in `magazine`:

```python
freq = {}

for ch in magazine:
    freq[ch] = freq.get(ch, 0) + 1
```

Then process `ransomNote` and consume one character at a time.

### Pattern

```text
count available
      ↓
consume one
      ↓
decrease count
      ↓
count becomes 0 → cannot use again
```

If the needed character is unavailable:

```text
False
```

### Difference from Balloon problem

```text
Balloons → compare final ratios
Ransom Note → consume counts while scanning
```

### Problem completed

- Ransom Note

---

# 7) Frequency Counting — Two Passes

## First Unique Character

Goal:

Return the index of the first character whose frequency is exactly `1`.

### Pass 1

```python
freq[ch] = freq.get(ch, 0) + 1
```

### Pass 2

Scan from left to right:

```python
for i in range(len(s)):
    if freq[s[i]] == 1:
        return i
```

Otherwise:

```python
return -1
```

### Why two passes?

Pass 1 tells us:

```text
Is this character unique?
```

Pass 2 preserves:

```text
original order
```

### Pattern

```text
Pass 1 → count
Pass 2 → first item satisfying condition
```

### Problem completed

- First Unique Character in a String

---

# 8) Pattern 2 — Duplicate Detection / Existence

## When to use

If the question is:

> Have I seen this value?

Use a set.

### Core template

```python
seen = set()

for x in nums:
    if x in seen:
        return True
    seen.add(x)

return False
```

Mental model:

```text
value → exists / does not exist
```

### Set vs Dictionary

```text
set
→ existence
```

```text
dict
→ existence + information
```

---

# 9) Missing / Existence Detection

## Find All Numbers Disappeared in an Array

Numbers should be from:

```text
1 ... n
```

Simple Hash Set approach:

```python
seen = set(nums)
```

Then check every expected number.

```text
Expected: 1 2 3 4 5 ...
Present:  values in seen
Missing:  expected values not in seen
```

### Pattern

```text
Expected values
      ↓
Store existing values
      ↓
Check expected range
      ↓
Missing → answer
```

### Interview follow-up

There is an O(1)-extra-space in-place marking version. Treat that as a separate array/index-marking technique.

### Problem completed

- Find All Numbers Disappeared in an Array

---

# 10) Pattern 3 — Last Seen / Index Mapping

Use a dictionary when you need:

- Previous index
- Last occurrence
- Distance between equal values
- Nearest previous occurrence

Mental model:

```text
value → last index
```

## Contains Duplicate II

Need:

```text
same value
+
index distance <= k
```

Store:

```python
last_seen[value] = index
```

When the value appears again:

```python
difference = current_index - last_seen[value]
```

Then:

```text
difference <= k
```

### Why `abs()` is unnecessary

Scanning is left-to-right, so:

```text
current index > previous index
```

Therefore:

```python
current - previous
```

is already non-negative.

### Problem completed

- Contains Duplicate II

---

# 11) Pattern 4 — Complement / Pair Lookup

Question:

> What value do I need to complete the target?

For Two Sum:

```text
current + needed = target
```

Therefore:

```python
needed = target - current
```

Hash Map stores previous values.

### Mental model

```text
current
+
needed
=
target
```

Typical structure:

```python
seen = {}

for i, x in enumerate(nums):
    needed = target - x

    if needed in seen:
        # pair found

    seen[x] = i
```

### Important distinction

Two Sum II was solved by Two Pointers because the array is sorted.

This is a different Hash Table pattern:

```text
lookup previous values
```

---

# 12) Pattern 5 — Pattern Matching / Bidirectional Mapping

Use mapping when one item must consistently correspond to another.

Examples:

```text
character → character
pattern character → word
```

The key question:

> If this item already has a mapping, does it still map to the same thing?

---

# 13) Isomorphic Strings

Need:

```text
s character → t character
```

and:

```text
t character → s character
```

### Rules

```text
same source → same target ✅
same source → different target ❌
different source → same target ❌
different source → different target ✅
```

### Why two maps?

To reject:

```text
a → x
b → x
```

Two different source characters cannot map to the same target character.

### Mental model

```text
source ↔ target
```

### Problem completed

- Isomorphic Strings

---

# 14) Word Pattern

Same one-to-one mapping idea, but now:

```text
pattern character → word
```

Need:

```text
pattern → word
word → pattern
```

Example:

```text
abba
dog cat cat dog
```

Mapping:

```text
a ↔ dog
b ↔ cat
```

Valid.

But:

```text
ab
dog dog
```

would create:

```text
a → dog
b → dog
```

Invalid.

### Important first check

```python
words = s.split()

if len(pattern) != len(words):
    return False
```

### Problem completed

- Word Pattern

---

# 15) Pattern 6 — Grouping / Canonical Key

## Group Anagrams

Words with the same character composition should go into one group.

Example:

```text
eat
tea
ate
```

Canonical signature:

```python
key = tuple(sorted(word))
```

Map:

```text
key → list of words
```

Example:

```text
('a','e','t') → ["eat","tea","ate"]
('a','n','t') → ["tan","nat"]
```

### Pattern

```text
item
 ↓
canonical key
 ↓
HashMap
 ↓
same key → same group
```

### Problem completed

- Group Anagrams

---

# 16) Grouping — Relative / Shift Pattern

## Group Shifted Strings

Strings belong to the same group if their relative character shifts are the same.

Example:

```text
abc
bcd
xyz
```

All have:

```text
+1, +1
```

So they share the same key.

### Difference calculation

```python
d = ord(word[i]) - ord(word[i-1])
d %= 26
```

Store the differences as a tuple.

Examples:

```text
abc  → (1,1)
bcd  → (1,1)
xyz  → (1,1)

acef → (2,2,1)
```

### Why `% 26`?

Alphabet wrap-around.

```text
z → a
```

Normal subtraction:

```text
ord('a') - ord('z') = -25
```

Normalized:

```text
-25 % 26 = 1
```

So a wrap-around shift is represented consistently.

### Single-character strings

No differences:

```text
a → ()
z → ()
```

Therefore all single-character strings share the same key.

### Pattern

```text
word
 ↓
relative differences
 ↓
normalize
 ↓
tuple key
 ↓
group
```

### Problem completed

- Group Shifted Strings

---

# 17) Pattern 7 — Consumption / Remaining Frequency

This appeared in:

- Ransom Note
- Intersection of Two Arrays

General pattern:

```text
Build frequency
       ↓
Scan another input
       ↓
Is value available?
       ↓
YES → use it
       ↓
decrease count
```

### Intersection idea

If:

```text
a = [1,2,2,3]
```

frequency:

```text
1 → 1
2 → 2
3 → 1
```

While scanning the other array:

```python
if value in freq and freq[value] > 0:
    ans.append(value)
    freq[value] -= 1
```

For the GFG version we practiced, duplicate results were not allowed, so after adding a common value we marked its count as unavailable:

```python
freq[value] = 0
```

### Problem completed

- Intersection of Two Arrays (platform-specific duplicate handling)

---

# 18) Pattern 8 — Prefix Sum + Hash Map

Important combination for array problems.

Useful when the question involves:

- Subarray sum
- Target sum
- Count of subarrays
- Longest subarray
- Previous cumulative state

Core idea:

```text
current prefix
-
previous prefix
=
subarray sum
```

Typical structure:

```python
count = {0: 1}
prefix = 0

for x in nums:
    prefix += x

    if prefix - target in count:
        ...

    count[prefix] = count.get(prefix, 0) + 1
```

### Mental model

```text
current prefix
      -
required previous prefix
      =
target
```

We will study Prefix Sum separately, then return to its Hash Table combinations.

---

# 19) Pattern 9 — State / Remainder Hashing

Sometimes the useful information is a state rather than the full value.

For example:

```python
state = prefix % k
```

If the same state appears again, the section between those positions has a useful property.

### Mental model

```text
current state
      ↓
Have I seen this state before?
      ↓
YES → use previous occurrence
```

Examples:

- Continuous Subarray Sum
- Contiguous Array

This will be revisited after Prefix Sum.

---

# 20) Encode and Decode TinyURL

Core pattern:

```text
long URL ↔ short ID
```

For decoding, store:

```text
short ID → original URL
```

A counter can generate unique IDs:

```text
0
1
2
3
...
```

Then:

```text
ID → URL
```

The LeetCode problem only requires:

```text
decode(encode(url)) == url
```

It does **not** require the same URL to always get the same short code.

### Pattern

```text
unique ID
 ↓
short code
 ↓
HashMap
 ↓
short code → URL
```

### Problem completed

- Encode and Decode TinyURL

---

# 21) Reorganize String — HOLD

This combines:

```text
Hash Map + Greedy
```

Core idea:

```text
frequency
↓
most frequent character
↓
feasibility check
↓
place at alternating positions
↓
fill remaining characters
```

Feasibility:

```text
maxFreq <= (n + 1) // 2
```

Construction uses:

```text
0, 2, 4, ...
then
1, 3, 5, ...
```

✅ Problem solved  
⏳ Revisit after learning Greedy independently.

---

# 22) Longest Consecutive Sequence

Use a set for fast membership.

### Main trick

Only start when:

```python
num - 1 not in seen
```

Then expand:

```python
while current + 1 in seen:
    current += 1
    count += 1
```

### Why?

For:

```text
[1,2,3,4]
```

only `1` should start the sequence.

If every number starts a scan, the same sequence is traversed repeatedly.

### Pattern

```text
set
 ↓
find sequence start
 ↓
expand forward
 ↓
track maximum
```

### Complexity

```text
Time  : O(n) average
Space : O(n)
```

### Problem completed

- Longest Consecutive Sequence

---

# 23) Held Combination Problems

We intentionally postpone these until the other pattern is learned.

### Hash Table + Greedy

```text
Split Array into Consecutive Subsequences
Minimum Deletions to Make Character Frequencies Unique
Reorganize String
```

### Hash Table + Binary Search

```text
Number of Matching Subsequences
```

The goal is to learn each core technique first, then solve combination problems.

---

# 24) Hash Table Pattern Recognition

## “How many?”

Use:

```text
Frequency Map
```

```text
value → count
```

---

## “Have I seen this?”

Use:

```text
Set
```

---

## “Where did I see this?”

Use:

```text
value → index
```

---

## “What value do I need?”

Use:

```text
Complement Lookup
```

```text
needed = target - current
```

---

## “What does this map to?”

Use:

```text
Mapping
```

For one-to-one:

```text
A → B
B → A
```

---

## “Which items belong together?”

Use:

```text
Canonical Key → Group
```

---

## “How many copies are available?”

Use:

```text
value → remaining count
```

---

## “Does this subarray/state depend on something previous?”

Use:

```text
Prefix/State → Hash Map
```

---

# 25) Set vs Dictionary

```text
SET
→ only need existence / uniqueness
```

```text
DICT
→ need count / index / mapping / group / state
```

Examples:

```text
Contains Duplicate
→ set

Number of Good Pairs
→ dict: value → count

Contains Duplicate II
→ dict: value → last index

Isomorphic Strings
→ dict: character → character

Group Anagrams
→ dict: signature → list
```

---

# 26) Frequency vs Distinct Count

Important distinction.

For:

```text
a a b c
```

Frequency:

```text
a → 2
b → 1
c → 1
```

Distinct count:

```text
3
```

Frequency answers:

```text
How many times?
```

Distinct count answers:

```text
How many different values?
```

This distinction was important in:

**Number of Good Ways to Split a String.**

---

# 27) `0 → 1` and `1 → 0` Distinct Count Logic

When maintaining distinct characters:

```text
0 → 1
    distinct +1
```

```text
1 → 2
    distinct unchanged
```

When removing:

```text
1 → 0
    distinct -1
```

```text
2 → 1
    distinct unchanged
```

This pattern is useful when maintaining two frequency maps.

---

# 28) Common Hash Table Mistakes

### Mistake 1 — Using the index instead of the value

Wrong when counting values:

```python
freq[i]
```

when `i` is an index.

Think:

```python
freq[nums[i]]
```

---

### Mistake 2 — Returning character instead of index

First Unique Character asks for:

```text
index
```

not the character.

---

### Mistake 3 — Using a set when duplicates matter

A set turns:

```text
[2,2]
```

into:

```text
{2}
```

So use a frequency map when multiplicity matters.

---

### Mistake 4 — Destroying frequency information

If one copy is consumed:

```text
2 → 1
```

not:

```text
2 → -1
```

unless the algorithm explicitly needs a marker.

---

### Mistake 5 — Forgetting reverse mapping

For one-to-one relationships:

```text
A → B
B → A
```

is the clearest safe approach.

---

### Mistake 6 — Using an unhashable grouping key

Wrong:

```python
key = sorted(word)
```

because that creates a list.

Use:

```python
key = tuple(sorted(word))
```

---

### Mistake 7 — Forgetting `% 26`

Shifted strings need wrap-around handling:

```python
d %= 26
```

---

### Mistake 8 — Expanding every number

For Longest Consecutive Sequence, only start from:

```python
num - 1 not in seen
```

---

# 29) Hash Table Master Templates

## Frequency

```python
freq = {}

for x in arr:
    freq[x] = freq.get(x, 0) + 1
```

---

## Duplicate / Set

```python
seen = set()

for x in arr:
    if x in seen:
        ...
    seen.add(x)
```

---

## Last Seen

```python
last_seen = {}

for i, x in enumerate(arr):

    if x in last_seen:
        # use previous index

    last_seen[x] = i
```

---

## Bidirectional Mapping

```python
m1 = {}
m2 = {}

for a, b in pairs:

    if a in m1 and m1[a] != b:
        return False

    if b in m2 and m2[b] != a:
        return False

    m1[a] = b
    m2[b] = a
```

---

## Grouping

```python
groups = {}

for item in items:

    key = canonical_key(item)

    if key not in groups:
        groups[key] = []

    groups[key].append(item)
```

---

## Prefix Sum + Hash

```python
seen = {0: 1}
prefix = 0

for x in nums:

    prefix += x

    if prefix - target in seen:
        ...

    seen[prefix] = seen.get(prefix, 0) + 1
```

---

# 30) Interview Recognition Flow

```text
HOW MANY?
    ↓
Frequency Map
```

```text
HAVE I SEEN THIS?
    ↓
Set
```

```text
WHERE WAS THIS SEEN?
    ↓
Last Seen Map
```

```text
WHAT DO I NEED?
    ↓
Complement Map
```

```text
WHAT DOES THIS MAP TO?
    ↓
Mapping
```

```text
WHICH ITEMS BELONG TOGETHER?
    ↓
Canonical Key
```

```text
HOW MANY COPIES ARE LEFT?
    ↓
Frequency + Consumption
```

```text
WHAT PREVIOUS PREFIX/STATE DO I NEED?
    ↓
Prefix/State + Hash Map
```

---

# 31) Problems Completed

## Frequency & Counting

```text
✅ Maximum Number of Balloons
✅ Number of Good Pairs
✅ Ransom Note
✅ First Unique Character
✅ Find All Numbers Disappeared in an Array
```

## Duplicate Detection

```text
✅ Contains Duplicate
✅ Contains Duplicate II
✅ Intersection of Two Arrays II / platform-specific variant
```

## Pattern Matching

```text
✅ Isomorphic Strings
✅ Word Pattern
✅ Group Anagrams
✅ Group Shifted Strings
```

## Advanced / Mapping

```text
✅ Encode and Decode TinyURL
✅ Reorganize String
✅ Longest Consecutive Sequence
✅ Number of Good Ways to Split a String
```

## Held

```text
⏸ Split Array into Consecutive Subsequences
   Hash Table + Greedy

⏸ Number of Matching Subsequences
   Hash Table + Binary Search

⏸ Minimum Deletions to Make Character Frequencies Unique
   Hash Table + Greedy
```

---

# 32) Final Mental Model

Don't memorize every individual solution.

Ask:

```text
COUNT?
→ dict: value → count

SEEN?
→ set

WHERE?
→ dict: value → index

NEED?
→ complement

MAP?
→ one-way / two-way mapping

GROUP?
→ canonical key

CONSUME?
→ remaining frequency

SUBARRAY / STATE?
→ prefix/state + Hash Map
```

The goal is:

```text
Problem
   ↓
Identify information needed
   ↓
Choose Hash Table structure
   ↓
Define what key means
   ↓
Define what value means
   ↓
Define when it updates
   ↓
Maintain the invariant
```

---

# 33) Final Interview Checklist

Before coding, ask:

```text
1. What information do I need to remember?

2. Set or dictionary?

3. If dictionary, what is the key?

4. What does the value store?
   count / index / mapping / group / state

5. When do I update it?

6. Do I need to consume/decrease counts?

7. Does order matter?

8. Do I need one-way or two-way mapping?

9. Does my key need to be hashable/canonical?

10. Is another pattern involved?
```

If another pattern is required, identify it instead of forcing the problem into a pure Hash Table solution.
