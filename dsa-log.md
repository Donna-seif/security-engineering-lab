## Group Anagrams (day 2 — from memory)
Approach: hashmap keyed on a 26-element character-count tuple perword; words with identical counts are anagrams, so they land in the same list under `defaultdict(list)`.
**What I got right :** reproduced the core grouping logic from memory.
**What I learned:** dict keys must be hashable, so the count list has to be converted to a tuple before it can be used as a key. defaultdict(list) auto-creates missing keys, avoiding manual key checks.
res.values() returns a dict_values view, not an actual list —
LeetCode's checker rejects that, so it needs list(res.values()) to match the declared List[List[str]] return type.