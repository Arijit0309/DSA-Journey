# Add Two Numbers — LC 2

**Core Ask:** Add two numbers stored as linked lists with digits in REVERSE order.
**Pattern:** Linked list traversal + carry simulation
**Difficulty:** Medium

---

## Brute Force
Idea: Convert both lists into real integers, add normally, convert sum back to a list.

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def addTwoNumbers_bruteforce(l1, l2):
    num1 = 0
    multiplier = 1
    node = l1
    while node is not None:
        num1 += node.val * multiplier
        multiplier *= 10
        node = node.next
    
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

---

## Optimal
Idea: Simulate digit-by-digit addition with carry, like doing it by hand on paper.

```python
def addTwoNumbers_optimal(l1, l2):
    dummy = ListNode()
    current = dummy
    carry = 0
    
    while l1 is not None or l2 is not None or carry:
        val1 = l1.val if l1 is not None else 0
        val2 = l2.val if l2 is not None else 0
        
        total = val1 + val2 + carry
        carry = total // 10
        digit = total % 10
        
        current.next = ListNode(digit)
        current = current.next
        
        if l1 is not None:
            l1 = l1.next
        if l2 is not None:
            l2 = l2.next
    
    return dummy.next
```
Time: O(max(n,m)) | Space: O(max(n,m))

---

## 🔑 Tricks Learned

**Trick 1 — Rebuild number from reversed digits (multiply + add):**
```python
num += digit * multiplier
multiplier *= 10
```
List [2,4,3] → 2×1 + 4×10 + 3×100 = 342
Works because reversed list = ones digit first = 10^0, 10^1, 10^2...

**Trick 2 — Decompose number into digits (% and //):**
```python
digit = total % 10    # peels off last digit
total //= 10          # removes last digit
```
Example: 807 → digits 7, 0, 8 (comes out reversed — perfect for this problem!)
Trick 1 and Trick 2 are OPPOSITES:
- List → Number: multiply + add (reconstruct)
- Number → List: % and // (decompose)

**Trick 3 — Carry propagation:**
```python
carry = total // 10   # 0 or 1
digit = total % 10    # what to store in this node
```
Same % and // from Trick 2, but applied per step during simulation.

**Trick 4 — Handle unequal length lists cleanly:**
```python
val = node.val if node is not None else 0
```
Once a list runs out, treat missing digits as 0. No separate if/else needed.

**Trick 5 — Loop needs 3 conditions OR'd together:**
```python
while l1 or l2 or carry:
```
carry can create ONE extra digit after both lists are exhausted.
Example: 99 + 1 = 100 — that extra "1" comes purely from carry.

**Trick 6 — Dummy node:**
```python
dummy = ListNode()
current = dummy
# build list via current.next = new_node, current = current.next
return dummy.next   # skip placeholder, return real head
```
Avoids special-case code for the very first node.

---

## Aha Moment
Simulating digit-by-digit with carry avoids ever creating a huge integer.
Works in ALL languages — unlike brute force which relies on Python's big-int support.

## Also Shows Up In
- % and // digit tricks → Reverse Integer (LC 7), Palindrome Number (LC 9)
- Carry propagation → Add Binary (LC 67), Multiply Strings (LC 43), Plus One (LC 66)
- Dummy node → Merge Two Sorted Lists (LC 21), Remove Nth Node From End (LC 19)
