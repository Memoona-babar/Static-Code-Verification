# Static Code Review Report

**Project:** Monster Battle Game  
**File Reviewed:** `game.cpp`  
**Review Type:** Static Code Verification

| Line No. | Defect Type | Why Is It Important? | Proposed Fix | Severity |
|---:|---|---|---|---|
| 27, 41–42, 119 | Array bounds / capacity | The array has capacity for 5 monsters, but `main()` spawns 6 and `spawnMonster()` does not check capacity. This can write outside the array. | Check `monsterCount < MAX_MONSTERS` before adding a monster; otherwise reject the spawn or use a dynamically sized container. | Critical |
| 47 | Assignment instead of comparison | `monsters[i]->id = id` changes the monster ID and the condition is based on the assigned value, so lookup results are incorrect. | Change `=` to `==`. | High |
| 54–56 | Null pointer / unsigned arithmetic | If an ID is not found, `m` is `nullptr` and `m->health` crashes. Also, unsigned arithmetic makes the negative-health check unreliable. | Check `if (m == nullptr)` first; use signed, validated damage and clamp health at zero. | Critical |
| 64–65 | Loop boundary / out-of-bounds access | `i <= monsterCount` accesses one element beyond the populated range and may read outside the array. | Change the condition to `i < monsterCount`. | High |
| 70–73 | Dangling reference | The function returns a reference to local variable `label`, which is destroyed when the function returns. Using it causes undefined behavior. | Return `std::string` by value instead of `std::string&`. Also check `m` before accessing it. | High |
| 77–80 | Uninitialized variable / division by zero | `sum` starts with an indeterminate value, making the average unreliable. If there are no monsters, division by zero occurs. | Initialize `double sum = 0.0`; return a defined result when `monsterCount == 0`. | High |
| 83–86 | Use-after-free / stale collection entry | `m` is deleted before `m->name` is printed. The pointer remains in the array and the count is not updated, so later access/destruction may use freed memory. | Save/print the name before deletion, then remove the monster from the array and update `monsterCount`. Handle `nullptr`. | Critical |
| 94–99 | Missing return path | `playerRank()` returns no value when `score <= 100`, but `main()` calls it with 50. | Add a default return, such as `return 3;`, if that matches the intended ranking rules. | High |
| 102–109 | Buffer safety / mismatched deallocation | `name` is a fixed 10-character buffer and input is not length-limited. `new char[10]` must be paired with `delete[]`, not `delete`. | Prefer `std::string name`; otherwise bound input and use `delete[] name`. | High |
| 121 | Memory leak | `bonus` is allocated with `new` but is never deleted or otherwise managed. | Remove the unused allocation or manage it with a smart pointer. | Medium |
| 133 | Shallow copy / double deletion | `Game copy = game` copies raw pointers. Both objects' destructors will try to delete the same monsters, leading to undefined behavior. | Implement the Rule of Three/Five correctly or use ownership-safe containers such as `std::vector<std::unique_ptr<Monster>>`. | Critical |
| 91 | Arithmetic precision | Both operands are integers, so `attack * armorPercent / 100` uses integer arithmetic before conversion to `double`; fractional damage is lost. | Use floating-point arithmetic, for example `attack * armorPercent / 100.0`, if fractional damage is intended. | Low |
| 31–33 | Initialization / object state | The pointer array is not explicitly initialized. Current loops depend on `monsterCount`, but other defects can make invalid entries accessible. | Initialize the pointer array to `nullptr` and maintain the count/array invariant. | Medium |
| 40–43 | Input/state validation | `spawnMonster()` does not validate capacity or input values such as health and attack. | Validate capacity and any required health/attack constraints before allocating. | High |

## Overall Result

**Status: Issues Found — Fixes Required**

The most serious findings are array overflow, null-pointer dereferencing, dangling references, use-after-free, and unsafe copying of `Game`. This report is based on static inspection of the supplied source code; it does not claim that the program was compiled or dynamically tested.
