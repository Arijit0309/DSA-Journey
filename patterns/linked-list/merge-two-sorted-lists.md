# Merge Two Sorted Lists — LC 21

**Core Ask:** Merge two already-sorted linked lists into one sorted linked list.
**Pattern:** Two pointer comparison + dummy node
**Difficulty:** Easy

---

## Brute Force
Idea: Collect all values from both lists into an array, sort it, rebuild a brand new list.

```python
def mergeTwoLists_bruteforce(l1, l2):
    values = []
    
    while l1:
        values.append(l1.val)
        l1 = l1.next
    while l2:
        values.append(l2.val)
        l2 = l2.next
    
    values.sort()
    
    dummy = ListNode()
    current = dummy
    for v in values:
        current.next = ListNode(v)
        current = current.next
    
    return dummy.next
```
Time: O((n+m) log(n+m)) — because of sort | Space: O(n+m)

---

## Optimal
Idea: Compare the two current nodes at each step, pick the smaller one,
attach it to result, move that pointer forward. Rewire in place — no new nodes.

```python
class Solution:
    def mergeTwoLists(self, l1, l2):
        dummy = ListNode()
        current = dummy
        
        while l1 is not None and l2 is not None:
            if l1.val <= l2.val:
                current.next = l1
                l1 = l1.next
            else:
                current.next = l2
                l2 = l2.next
            current = current.next
        
        current.next = l1 or l2
        
        return dummy.next
```
Time: O(n+m) | Space: O(1)

---

## Manual Trace — [1,1,3] and [2,3,4]

| Step | l1 | l2 | Pick | Result so far |
|---|---|---|---|---|
| 1 | 1 | 2 | l1 (1<2) | 1 |
| 2 | 1 | 2 | l1 (1<2) | 1→1 |
| 3 | 3 | 2 | l2 (2<3) | 1→1→2 |
| 4 | 3 | 3 | l1 (equal, <=) | 1→1→2→3 |
| 5 | None | 3→4 | attach remaining l2 | 1→1→2→3→3→4 |

---

## 🔑 Tricks Learned

**Trick 1 — Compare only the current heads, never look ahead:**
Since both lists are already sorted, the smallest possible next node
is always at the CURRENT position of either l1 or l2.
You never need to look further — just compare the two front nodes each step.

**Trick 2 — Loop condition is AND not OR:**
```python
while l1 is not None and l2 is not None:
```
Stop as soon as EITHER list runs out — no point comparing
when one side has nothing left to offer.
(Compare with Add Two Numbers which used OR — because there we
needed to keep going for the carry even after both lists ended.)

**Trick 3 — Attach remaining list in ONE line:**
```python
current.next = l1 or l2
```
When one list runs out, the other is already sorted —
no need to loop through remaining nodes one by one.
Just stitch the whole remaining list directly onto the end.
`l1 or l2` means: "use l1 if it's not None, otherwise use l2."

**Trick 4 — Dummy node (same as always):**
```python
dummy = ListNode()
current = dummy
# ... build via current.next ...
return dummy.next
```
Avoids special-case code for the first node.
Shows up in EVERY "build a linked list" problem.

**Trick 5 — Rewire pointers, don't build new nodes:**
The optimal solution never calls ListNode() to create new nodes.
It just changes where existing nodes' .next pointers point.
This is what gives O(1) space instead of O(n+m).

---

## The 5-Question Thinking Process (use for any linked list problem)
1. What do I need to TRACK? → variables / pointers
2. When do I KEEP GOING? → loop condition
3. What happens EACH STEP? → loop body
4. What happens at EDGE CASES? → after loop cleanup
5. What do I RETURN? → return statement

---

## Aha Moment
Both lists are already sorted — so at every step, the answer to
"what's the next smallest node?" is always sitting right at the
current front of one of the two lists. No sorting needed, no looking ahead.
Just always pick the smaller front node.

## Also Shows Up In
- Merge K Sorted Lists (LC 23) — same idea, but k lists instead of 2 (uses heap)
- Sort List (LC 148) — uses merge sort which calls this exact function
- Merge Sorted Array (LC 88) — same two-pointer merge idea but on arrays
