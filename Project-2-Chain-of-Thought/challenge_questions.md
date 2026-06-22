# Challenge Questions — Chain-of-Thought Test Bank

This bank of questions was used to test the structured reasoning framework across four categories: mathematics, programming/debugging, logic puzzles, and technical interview questions.

---

## Category A — Mathematics

**A1.** A train leaves Station X traveling at 60 km/h. Forty-five minutes later, a second train leaves the same station on the same track traveling at 90 km/h. How far from Station X does the second train catch up to the first?

**A2.** A rectangular garden has a perimeter of 84 meters. Its length is 6 meters more than twice its width. What are the garden's dimensions, and what is its area?

**A3.** A shop offers a 20% discount on an item, and then applies an additional 10% off the discounted price at checkout. If the final price paid is $43.20, what was the original price?

**A4.** Three friends split a restaurant bill so that the second friend pays $8 more than the first, and the third pays twice as much as the first. If the total bill (before tip) is $152, how much does each friend pay?

---

## Category B — Programming / Debugging

**B1.** The following Python function is supposed to return the second-largest unique value in a list of integers. It crashes on the input `[5, 5, 5, 5]`. Identify the bug and fix it.

```python
def second_largest(nums):
    unique = list(set(nums))
    unique.sort(reverse=True)
    return unique[1]
```

**B2.** Trace through this function call by hand: `is_balanced("(()())")` — does it return `True` or `False`, and why?

```python
def is_balanced(s):
    count = 0
    for ch in s:
        if ch == '(':
            count += 1
        elif ch == ')':
            count -= 1
        if count < 0:
            return False
    return count == 0
```

**B3.** What is the time complexity of the following function, and can it be improved? Justify your answer.

```python
def has_duplicate(nums):
    for i in range(len(nums)):
        for j in range(len(nums)):
            if i != j and nums[i] == nums[j]:
                return True
    return False
```

---

## Category C — Logic Puzzles

**C1.** Four friends — Asha, Ben, Carla, and Dev — each own exactly one of four pets: a cat, a dog, a parrot, and a fish. Asha doesn't own the dog, the fish, or the cat. Ben owns either the cat or the parrot. Carla doesn't own the parrot. Dev owns the fish. Who owns each pet?

**C2.** A farmer needs to cross a river with a wolf, a goat, and a cabbage. The boat can carry the farmer plus only one item at a time. If left unsupervised together, the wolf will eat the goat, and the goat will eat the cabbage. How does the farmer get everything across safely?

**C3.** Three boxes are labeled "Apples," "Oranges," and "Mixed." All three labels are known to be wrong. You may draw exactly one fruit from exactly one box to determine the correct labeling of all three boxes. Which box do you draw from, and how do you deduce the rest?

---

## Category D — Technical Interview Questions

**D1.** Design an algorithm to determine whether a given string is a valid anagram of another string. State the time and space complexity of your approach, and consider whether case sensitivity and whitespace should be handled.

**D2.** You are given a sorted array of integers and a target value. Describe an algorithm to find the target's index in better than O(n) time, and explain what happens if the target is not present.

**D3.** Explain how you would detect a cycle in a singly linked list without using extra memory proportional to the list's length. What is the name of this technique, and what is its time complexity?

---

## Usage Note

Each question above was run through the structured reasoning framework defined in `reasoning_prompt.md`. Full reasoning traces (Understanding → Plan → Execution → Verification → Final Answer) for a representative subset of these questions are documented in `test_cases.md`, with aggregate results and failure analysis in `results.md`.
