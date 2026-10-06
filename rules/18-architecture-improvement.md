# 18. Architecture improvement (deepening scan)

Scan for shallow modules and deepen them:

| Friction signal                                               | Deepening opportunity                      |
| ------------------------------------------------------------- | ------------------------------------------ |
| Understanding requires bouncing between many small modules    | Merge into a deeper module                 |
| Interface nearly as complex as implementation                 | Hide complexity behind a smaller interface |
| Pure functions extracted for testability, bugs in call chains | Restore locality                           |
| Tightly-coupled modules leak across seams                     | Redraw the seam; add an adapter            |
| Untested or hard to test through current interface            | Redesign the interface for testability     |

Apply the **deletion test** (section 14) to suspected shallow modules.