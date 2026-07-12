---

## Reverse Linked List — LC 206

**Core Ask:** Reverse a singly linked list in place, return the new head.

**Pattern:** Pointer rewiring (prev / curr / next_node triplet)

### Brute Force
Idea: Extract all values into an array, reverse it, rebuild a brand new list.

```python
def reverseList_bruteforce(head):
    values = []
    node = head
    while node is not None:
        values.append(node.val)
        node = node.next
    
    values.reverse()
    
    dummy = ListNode()
    current = dummy
    for v in values:
        current.next = ListNode(v)
        current = current.next
    
    return dummy.next
```
Time: O(n) | Space: O(n)

### Optimal
Idea: Rewire the existing nodes' `.next` pointers in place.
No extra array, no new list built.

```python
def reverseList(head):
    prev = None
    curr = head
    
    while curr is not None:
        next_node = curr.next   # 1. save before we lose it
        curr.next = prev        # 2. flip the arrow backward
        prev = curr              # 3. move prev forward
        curr = next_node          # 4. move curr forward
    
    return prev   # prev is now the new head
```
Time: O(n) | Space: O(1)

**🔑 The core 4-line block — memorize this:**
```python
next_node = curr.next   # save first, ALWAYS
curr.next = prev        # flip
prev = curr              # shift prev
curr = next_node          # shift curr
```
This exact block repeats for every node until curr hits None.
Order is critical — if you flip curr.next BEFORE saving it,
you permanently lose the rest of the list.

**🔑 Why next_node exists:**
curr.next is your ONLY way to reach the rest of the list.
The moment you overwrite it (line 2), that link is gone forever.
next_node saves it in line 1 so line 4 can still move forward.

**🔑 Why return prev (not curr):**
When the loop ends, curr = None (fell off the end).
prev is sitting on the LAST node we touched — which is
the new head of the reversed list.

**🔑 Important nuance — this optimization is about SPACE, not time:**
Both brute force and optimal are O(n) time.
The real win is O(1) space — rewiring in place vs building a new list.
Reminder: not every optimization is about speed, sometimes it's memory.

**Aha:** Save → flip → shift prev → shift curr. Repeat until curr is None.
prev ends up as the new head.

**Also shows up in:**
- Reverse Linked List II (LC 92) — reverse only a portion
- Palindrome Linked List (LC 234) — reverse second half, compare
- Reverse Nodes in k-Group (LC 25) — reverse in chunks
