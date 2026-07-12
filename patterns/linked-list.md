# Linked List Pattern

## Add Two Numbers — LC 2

**Core Ask:** Add two numbers stored as linked lists with digits in REVERSE order.

**Pattern:** Linked list traversal + carry simulation

### Brute Force
Idea: Convert both lists into real integers, add them normally, convert the sum back into a list.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def addTwoNumbers_bruteforce(l1, l2):
    # Rebuild l1 into a real number
    num1 = 0
    multiplier = 1
    node = l1
    while node is not None:
        num1 += node.val * multiplier
        multiplier *= 10
        node = node.next
    
    # Rebuild l2 into a real number
    num2 = 0
    multiplier = 1
    node = l2
    while node is not None:
        num2 += node.val * multiplier
        multiplier *= 10
        node = node.next
    
    total = num1 + num2
    
    if total == 0:
        return ListNode(0)
    
    dummy = ListNode()
    current = dummy
    while total > 0:
        digit = total % 10
        current.next = ListNode(digit)
        current = current.next
        total //= 10
    
    return dummy.next
```
Time: O(n+m) | Space: O(max(n,m))

### 🔑 Trick 1 — REBUILD a number using MULTIPLICATION + ADDITION
When digits come in REVERSE order (ones digit first), rebuild the real number by
multiplying each digit by an increasing power of 10, then summing:

```
num = digit1 * 1 + digit2 * 10 + digit3 * 100 + ...
```

```python
num1 = 0
multiplier = 1
node = l1
while node is not None:
    num1 += node.val * multiplier   # ADD this digit's contribution
    multiplier *= 10                # MULTIPLY multiplier by 10 for next digit
    node = node.next
```

Example: list `[2,4,3]` (reversed digits of 342)
- digit 2 × 1   = 2
- digit 4 × 10  = 40
- digit 3 × 100 = 300
- sum = 342 ✅

This works BECAUSE the list is reversed — position 0 in the list = ones place (10^0), 
position 1 = tens place (10^1), and so on. Multiplying by increasing powers of 10 
lines up perfectly with walking the list front to back.

### 🔑 Trick 2 — DECOMPOSE a number using MODULO (%) + DIVISION (//)
To go the opposite direction (number → individual digits), repeatedly:
- `% 10` → peels off the LAST digit (the remainder when divided by 10)
- `// 10` → removes that last digit (integer division, drops the remainder)

```python
while total > 0:
    digit = total % 10     # get last digit
    # ... use digit ...
    total //= 10           # remove last digit
```

Example: `total = 807`
| step | total % 10 (digit) | total // 10 (new total) |
|---|---|---|
| 1 | 7 | 80 |
| 2 | 0 | 8 |
| 3 | 8 | 0 |

Digits extracted in order: 7, 0, 8 → naturally comes out in REVERSED order, 
which is exactly the format this problem wants!

**Summary of the two tricks (opposite operations):**
- List → Number: MULTIPLY by powers of 10, then ADD (reconstruction)
- Number → List: MOD by 10 to extract, DIVIDE by 10 to shrink (decomposition)

---

### Optimal
Idea: Simulate addition digit-by-digit with a carry, exactly like doing it by hand on paper. Never converts to a full integer.

```python
def addTwoNumbers_optimal(l1, l2):
    dummy = ListNode()
    current = dummy
    carry = 0
    
    while l1 is not None or l2 is not None or carry:
        val1 = l1.val if l1 is not None else 0
        val2 = l2.val if l2 is not None else 0
        
        total = val1 + val2 + carry
        carry = total // 10      # new carry (0 or 1)
        digit = total % 10       # digit to store in this node
        
        current.next = ListNode(digit)
        current = current.next
        
        if l1 is not None:
            l1 = l1.next
        if l2 is not None:
            l2 = l2.next
    
    return dummy.next
```
Time: O(max(n,m)) | Space: O(max(n,m))

### 🔑 Trick 3 — CARRY PROPAGATION (manual addition, simulated)
Same `% 10` / `// 10` idea as Trick 2, but applied PER STEP instead of at the end:

### 🔑 Trick 4 — Handling unequal-length lists WITHOUT extra if/else blocks
```python
val1 = l1.val if l1 is not None else 0
val2 = l2.val if l2 is not None else 0
```
Once a list runs out, just treat its "value" as 0 — no special-casing needed.

### 🔑 Trick 5 — Loop condition needs 3 conditions OR'd together
```python
while l1 is not None or l2 is not None or carry:
```
Needed because a leftover carry can create ONE EXTRA final digit even after 
BOTH lists are exhausted. Example: `99 + 1 = 100` — the extra "1" only exists 
because of carry, not because either list had more digits.

### 🔑 Trick 6 — DUMMY NODE (used in both brute force and optimal)
```python
dummy = ListNode()   # fake placeholder node, thrown away at the end
current = dummy

# ... build the list by doing current.next = new_node, current = current.next ...

return dummy.next    # skip the fake node, return the REAL head
```
Avoids messy special-case code for "is this the very first node I'm adding?" 
— you always attach to `current.next`, even for the first real node.

**Aha:** Simulating arithmetic digit-by-digit with carry avoids ever creating a huge 
number — this is what makes the optimal version work even in languages without 
big-integer support (unlike the brute force, which relies on Python's unlimited int size).

**Also shows up in:**
- `%` and `//` digit extraction → Reverse Integer, Palindrome Number, Add Binary
- Carry propagation → Add Binary, Multiply Strings, Plus One
- Dummy node → Merge Two Sorted Lists, Remove Nth Node From End, any "build a new linked list" problem
