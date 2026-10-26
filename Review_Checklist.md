# Static Verification Review Checklist

**Project:** Monster Battle Game  
**File Reviewed:** `game.cpp`  
**Review Type:** Static Code Verification

| Category | Result (Issues / OK) | Defect | Static Code Verification |
|---|---|---|---|
| Initialization | Issues | `sum` is not initialized in `averageAttack()` (line 77). `monsters` elements are not initialized in the constructor. | Initialize `sum = 0.0`; initialize the array entries or ensure each entry is assigned before use. |
| Pointers | Issues | `attackMonster()` and `getMonsterLabel()` can dereference a null pointer when an ID is not found. `defeatMonster()` uses a pointer after deleting its object. | Check whether `findMonster()` returns `nullptr` before using the pointer; print the name before deleting. |
| Arrays / Buffers | Issues | `spawnMonster()` can write beyond the `monsters` array. `readBoss()` reads into a fixed-size character buffer without a width limit. | Check `monsterCount < MAX_MONSTERS`; use `std::string` for the boss name or limit input safely. |
| Loops | Issues | `totalMonsterHealth()` uses `i <= monsterCount`, which accesses one element beyond the valid range. | Use `i < monsterCount`. |
| Arithmetic | Issues | `attackMonster()` uses an unsigned damage value in a negative check; `calculateDamage()` performs integer arithmetic; `averageAttack()` can divide by zero. | Validate non-negative damage, use suitable floating-point arithmetic if needed, and handle `monsterCount == 0`. |
| Control Flow | Issues | `playerRank()` has no return value when `score <= 100`. `attackMonster()` does not handle a missing monster. | Add a default return value and a null-pointer check. |
| Function Call | Issues | `readBoss()` passes a `char*` to a `std::string` parameter (conversion is possible), but then releases the buffer using `delete` instead of `delete[]`. Calls such as `attackMonster(99, 20)` and `getMonsterLabel(2)` expose missing-ID handling. | Use `delete[]` for arrays, preferably replace the buffer with `std::string`, and validate IDs in called functions. |
| Classes | Issues | `Game` owns raw pointers but has a compiler-generated shallow copy operation; `Game copy = game` can cause double deletion. `defeatMonster()` deletes a monster without removing it from the array/count. | Implement safe copy/move behavior or use smart pointers/containers; remove defeated monsters from the collection consistently. |
