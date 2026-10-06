# 13. Code review — two axes

Review **Standards** and **Spec** separately — never merge them. A change can be clean code that builds the wrong thing.

**Standards axis (Fowler smell baseline):**

| Smell                  | Signal                                      | Fix                                    |
| ---------------------- | ------------------------------------------- | -------------------------------------- |
| Mysterious Name        | Name doesn't reveal purpose                 | Rename; if impossible, design is murky |
| Duplicated Code        | Same logic in multiple hunks                | Extract shared shape                   |
| Feature Envy           | Method reaches into another's data          | Move method to the data                |
| Data Clumps            | Same fields travel together                 | Bundle into a type                     |
| Primitive Obsession    | Primitive stands for a domain concept       | Give the concept its own type          |
| Repeated Switches      | Same switch on same type recurs             | Polymorphism or shared map             |
| Shotgun Surgery        | One change → edits across many files        | Gather into one module                 |
| Speculative Generality | Abstraction for needs the spec doesn't have | Delete; inline back                    |
| Middle Man             | Class just delegates                        | Cut it; call target direct             |

**Rule:** documented repo standard **always overrides** baseline smell.

**Spec axis:** requirements missing/partial · scope creep (behavior not asked for) · implemented but wrong (quote the spec line).

**Output:** two separate reports under `## Standards` and `## Spec`.