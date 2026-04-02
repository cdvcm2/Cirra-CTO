# CURSOR OUTPUT FORMAT
Every Cursor session must return output in this exact 
format. If a sub-CTO receives output that does not match 
this format, they must ask Cursor to re-run with this 
format before reporting anything to Clement.

SUMMARY
[one line — what was done]

TASK
[one line — what was attempted]

FILES CREATED
[list or NONE]

FILES MODIFIED
[list or NONE]

FILES DELETED
[list or NONE]

CHECKS RUN
[tsc / build / lint results]

SCHEMA CHANGES
[tables created, altered, dropped — or NONE]

MIGRATION APPLIED
[yes / no / not applicable]

COMMITTED
[yes — hash / no]

PUSHED
[yes / no]

MERGE CONFLICTS
[yes — details / no]

BUILD STATUS
[green / failing — error details if failing]

OPEN ISSUES
[anything incomplete, broken, or needing follow-up]

If Cursor output is missing this format, paste this 
reminder into Cursor before the next task:

"After completing this task return your output in 
this exact format:
SUMMARY / TASK / FILES CREATED / FILES MODIFIED / 
FILES DELETED / CHECKS RUN / SCHEMA CHANGES / 
MIGRATION APPLIED / COMMITTED / PUSHED / 
MERGE CONFLICTS / BUILD STATUS / OPEN ISSUES"

---
